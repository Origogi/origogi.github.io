---
published: true
title: "[Flutter][번역] Flutter internals -  진행중"
excerpt : " "
layout: single
author_profile: true
read_time: false # read_time을 출력할지 여부 1min read 같은것!
toc: true #Table Of Contents 목차 보여줌
toc_label: "My Table of Contents" # toc 이름 정의
toc_icon: "cog" #font Awesome아이콘으로 toc 아이콘 설정
toc_sticky: true # 스크롤 내릴때 같이 내려가는 목차
categories :
 - Flutter
tags: 
  - Flutter
  - Dart
comments: true
header:
  overlay_image: assets/images/flutter_dart.png
  overlay_filter: 0.5
  teaser: assets/images/flutter-header.png
---
> Original Source : <https://www.didierboelens.com/2019/09/flutter-internals>
> <br>Author : 

# Flutter Internals

내부적으로는 Flutter가 어떻게 작동할까?

Widgets, Elements, BuildContext, RenderOject, Bindings이란 무엇일까..??

난이도: _초급_

## Introduction

작년에 매력적인 Flutter 세계로 여행을 시작했을 때 오늘날의 문서의 양을 비교하자면 인터넷에서 문서를 거의 찾을 수 없었습니다.<br>
그리고 현재 많은 문서들이 작성 되었음에도 불구하고 Flutter가 내부적으로 어떻게 작동하는지에 대해서는 거의 다루지 않았습니다.

마지막으로 Widget, Elements, Build Context란 무엇인지? Flutter가 빠른 이유는? 그리고 왜 때때로 당신의 예상과 다르게 작동하는가? Tree란 무엇인지?

만약 당신이 App을 개발할 때, Widget이 어떤 걸 표시하고 화면과 어떻게 상호작용하는지만 관심을 가질 것입니다. 
하지만 당신은 Widget이 실제로 어떻게 작동하는지 궁금하지 않으셨습니까? 시스템은 화면이 언제 업데이트 되는 지와 업데이트 해야 하는 부분을 어떻게 알 수 있습니까?

## 1부: 사전 지식
이 글의 첫 부분은 이 게시물의 두 번째 부분을 더 잘 이해하기 위해 몇 가지 핵심 개념을 소개합니다.

### 기기로 돌아 가기
일단 기본부터 생각해 봅시다.

만약 당신이 기기에서 app을 실행하게 되면 기기에서 실행중인 app은 화면으로 표시가 됩니다.

실제로 표시되는 모든 것은 일련의 픽셀이며, 이 픽셀들을 통해 평면 이미지 (2 차원)를 구성하고, 만약 손가락으로 화면을 터치하게 되면 기기는 화면을 통해 손가락의 위치를 인식합니다.

아래와 같은 상호작용을 기반으로 기기는 평면 이미지를 업데이트함으로써 사용자에게 마법같은 효과(시각적으로)를 경험하게 됩니다.

- the device screen (e.g. finger on the glass)
- the network (e.g. communication with a server)
- the time (e.g. animations)
- other external sensors

화면의 이미지 렌더링은 하드웨어 (디스플레이 장치)에 의해 보장되며, 하드웨어 (디스플레이 장치)는 일정한 간격 (일반적으로 초당 60 회)으로 디스플레이를 새로 고칩니다. 이 새로 고침 빈도는 "새로 고침 빈도"라고도하며 Hz (Hertz)로 표시됩니다.

디스플레이 장치는 GPU(Graphics Processing Unit)로부터 화면에 표시할 Data를 수신받으며, 일부 Model(폴리곤과 텍스처)를 신속하게 영상으로 생성하도록 최적화 및 설계되어 있다. 그리고  GPU가 표시할 "이미지"(Frame Buffer)를 생성하여 하드웨어에 전송할 수 있는 초당 횟수를 `Frame Rate` 이라고 한다. 이는 fps 단위로(예: 초당 60 frame 또는 60fps)으로 측정합니다..

여기서 독자는 이 article에서  GPU/하드웨어와 물리적 유리센서에 의해 렌더링된 2차원 평면 이미지의 개념을 왜 다루는 지, 그리고 Flutter Widgets와의 관계는 무엇인지 궁금해 할 것입니다.

Flutter 애플리케이션의 주요 목표 중 하나가 2차원 평면 이미지를 구성하고 상호작용하는 것이기 때문에, 나는 그 관점에서 본다면 Flutter가 실제로 어떻게 작동하는지 이해하는 것이 더 쉬울 수 있다고 생각합니다.

### Code와 물리적 장치 사이의 Interface

Flutter에 관심있는 사람이라면 아래 Flutter high-level architecture를 묘사한 그림을 본 적이 있을 것입니다.

