---
published: false
title: "[Flutter][번역] Why use RxDart and how we can use with BLoC Pattern in Flutter?"
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
> Original Source : <https://medium.com/flutter-community/why-use-rxdart-and-how-we-can-use-with-bloc-pattern-in-flutter-a64ca2c7c52d>



# Why use RxDart and how we can use with BLoC Pattern in Flutter?

![](https://miro.medium.com/max/606/1*YY94QoK1UxPas487Ji615A.png)

다시 만나 반갑습니다! 혹시 Reactive 프로그래밍에 대해 들어 보신적이 있습니까? RxDart는 ReactiveX 기반의 Dart 언어 용 Reactive 프로그래밍 라이브러리입니다. Dart는 Streams와 함께 작동하기에 알맞은 패키지를 이미 가지고 있지만 RxDart는 그 위에 기능을 추가합니다. 여기서 질문을 드리자면, Stream이란 무엇입니까?

## Streams과 Sinks

Stream은 데이터와 이벤트의 흐름을 나타냅니다. 그렇다면 가장 중요한 것은 무엇입니까? Stream을 사용하면 데이터 및 이벤트 변경 사항을 들을 수 있으며 스트림에서 오는 내용을 Listener와 처리 할 수 ​​있습니다. Flutter에서는 어떻게 적용 할 수 있을까요? 예를 들어, Stream과의 interaction에 대한 최신 Snapshot을 기반으로 자체 빌드하는 StreamBuilder라는 Flutter의 위젯이 있습니다. 그리고, 새로운 데이터 흐름이있는 경우 새 데이터를 처리하기 위해 위젯이 새로 고침됩니다.

[Flutter Dev Channel의 Widget Weekly](https://youtu.be/MkKEWHfy99Y)는 StreamBuilder 작동 방식에 대한 훌륭한 내용을 제공합니다.

그리고 Sinks란 무엇일까요? 만약 우리가 데이터 플럭스의 출력을 가지고 있다면, 우리는 또한 입력이 필요힙니다. 그것이 Sinks가 사용되는 것입니다. 간단해 보이지 않습니까? 이제 BLoC 패턴에 대해 살펴보고, 어떻게 이 두 개념을 멋진 Flutter 앱으로 결합할 수 있는지 알아봅시다.

![dd](https://miro.medium.com/max/600/1*ccPKTUTwZtt8HADzCtBLwA.gif)
Stream of Cats 😁

## BLoC Pattern
