---
published: true
title: "[Flutter][번역] Flutter: Push, Pop, Push - 진행중
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
> Original Source : <https://medium.com/flutter-community/flutter-push-pop-push-1bb718b13c31>


# Flutter: Push, Pop, Push

![](https://miro.medium.com/max/1024/1*7banZjCh-rGamKT-9jqADA.jpeg)

Framework가 제공하는 모든 위젯을 통해 Flutter에서 UI를 구축하는 것은 매우 간단합니다. 마지막 기사에서 다루었습니다.
그러나 우리는 아무 기능도 하지 않는다면 아름다운 응용 프로그램을 가질 수 없습니다. 애플리케이션 화면을 이동하거나 화면간에 데이터를 주고 받아야합니다. Flutter에서는 [Navigator](https://docs.flutter.io/flutter/widgets/Navigator-class.html)를 이용하여 한 화면에서 다른 화면으로 이동할 수 있습니다.

그리고 Navigator를 `Route`를 쌓아두는 간단한 Widget입니다.

새 화면으로 푸시하거나 현재 화면에서 팝업하는 방법을 알려주는 포스트는 이미 많이 있지만 이 포스트는 그 이상으로 대부분의 네비게이터 방법에 중점을두고 각 방법의 사용 사례를 설명합니다.

## Before we begin…

앞에서 `Route`에 대해서 언급하였는데 그것은 정확하게 무엇일까요?

`Route`는 앱의 화면이나 페이지에 대한 추상화입니다.
예를 들어 '/home'은 HomeScreen으로, '/login'은 LoginScreen으로 안내한다. '/'가 가장 첫 번째 경로일 것이다. 이것은 REST API 의 Routing과 매우 유사합니다. 그래서 '/'는 Root처럼 동작합니다.

아래 예제는 Flutter 응용 프로그램에서 경로를 선언하는 방법입니다.

~~~dart
new MaterialApp(
  home: new Screen1(),
  routes: <String, WidgetBuilder> {
    '/screen1': (BuildContext context) => new Screen1(),
    '/screen2' : (BuildContext context) => new Screen2(),
    '/screen3' : (BuildContext context) => new Screen3(),
    '/screen4' : (BuildContext context) => new Screen4()
  },
)
~~~

Screen1 (), Screen2 () 등은 각 화면의 클래스 이름입니다.

## Push, push, push.

데이터 구조에 대한 지식이 있으면 스택에 대해 이미 알고 있을 것 입니다. 그리고 스택에 대한 기본 지식이 있다면 Push 및 Pop에 대해 알고 있을 것입니다.

만약 위 사전 지식을 모를 경우 간략히 설명 드리면

- Push는 스택 맨 위에 요소를 추가
- Pop은 스택 맨 위에 요소를 제거

Flutter의 경우 다른 화면으로 이동할 때 push 메소드를 사용히여 Navigator 위젯의 스택 상단에 새 화면을 추가합니다. 당연히 pop 메소드는 해당 스크린을 스택에서 제거합니다.

이제 [샘플 프로젝트의 코드](https://github.com/PoojaB26/NavigatorsDemo-Flutter)로 이동하여 화면 1에서 화면 2로 이동할 수 있는 방법을 살펴보십시오. 샘플 앱을 실행하면 그 방법을 실험할 수 있습니다.

~~~dart
new RaisedButton(
   onPressed:(){
   Navigator.of(context).pushNamed('/screen2');
},
   child: new Text("Push to Screen 2"),
),
~~~


`pushNamed` 메소드를 사용하여 main.dart에 경로가 정의 된 화면으로 이동할 수 있습니다. 우리는 이 방법을 `namedRoute`라고 부릅니다. 이 방법의 사용 사례는 매우 간단합니다.

![image](https://miro.medium.com/max/94/1*RKtC1MKJbjSfMjUlR-2K7g.png){: style="float: left"}

## Pop it

Now when we want to get rid of the last visited screen, which is Screen2 in this case, we would need to pop Routes from the Navigator’s stack using the pop methods.