![](https://www.didierboelens.com/images/internals_archi_overview.png)

Dart를 사용하여 Flutter app을 작성할 때 Flutter Framework 수준 (녹색)으로 유지됩니다.

Flutter Framework는 Window라는 추상화 계층을 통해 Flutter Engine (파란색)과 상호 작용합니다. 이 추상화 계층은 일련의 API를 노출하여 장치와 간접적으로 통신합니다.

아래 경우에 대해서 Fluttter Engine은 Flutter Framework로 추상화 계층을 통해 이벤트를 전달한다.

 - 기기 레벨에서 특정 이벤트가 발생함(방향 변경, 설정 변경, 메모리 문제, 애플리케이션 실행 상태 등)
- Grass 레벨에서 일부 이벤트가 발생함(= 제스처)
- 플랫폼 채널에서 일부 데이터 전송
- _하지만 무엇보다도 플로터 엔진이 새 프레임을 렌더링할 준비가 되었을 때_

### Flutter Framework는 Flutter Engine 프레임 렌더링에 의해 구동됨


이 항목은 믿기 어렵지만 사실입니다.

아래 경우를 제외하고 Flutter Engine Frame Renderring에 의해 트리거되지 않고 Flutter Framework 코드가 실행되지 않습니다.

- Gesture (an event on the glass)
- Platform messages (messages that are emitted by the device, e.g. GPS)
- Device messages ( messages that refer to a variation to the device state, e.g. orientation, application sent to background, memory warnings, device settings…)
- Future or http responses

> Flutter Framework는 Flutter Engine Frame Renderring의 요청이 없으면 시각적 변화를 적용하지 않습니다.

(Flutter Engine에 의해 트리거되지 않고 시각적 변화를 적용하는 것은 가능하지만 실제로 그렇게하지 않는 것이 좋습니다)

그러나 제스처와 관련된 일부 코드가 실행되어 시각적 변경을 하거나 또는 애니매이션 동작하는 경우 어떻게 내부적으로 동작하는 지 당신은 궁금해 할 것입니다.

> 만약 당신이 시각적 변경을 원할 경우 Flutter Engine에 무언가를 렌더링해야한다고 알려 주어야합니다.
그 다음 Refresh할 때, Flutter Engine은 Flutter Framework가 코드를 실행하도록 요청하고 마침내 렌더링 할 새로운 장면을 제공합니다.

그리고 가장 큰 문제는 Flutter Engine이 렌더링을 기반으로 전체 애플리케이션 동작을 어떻게 조정 하는가입니다.

내부 매커니즘을 살펴보기 위해 아래 애니메이션을 참고 바랍니다.

![](https://www.didierboelens.com/images/internals_flow.gif)


 1. 일부 외부 이벤트(Gesture, http response, ...) 또는 Future 도 Rendering 업데이트 하기 위한  몇 가지 작업을 시작할 수 있습니다. 메시지를 Flutter Engine 에 전송하여 알려줍니다( = Schedule Frame)
 2. Flutter Engine이 렌더링 업데이트를 진행할 준비가 되면 프레임 시작 요청을 보냅니다.
 3. 이 프레임 시작 요청은 Flutter Framework에서 가로 채서 티커와 주로 관련된 모든 작업 (예 : 애니메이션)을 실행합니다.
These tasks could re-emit a request for a later frame rendering… (example: an animation is not complete and to move forward, it will need to receive another Begin frame at a later stage)
 - Then, the Flutter Engine emits a Draw Frame.
This Draw Frame is intercepted by the Flutter Framework, which will look for any tasks linked to updating the layout in terms of structure and size.
- Once all these tasks are done, it goes on with the tasks related to updating the layout in terms of painting.
- If there is something to be drawn on the screen, it then sends the new Scene to be rendered to the Flutter Engine which will update the screen.
 - Then, the Flutter Framework executes all tasks to be run after the rendering is complete (= PostFrame callbacks) and any other sub-sequent other tasks not rendering related.
 - … and this flow starts again and again.

### RenderView and RenderObject
Before going into the details related to the flow of actions, it is the right time to introduce the notion of Rendering Tree.

As previously said, everything eventually turns out to become a series of pixels to be displayed on the screen and the Flutter Framework converts the Widgets we are using to develop the application into visual parts which will be rendered on the screen.

These visual parts which are rendered on the screen correspond to objects, called RenderObjects, which are used to:

 - define some area of the screen in terms of dimensions, position, geometry but also in terms of “rendered content“
 - identify zones of the screen potentially impacted by the gestures (= finger)
The set of all the RenderObject forms a tree, called Render Tree. At the top of that tree (= root), we find a RenderView.

The RenderView represents the total output surface of the Render Tree and is itself a special version of a RenderObject.

Visually speaking we could represent all this as follows:

![](https://www.didierboelens.com/images/internals_renderView.png)

The relationship between Widgets and RenderObjects will be discussed later in this article.

It is now time to go a bit deeper…

First things first - initialization of the bindings
When you start a Flutter application, the system invokes the main() method which will eventually call the runApp(Widget app) method.

During that call to the runApp() method, Flutter Framework initializes the interfaces between the Flutter Framework and the Flutter Engine. These interfaces are called bindings.

The Bindings - Introduction
The bindings are meant to be some kind of glue between the Flutter Engine and the Flutter Framework. It is only through these bindings that data can be exchanged between the two Flutter parts (Engine and Framework).
(There is only one exception to this rule: the RenderView but we will see this later).

Each Binding is responsible for handling a set of specific tasks, actions, events, regrouped by domain of activities.

### At time of writing this article, Flutter Framework counts 8 bindings.

Below, the 4 ones that will be discussed in this article:

- SchedulerBinding
- GestureBinding
- RendererBinding
- WidgetsBinding
- For sake of completeness, the last 4 ones (which will not be addressed in this article):

- ServicesBinding: responsible for handling messages sent by the platform channel
- PaintingBinding: responsible for handling the image cache
- SemanticsBinding: reserved for later implementation of everything related to Semantics
- TestWidgetsFlutterBinding: used by widgets tests library
I could also mention the WidgetsFlutterBinding but the latter is not really a binding but rather some kind of “binding initializer”.

The following diagram shows the interactions between the bindings I am going to cover a bit later in this article and the Flutter Engine.

![](https://www.didierboelens.com/images/internals_bindings.png)