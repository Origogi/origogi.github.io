---
published: false
title: "[Flutter] Flutter I/O 2022 요약"
excerpt: " "
layout: single
classes: wide
author_profile: true
read_time: false # read_time을 출력할지 여부 1min read 같은것!
toc: true #Table Of Contents 목차 보여줌
toc_label: "My Table of Contents" # toc 이름 정의
toc_icon: "cog" #font Awesome아이콘으로 toc 아이콘 설정
toc_sticky: true # 스크롤 내릴때 같이 내려가는 목차
categories:
  - Flutter
tags:
  - Flutter
  - Dart
  - Amplify
comments: true
header:
  overlay_image: assets/images/flutter_banner.jpeg
  overlay_filter: 0.4
  teaser:
sitemap:
  changefreq: daily
  priority: 1.0
---

<div align="center">
<img src="https://d2908q01vomqb2.cloudfront.net/7b52009b64fd0a2a49e6d8a939753077792b0554/2021/02/17/Site-Merch_AWS-Amplify-Flutter_SocialMedia_1-1024x536-1.png" >
</div>

## Overview

해당 포스트는 2022 Flutter I/O Extended Korea 2022 에서 세션을 정리해 보았습니다.

### 날짜, 위치

- 2022/06/26 (일) 14:00 - 18:00
- 서울 광진구 능동로 120 건국대학교 서울캠퍼스 산학협동관 1층

## TimeTable

- 14:00 ~ 14:20 Keynote - Flutter Korea
- 14:30 ~ 15:00 플러터 리눅스 데스크톱 + ROS2 - 박제창(엔젤로보틱스)
- 15:10 ~ 15:30 Dart 2.17 변경점, Enum 실제로 써보자! - 강태욱(휴넷)
- 15:40 ~ 16:10 멀티 모듈을 활용한 플러터 클린아키텍처 - 임태규(Presto Labs)
- 16:20 ~ 16:40 플러터로 게임 개발하기 - 강환석(라피티)
- 16:50 ~ 17:10 플러터 애니메이션 적용기 - 김선호(삼성전자)
- 17:20 ~ 17:40 Flutter & Low code backend - 구글 Firebase와 AWS Amplify 사용해보기 - 송승현( Dependable System Lab)

## 세션 #1 Linux Desktop, ROS

- Flutter 3.0 에서 Mac, Linux OS beta 에서 stable로 변경

### Flutter Desktop

데스크톱 앱을 빌드하기 위해서 추가적으로 아래 툴을 추가적으로 설치해야함

- Windows : visual studio 2022
- max os : xcode, cocoapod
- Linux : ...,...,,

리눅스에서는 더 많은 툴을 요구하지만 명령어 한줄로 한방에 설치 가능

```
flutter config
```

### Linux 데스크탑

### ROS

Robot Operating System, 로봇제어를 위한 미들웨어

### ROS + Flutter

- rcl dart
- Web socket OK
- Open Source!!, QT는 유료

ROS 에 웹 소켓 서버를 띄우고 Flutter 로 Client 를 만들어서 ROS 의 규격화된 메시지를 송수신 함으로써 로봇을 제어하거나 상태를 관제할수 있음

## 세션 #2 Dart 2.17 변경점, Enum 실제로 써보자!

### Enhaced Enum

- 이넘 내부에 멤버 변수 및, 함수를 선언 가능

### Super Initilizer

super 키워드로 보일러 플레이트 코드 삭제

### Named Parameter Every where

Optional Parameter를 아무곳이나 가능

### Flutter Lints 2.0

### Secure Sockets

Keylog Callbakck 을 통한 Logging 추가

### Dart Doc

1. 가장 많이 본 상의 200 페이지 핵심 라이브러리 샘플 코드 추가
2. dart doc 명령어 추가
3. dart pub/ flutter pub 명령어 추가

### Multi Platform 강화

#### Dart FFI

- C/ Native 코드와 소통하기 위한 장치
- 더 쉽게 FFI 플러그인을 만들수 있게 템플릿 제공
- NativeFinalizer : 메모리 관리 효울화

#### RISC-V 아키텍쳐 테스트 지원

### Enum 실프로젝트 적용

## 세션 3 멀티 모듈을 활용한 플러터 클린아키텍처

### Part 1. 클린 아키텍처

개발 효율화 => 개발 비용을 높이는 요소를 제거

개발 비용을 높이는 요소

- 지속적인 변경 요청
- 복잡도가 증가할 수록 변경 비용은 증가

### 변경에 잘 대응하기

- 시간 관점 대응

  - 플러그인 아키텍처

- 범위 관점 대응
  - 경계선 긋기

### 플러그인 아키텍처

- 추상적인 것 (일찍 결정해야 함)

- 구체적인 것 (늦게 결정해도 됨)
  - 데이터베이스

### 아키텍쳐 경계
