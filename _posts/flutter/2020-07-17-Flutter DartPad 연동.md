---
published: true
title: "[Flutter] Dart Pad를 이용하여 내가 만든 Flutter App을 공유하기"
excerpt : " "
layout: single
classes: wide
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
  teaser: 
sitemap :
  changefreq : daily
  priority : 1.0
---

![header](https://miro.medium.com/max/1920/1*2adBDBgzNounV9WhluUh5Q.png)

## 1. Overview

안녕하세요 이 포스트에서 DartPad를 이용하여 내가 만든 Flutter app를 다른 사람에게 공유하는 방법을 다루도록 하겠습니다.

### DartPad

DartPad는 SDK나 IDE 설치 없이도 Dart 및 Flutter 코드를 Web browser 환경에서 실행할 수 있는 무료 오픈 소스 온라인 편집기입니다.
  
[dartpad.dev](https://dartpad.dev/)에 접속하여 Dart 코드 뿐만 아니라 Flutter App도 실행이 가능합니다.

## 2. DartPad Link Url 생성

dartpad는 기본적으로 내가 작성한 코드를 저장하지 않기 때문에 dartpad에 바로 작성한 코드를 다른 사람에게 바로 공유를 할 수 없습니다.
  
이를 위해 `Github Gist`의 도움이 필요합니다.

### Github Gist

[Github Gist](https://gist.github.com/)란 간단한 메모장같은 것으로 Code Snippeset이나 로그, 메모등을 남기는데 사용합니다.

### Link Url 생성 과정

- [Github Gist](https://gist.github.com/) 에 접속하여 내가 작성한 코드를 body에 복사/붙여넣기를 합니다.

- filename를 입력할 때 확장자를 `.dart`를 붙입니다.

<div align="center">
  <img src="https://user-images.githubusercontent.com/35194820/87750653-002e1d00-c837-11ea-9ddd-37f2d9167e10.png" >
</div>

- `Create public gist` 버튼을 누릅니다.

- Gist를 생성하면 아래와 같이 주소창에 id가 생성되었음을 확인할 수 있습니다.
</br>

<div align="center">
  <img src="https://user-images.githubusercontent.com/35194820/87751106-3c15b200-c838-11ea-937d-38b7393e032c.png" >
</div>

- Web browser에 아래와 같이 주소창을 입력하여 접속 되는 지 확인합니다.

~~~dart
https://dartpad.dev/[id]
~~~

<div align="center">
  <img src="https://user-images.githubusercontent.com/35194820/87751283-a9294780-c838-11ea-99ab-33aa4829e438.png" >
</div>

- 마지막으로 접속도 잘되고 실행도 잘되는 것을 확인하셨으면 동료 개발자나 친구들에게 메신저로 위 링크를 보내주며 자랑을 해봅시다!


<div align="center">
  <img src="https://media.giphy.com/media/lArQw5Z7Ejb0c/giphy.gif" width="300" >
</div>

참고로 위에서 제가 만든 Sample은 다음과 같습니다.
- [DartPad](https://dartpad.dev/5eaa69dfeba45bdc3d05b715d16ddc5f)

- [Gist](https://gist.github.com/Origogi/5eaa69dfeba45bdc3d05b715d16ddc5f)

## 3. DartPad 를 Web에 Embedded

- DartPad 같은 경우 Web page에 임베디드도 제공하고 있습니다. html이나 markdown 파일에 아래 코드를 삽입을 합니다.

~~~html
<iframe src="https://dartpad.dev/embed-flutter.html?id=5eaa69dfeba45bdc3d05b715d16ddc5f" style="width:100%; height:700px"></iframe>
~~~

- 위에서 `id` 값을 내가 만든 `gist id`를 바꿔줍니다.
- `style` 값을 내 화면에 맞게 수정을 합니다.

그러면 아래와 같이 임베디드된 dartpad가 화면에 출력이 됩니다.

<iframe src="https://dartpad.dev/embed-flutter.html?id=5eaa69dfeba45bdc3d05b715d16ddc5f" style="width:100%; height:700px"></iframe>

## 4. 결론

DartPad을 이용하면 IDE, SDK 없이 앱을 실행하고 실시간으로 수정함으로써 추후에 기획자나 디자이너와 커뮤니케이션을 할 때 매우 유용할 것이라 생각이 됩니다.
무엇보다도 사용 방법이 매우 쉬어서 저에게 매우 매력적으로 다가온 거 같습니다. 

그러니 여러분도 한번 사용 해보시는 것을 강력 추천 드립니다.

## Reference

- https://github.com/dart-lang/dart-pad