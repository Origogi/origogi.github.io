---
published: true
title: "[Flutter][번역] Flutter: Push, Pop, Push - 진행중"
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
  - 번역
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

![image](https://miro.medium.com/max/94/1*RKtC1MKJbjSfMjUlR-2K7g.png)

## Pop it

이제 마지막 방문한 화면 (이 경우 Screen2)을 제거하려면 `pop` 메소드를 사용하여 네비게이터의 스택에서 Route를 Pop해야합니다.

~~~dart
Navigator.of(context).pop();
~~~

위 코드는 `onPressed`
 메서드 안에 들어갑니다.


![image](https://miro.medium.com/max/93/1*hq7qfAer0wCCSyIBKr7sfg.png)

Scaffold를 사용할 때는 일반적으로 명시적으로 Route를 pop할 필요가 없습니다. 왜냐하면 Scaffold는 자동으로 ApApar에 `back` 버튼을 추가하기 때문입니다. `back` 버튼을 누르면 Navigator.pop ()을 호출합니다. 
Android에서도 기기의 back 버튼을 누르면 같은 방식으로 작동합니다.
그럼에도 불구하고 사용자가 취소 버튼을 클릭 할 때 AlertDialog를 팝업하는 것과 같은 유스 케이스에는 이 방법이 필요할 수 있습니다.

### Why pop instead of pushing back to the previous screen?

원하는 위치에 호텔을 나열하는 호텔 예약 앱이 있다고 가정합니다. 
목록에서 하나의 항목을 클릭하면 호텔에 대한 자세한 정보가 있는 화면으로 이동합니다. 선택한 호텔이 마음에 들지 않다 다시 목록으로 돌아가고 싶습니다.
Stack에 HotelListScreen 을 push하면 DetailsScreen은 Stack에 계속 유지가 됩니다. 따라서 back 버튼을 누르면 다시 DetailsScreen 로 돌아가게 됩니다. 이것은 매우 혼란스럽습니다.

샘플 앱을 실행하고 Screen1의 appBar를 확인하십시오. 이것은 초기 Route 이기 때문에 back 버튼이 없습니다. 
이제 back 버튼 대신 `Push to Screen 2` 버튼을 누룹니다. 그 다음 `Push to Screen1 instead of Pop` 버튼을 누른 후 이제 Screen1의 appBar를 확인 하십시오. 새로 표시된 back 버튼을 누르면 Screen2로 돌아갈 수 있습니다.

![](https://miro.medium.com/max/92/1*Xsyo5c8s1JwO6f2OQ1nNEg.png)


### maybePop

만약 초기 Route 인 상태에서 실수로 Screen을 pop을 하게 되면 어떻게 될까요? 스택에 있는 유일한 screen을 pop하게 되면 app은 종료가 됩니다. 그 이유는 표시할 Route가 없기 때문입니다. 사용자가 이러한 경험을 원하지 않을수도 있습니다.

이를 방지하기 위해 maybePop ()이 등장하게 됩니다. 
Screen1 에서 `maybePop` 버튼을 클릭하게 되면 아무 것도 하지 않습니다. 왜냐하면 pop를 할 것이 없기 때문입니다. 하지만 Screen3에서 같은 버튼을 누르게 되면 pop이 될 것입니다. 왜냐하면 그것을 할 수 있는 상태이기 때문입니다.

### canPop

위 동작을 수행 할 수 있다는 것은 놀라운 일이지만 이것이 초기 Route 인지 어떻게 알 수 있습니까? 그러한 경우 사용자에게 경고를 표시 할 수 있다면 좋을 것입니다.

좋은 질문입니다.  `canPop ()` 메서드를 호출하면 Stack에 Route를 pop 할수 있으면 true를 반환하고 가능하지 않으면 false를 반환합니다.

두가지 방법 모두 Sreen 1과 Screen 3의 `canPop` 및 `maybePop`을 사용해 보고 차이점을 확인하십시오. canPop의 return 값이 IDE의 콘솔 탭에 표시됩니다.