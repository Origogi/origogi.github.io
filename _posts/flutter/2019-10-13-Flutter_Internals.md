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

The display device receives the information to be displayed on the screen from the GPU (Graphics Processing Unit), which is a specialized electronic circuit, optimized and designed to rapidly generate an image from some data (polygons and textures). The number of times per second the GPU is able to generate the “image” (=frame buffer) to be displayed and to send it to the hardware is called the frame rate. This is measured with the fps unit (e.g. 60 frames per second or 60 fps).

You will maybe ask me why did I start this article with the notions of 2-dimension flat image rendered by the GPU/hardware and the physical glass sensor… and what is the relationship with the usual Flutter Widgets?

Simply because one of the main objectives of a Flutter application is to compose that 2-dimensional flat image and to make it possible to interact with it, I think it might be easier to understand how Flutter actually works if we look at it from that perspective.

…but also because in Flutter, believe it or not, almost everything is driven by the needs of having to refresh the screen… quickly and at the right moment !

### Interface between the code and the physical device
One day or another, everyone interested in Flutter already saw the following picture which describes the Flutter high-level architecture.

![](https://www.didierboelens.com/images/internals_archi_overview.png)

When we are writing an Flutter application, using Dart, we remain at the level of the Flutter Framework (in green).

The Flutter Framework interacts with the Flutter Engine (in blue), via an abstraction layer, called Window. This abstraction layer exposes a series of APIs to communicate, indirectly, with the device.

This is also via this abstraction layer that the Flutter Engine notifies the Flutter Framework when, among others:

 - an event of interest happens at the device level (orientation change, settings changes, memory issue, application running state…)
 -some event happens at the glass level (= gesture)
 - the platform channel sends some data
- but also and mainly, when the Flutter Engine is ready to render a new frame

### Flutter Framework is driven by the Flutter Engine frame rendering
This statement is quite hard to believe, but it is the truth.

Except in some cases (see below), no Flutter Framework code is executed without having been triggered by the Flutter Engine frame rendering.

These exceptions are:

- Gesture (= an event on the glass)
- Platform messages (= messages that are emitted by the device, e.g. GPS)
- Device messages (= messages that refer to a variation to the device state, e.g. orientation, application sent to background, memory warnings, device settings…)
- Future or http responses

> The Flutter Framework will not apply any visual changes without having been requested by the Flutter Engine frame rendering.

(between us, it is however possible to apply a visual change without having been invited by the Flutter Engine, but this is really not advised to do so)

But you will ask me, if some code related to the gesture is executed and causes a visual change to happen, or if I am using a timer to rythm some task, which leads to visual changes (such as an animation, for example), how does this work then?

> If you want a visual change to happen, or if you want some code to be executed based on a timer, you need to tell the Flutter Engine that something needs to be rendered.
Usually, at next refresh, the Flutter Engine will then request the Flutter Framework to run some code and eventually provide the new scene to render.

Therefore, the big question is how does Flutter Engine orchestrate the whole application behavior, based on the rendering?

To give you a flavor of the internal mechanisms, have a look at the following animation…

![](https://www.didierboelens.com/images/internals_flow.gif)

Short explanation (further details will come later):

 - Some external events (gesture, http responses, …) or even futures, can launch some tasks which lead to having to update the rendering. A message is sent to the Flutter Engine to notify it ( = Schedule Frame)
- When the Flutter Engine is ready to proceed with the rendering update, it emits a Begin Frame request
 - This Begin Frame request is intercepted by the Flutter Framework, which runs any task mainly related to Tickers (such as Animations, e.g.)
These tasks could re-emit a request for a later frame rendering… (example: an animation is not complete and to move forward, it will need to receive another Begin frame at a later stage)
 - Then, the Flutter Engine emits a Draw Frame.
This Draw Frame is intercepted by the Flutter Framework, which will look for any tasks linked to updating the layout in terms of structure and size.
- Once all these tasks are done, it goes on with the tasks related to updating the layout in terms of painting.
- If there is something to be drawn on the screen, it then sends the new Scene to be rendered to the Flutter Engine which will update the screen.
 - Then, the Flutter Framework executes all tasks to be run after the rendering is complete (= PostFrame callbacks) and any other sub-sequent other tasks not rendering related.
 - … and this flow starts again and again.