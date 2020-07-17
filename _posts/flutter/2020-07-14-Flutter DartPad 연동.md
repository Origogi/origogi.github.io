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

1. [Github Gist](https://gist.github.com/) 에 접속하여 내가 작성한 코드를 body에 복사/붙여넣기를 합니다.

2. filename를 입력할 때 확장자를 `.dart`를 붙입니다.

## 3. DartPad 를 Web에 Embedded


### Sample

<iframe src="https://dartpad.dev/embed-flutter.html?id=5eaa69dfeba45bdc3d05b715d16ddc5f" style="width:100%; height:700px"></iframe>
