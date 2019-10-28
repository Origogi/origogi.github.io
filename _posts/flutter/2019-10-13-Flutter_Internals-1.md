---
published: true
title: "[Flutter][번역] Flutter internals (1)"
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
  teaser: https://venturebeat.com/wp-content/uploads/2019/09/google-dart-flutter.png?w=578&strip=all
sitemap :
  changefreq : daily
  priority : 1.0
---
> Original Source : <https://www.didierboelens.com/2019/09/flutter-internals>
> <br>Author : 

# Flutter Internals

내부적으로는 Flutter가 어떻게 작동할까?

Widgets, Elements, BuildContext, RenderOject, binding이란 무엇일까..??

난이도: _초급_

## Introduction

작년에 매력적인 Flutter 세계로 여행을 시작했을 때 오늘날의 문서의 양을 비교하자면 인터넷에서 문서를 거의 찾을 수 없었습니다.<br>
그리고 현재 많은 문서들이 작성 되었음에도 불구하고 Flutter가 내부적으로 어떻게 작동하는지에 대해서는 거의 다루지 않았습니다.

마지막으로 Widget, Elements, Build Context란 무엇인지? Flutter가 빠른 이유는? 그리고 왜 때때로 당신의 예상과 다르게 작동하는가? Tree란 무엇인지?

만약 당신이 App을 개발할 때, Widget이 어떤 걸 표시하고 화면과 어떻게 상호작용하는지만 관심을 가질 것입니다. 
하지만 당신은 Widget이 실제로 어떻게 작동하는지 궁금하지 않으셨습니까? 시스템은 화면이 언제 업데이트 되는 지와 업데이트 해야 하는 부분을 어떻게 알 수 있습니까?

## 1부: 사전 지식

이 글의 첫 부분은 이 게시물의 두 번째 부분을 더 잘 이해하기 위해 몇 가지 핵심 개념을 소개합니다.

### Device로 돌아 가기

일단 기본부터 생각해 봅시다.

만약 Device에서 app을 실행하게 되면 Device에서 실행중인 app은 화면으로 표시가 됩니다.

실제로 표시되는 모든 것은 일련의 픽셀이며, 이 픽셀들을 통해 평면 이미지 (2 차원)를 구성하고, 만약 손가락으로 화면을 터치하게 되면 Device는 화면을 통해 손가락의 위치를 인식합니다.

아래와 같은 상호작용을 기반으로 Device는 평면 이미지를 업데이트함으로써 사용자에게 마법같은 효과(시각적으로)를 경험하게 됩니다.

- Device screen (e.g. finger on the glass)
- Network (e.g. communication with a server)
- Time (e.g. animations)
- Other external sensors

화면의 이미지 Rendering은 하드웨어 (디스플레이 장치)에 의해 보장되며, 하드웨어 는 일정한 간격 (일반적으로 초당 60 회)으로 디스플레이를 새로 고칩니다. 이 새로 고침 빈도는 "새로 고침 빈도"라고도하며 Hz (Hertz)로 표시됩니다.

디스플레이 장치는 GPU(Graphics Processing Unit)로부터 화면에 표시할 Data를 수신받으며, 일부 Model(폴리곤과 텍스처)를 신속하게 영상으로 생성하도록 최적화 및 설계되어 있습니다. <br>그리고  GPU가 표시할 "이미지"(Frame Buffer)를 생성하여 하드웨어에 전송할 수 있는 초당 횟수를 `Frame Rate` 이라고 합니다. 이는 fps 단위로(예: 초당 60 frame 또는 60fps)으로 측정합니다..

이 article에서  GPU/하드웨어와 물리적 유리센서에 의해 Rendering된 2차원 평면 이미지의 개념을 왜 다루는 지, 그리고 Flutter Widgets와의 관계는 무엇인지 궁금해 할 것입니다.

Flutter 애플리케이션의 주요 목표 중 하나가 2차원 평면 이미지를 구성하고 상호작용하는 것이기 때문에, 나는 그 관점에서 본다면 Flutter가 실제로 어떻게 작동하는지 이해하는 것이 더 쉬울 수 있다고 생각합니다.

### Code와 물리적 장치 사이의 Interface

Flutter에 관심있는 사람이라면 아래 Flutter high-level architecture를 묘사한 그림을 본 적이 있을 것입니다.

![](https://www.didierboelens.com/images/internals_archi_overview.png)

Dart를 사용하여 Flutter app을 작성할 때 Flutter Framework 수준 (녹색)으로 유지됩니다.

Flutter Framework는 Window라는 추상화 계층을 통해 Flutter Engine (파란색)과 상호 작용합니다. 이 추상화 계층은 일련의 API를 노출하여 장치와 간접적으로 통신합니다.

아래 경우에 대해서 Fluttter Engine은 Flutter Framework로 추상화 계층을 통해 이벤트를 전달한다.

- Device 레벨에서 특정 이벤트가 발생함(방향 변경, 설정 변경, 메모리 문제, 애플리케이션 실행 상태 등)
- Grass 레벨에서 일부 이벤트가 발생함(= Gesture)
- 플랫폼 채널에서 일부 데이터 전송
- _하지만 무엇보다도 플로터 Engine이 새 프레임을 Rendering할 준비가 되었을 때_

### Flutter Framework는 Flutter Engine 프레임 Rendering에 의해 구동됨

이 항목은 믿기 어렵지만 사실입니다.

아래 경우를 제외하고 Flutter Engine Frame Rendering에 의해 트리거되지 않고 Flutter Framework 코드가 실행되지 않습니다.

- Gesture (an event on the glass)
- Platform messages (messages that are emitted by the device, e.g. GPS)
- Device messages ( messages that refer to a variation to the device state, e.g. orientation, application sent to background, memory warnings, device settings…)
- Future or http responses

> Flutter Framework는 Flutter Engine Frame Rendering의 요청이 없으면 시각적 변화를 적용하지 않습니다.

(Flutter Engine에 의해 트리거되지 않고 시각적 변화를 적용하는 것은 가능하지만 실제로 그렇게하지 않는 것이 좋습니다)

Gesture와 관련된 일부 코드가 실행되어 시각적 변경을 하거나 또는 애니매이션 동작하는 경우 어떻게 내부적으로 동작하는 지 당신은 궁금해 할 것입니다.

> 만약 당신이 시각적 변경을 원할 경우 Flutter Engine에 무언가를 Rendering해야한다고 알려 주어야합니다.
그 다음 Refresh할 때, Flutter Engine은 Flutter Framework가 코드를 실행하도록 요청하고 Rendering 할 새로운 장면을 제공합니다.

그리고 가장 큰 문제는 Flutter Engine이 Rendering을 기반으로 전체 애플리케이션 동작을 어떻게 조정 하는가입니다.

내부 매커니즘을 살펴보기 위해 아래 애니메이션을 참고 바랍니다.

![](https://www.didierboelens.com/images/internals_flow.gif)

 1. 일부 외부 이벤트(Gesture, http response, ...) 또는 Future 도 Rendering 업데이트 하기 위한  몇 가지 작업을 시작할 수 있습니다. 메시지를 Flutter Engine 에 전송하여 알려줍니다( = Schedule Frame)
 2. Flutter Engine이 Rendering 업데이트를 진행할 준비가 되면 `Begin Frame` 요청을 보냅니다.
 3. Flutter Framework은 `Begin Frame` 요청을 가로 채서 Tickers와 관련된 작업 (예 : 애니메이션)을 실행합니다.
이 작업 이후 Frame Redering에 대한 요청을 다시 전송할 수 있습니다… (예 : 애니메이션이 완료되지 않고 계속 진행할려면 나중에 다른 Frame 시작 요청을 받아야합니다)
4. 그런 다음 Flutter Engine에서 Draw Frame을 내 보냅니다.
5. 이 Draw Frame은 Flutter Framework에서 가로 채서 structure, size 측면에서 레이아웃 업데이트와 관련된 작업을 찾습니다.
6.  이 모든 작업이 완료되면 painting 측면에서 레이아웃 업데이트와 관련된 작업을 계속 진행한다.
7. 화면에 그려야 할 것이 있으면 새 Scean을 Flutter Engine으로 Rendering 하여 화면을 업데이트합니다.
8. Flutter Framework 는 렌더링이 완료된 후 (= PostFrame 콜백 ) 렌더링과 관련이없는 다른 후속 작업 이 실행 된 후 실행할 모든 작업을 실행합니다 .
9. 그리고 위 순서를 다시 반복을 합니다.

### RenderView 및 RenderObject

동작 흐름과 관련된 세부 사항을 살펴보기 전에 Rendering tree 개념을 알아볼 차례입니다.

앞에서 언급했듯이 모든 것은 화면에 표시되는 일련의 픽셀이되고 Flutter Framework는 응용 프로그램을 개발하는 데 사용하는 Widget을 화면에 Rendering하여 시각적 부분으로 변환시킵니다.

화면에 Rendering되는 이러한 시각적 부분은 `RenderObject`라고 불리며, 다음과 같은 경우에 사용이 됩니다.

 - 화면의 일부 영역을 치수, 위치, 기하학적 측면뿐만 아니라 `rendered content` 측면에서도 정의한다.
 - Gesture에 의해 영향을 받을 수 있는 화면의 영역을 식별한다. (= finger)

모든 `RenderObject`의 set는 `Render Tree`라고 불리는 Tree 를 형성합니다. 그 Tree의 Root에서 RenderView가 존재합니다..

`RenderView`는 `Render Tree`의 전체 출력되는 표면을 나타내며 그 자체가 `RenderObject`의 특수 버전입니다.

위 내용을 다음 그림으로 표현할 수 있다.

![gr](https://www.didierboelens.com/images/internals_renderView.png)

그리고 `Widget`과 `RenderObjects`의 관계는 이 기사의 뒷부분에서 설명합니다.

이제 조금 더 깊이 알아 봅시다. ...

### 첫번째 사항 - initialization of the binding

Flutter 애플리케이션을 시작할 때 시스템은 runApp(Widget App) 메서드를 호출합니다.

runApp() 메서드를 호출 중에 Flutter Framework는 Flutter Framework와 Flutter Engine 사이의 인터페이스를 초기화합니다. 이러한 인터페이스를 `binding`라고 합니다.

### Binding - Introduction

`binding`은 Flutter Engine과 Flutter 프레임워크 사이에 접착제 같은 것을 의미합니다. 이러한 `binding`을 통해서만 두 Flutter 모듈(Engine 및 Framework) 간에 데이터를 교환할 수 있습니다.
(RenderView가 한 가지 예외지만 나중에 이를 살펴 볼 것입니다.)

각 `binding`은 활동 category 별로 재 그룹화 된 task, action, event set를 처리합니다.

### Flutter Framework counts 8 binding.(At time of writing this article)

- SchedulerBinding
- GestureBinding
- RendererBinding
- WidgetsBinding

(이 기사에서는 위 4개에 대해서만 다룰 것입니다.)

- ServicesBinding: 플랫폼 채널에서 보낸 메시지를 처리합니다.
- PaintingBinding: image cache를 처리합니다.
- SemanticsBinding: reserved for later implementation of everything related to Semantics
- TestWidgetsFlutterBinding: widgets test library에 사용

또한 `WidgetsFlutterBinding`을 언급 할 수 있지만 실제로 `binding`이 아니라 일종의 `binding initializer` 입니다.

다음 Diagram 은 이 기사에서 나중에 다루게 될 `binding`과 Flutter Engine 간의 상호 작용을 보여줍니다.

![GR](https://www.didierboelens.com/images/internals_bindings.png)

이게 각각 주요 `binding`에 대해 알아 봅시다.

### SchedulerBinding

이 `binding`은 두 개의 주요 역할을 수행합니다.

1. Flutter Engine에 다음과 같이 알려 주는 역할입니다. 
  "안녕, 다음 번에 바쁘지 않을 때는 깨어나서 무엇을 Rendering해야 하는지 또는 나중에 언제 다시 전화 해야하는지 알려주십시오…";
2. 이러한 `모닝콜` 에 귀를 기울이고 반응하는 것 입니다.

그리면 `SchedulerBinding`은 언제 `모닝콜`을 요청하는 것일까요?

- When a Ticker needs to tick
  예를 들어, 애니메이션이 있고 시작한다고 가정합니다. 애니메이션은 `Ticker`에 의해 control되며, 주기적인 간격 (= tick)으로 callback을 실행하기 위해 호출됩니다. 이러한 callback을 실행하려면 Flutter Engine에 다음 Refresh (= Bigine Frame)시 깨우도록 지시해야합니다. 그리고 Ticker에 의해 콜백을 호출하여 작업을 수행합니다. 해당 작업이 끝날 때 Ticker가 여전히 진행되어야 하는 경우 다른 프레임을 예약하기 위해 `SchedulerBinding`을 호출합니다.

- When a change applies to the layout
  예를 들어, 시각적 변경를 일으키는 이벤트에 응답하는 경우(예: 화면의 일부 색 업데이트, 스크롤, 화면 추가/제거), 필요한 단계를 수행하여 화면에 Rendering해야 합니다. 이 경우 Flutter Framework는 `SchedulerBinding`을 호출하여 Flutter Engine으로 다른 프레임을 예약합니다. (우리는 그것이 실제로 어떻게 작동하는지 나중에 볼 것입니다)

### GestureBinding


이 `binding`은 "손가락"(= Gesture)의 관점에서 Engine과의 상호 작용을 수신합니다..
특히, 손가락과 관련된 데이터를 받아들이고 Gesture의 영향을 받는 화면의 부분을 결정해야합니다. 그 다음 그 화면의 부분에 대해 알립니다.

### RendererBinding

이 `binding`은 Flutter Engine과 Render Tree 사이의 접착제 입니다. 여기에는 두 가지 역할이 있습니다.

1. 엔진에서 발생하는 이벤트를 듣고 장치 설정을 통해 사용자가 적용한 변경 사항을 시각적/의미적 으로 나타내는 것입니다.
2. 엔진에 디스플레이에 적용 할 변경 항목을 제공하는 것입니다.

화면에 Rendering 할 변경 사항을 제공하기 위해 이 `binding`은 `PipelineOwner`를 구동하고 RenderView를 초기화합니다.

`PipelineOwner`는 레이아웃과 관련하여 어떤 `RenderObject`가 어떤 작업을 수행해야하는지 알고 이러한 작업을 조정하는 일종의 orchestra 역할을 수행합니다.

### WidgetsBinding

이 `binding`은 언어 (= locale) 및 Semantics에 영향을 주는 Device Setting 같이 사용자가 적용한 변경 사항을 수신합니다.

이 외에도 `WidgetsBinding`은 Widget과 Flutter Engine 사이의 접착제입니다. 여기에는 두 가지 역할이 있습니다.

1. Widget 구조 변경 처리를 담당하는 프로세스를 실행하는 것입니다.
2. Rendering을 트리거하는 것입니다

Widget 구조 변경 처리는 `BuildOwner`를 통해 수행됩니다.

`BuildOwner`는 Rebuild가 필요한 Widget을 추적하고 Widget 구조 전체에 적용되는 또 다른 작업을 처리합니다.
