---
published: true
title: "[Android][Compose] Compose Overview"	
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
 - Android	
tags: 	
  - Android
  - Kotlin
  - Compose
  - JetPack
comments: true	
header:
  overlay_image: https://user-images.githubusercontent.com/35194820/119770376-18f76c80-bef7-11eb-8b3e-abca9300d1c1.gif
  overlay_filter: 0.4
  teaser: https://www.pngkey.com/png/full/27-278037_banner-freeuse-library-android-transparent-app-android-development.png
sitemap :	
  changefreq : daily	
  priority : 1.0	
---

<div align="center">
<img src="https://user-images.githubusercontent.com/35194820/120804010-1fcd5180-c57f-11eb-9863-f632f74bbf86.jpg">
</div>

## 서론

해당 포스트는 Android Compose 의 개념에 대해 다루고자 합니다.

## Anroid Compose

Compose 는 Android 를 위한 선언형 UI 패턴 라이브러리입니다. 선언형 API를 사용하여 앱의 UI를 좀 더 쉽고 직관적으로 구현할 수 있도록 합니다. 
Compose를 이해하기 위해서는 반드시 선언형 UI 패턴의 개념에 대해 알아야합니다.

## 명령형(Imperative) UI 패턴 VS 선언형(Declarative) UI 패턴

명령형 UI 패턴은 가장 고전적인 UI 를 업데이트하는 방식입니다. 각 화면 요소를 View와 View를 그릴 때 필요한 데이터를 저장하는 State가 있다고 가정할 때
UI가 업데이트 되는 과정은 아래와 같습니다.

1. 어떤 이벤트가 발생하면 중간에 컨트롤러가 이벤트를 수신
2. 이벤트를 처리하고 State 를 업데이트 
3. 컨트롤러는 여러개의 View 중 업데이트할 View를 선택
4. View에 setter 를 통해 State를 넘겨줌
5. View는 넘겨받은 State를 기반으로 View를 갱신

명령형(Imperative) UI 패턴은 다음과 같은 단점을 가지고 있습니다.

<div align="center">
<img src="https://user-images.githubusercontent.com/35194820/120802957-fcee6d80-c57d-11eb-9eee-2dcdfdbfd863.PNG">
</div>

1. View 와 컨트롤러에 State가 중복됩니다.
2. View 와 컨트롤러에 State는 항상 일치시켜야 하며 일치시키는 과정이 번거로울수도 있습니다.
3. 코드가 복잡해질수록 View 와 컨트롤러에 State를 일치시키는 로직이 누락이 될수도 있습니다..

위와 같은 단점을 극복하기 위해 나온 개념이 선언형 UI 패턴 방식입니다. 선언형 UI 패턴은 다음과 같은 특징을 가지고 있습니다.

<div align="center">
<img src="https://user-images.githubusercontent.com/35194820/120802961-fd870400-c57d-11eb-9332-40d9bf2f0348.PNG">

<img src="https://user-images.githubusercontent.com/35194820/120802963-fe1f9a80-c57d-11eb-87aa-e64b23ea2b91.PNG">
</div>


1. View 자체에 이제 State는 존재하지 않습니다. 따라서 더이상 setter 를 노출되지 않습니다.
2. View 는 State 를 바라보고 있으며 State를 기반으로 View를 생성/구축합니다.
3. State가 변경이 되면 View 를 다시 생성/구축합니다.

## Composable 함수

Android Compose 는 화면을 구성할 때 Composable 함수를 이용합니다. Composable 함수는 `@Composable` 어노테이션을 선언함으로써 구성하게 됩니다.

위 코드에서 보이는 `Text( ... )` 는 아래와 같이 미리 정의된 Composable 입니다.

~~~kotlin
@Composable
fun Text(text: AnnotatedString, modifier: Modifier = Modifier, ...) {
    ...
}
~~~

또한 `@Composable` 어노테이션를 사용하여 아래와 같이 Composable을 추가하고 호출이 가능합니다.

~~~kotlin
@Composable
fun NewsStory() {
 ...
}
~~~

특정 Composable 은 자식 Composable를 가질수 있으며 자식 Composable 는 또 자식 Composable를 가짐으로써 Composable tree를 구성하게 됩니다. 이는 Flutter의 Widget tree와 매우 유사합니다.

Composable 함수는 아래와 같은 특징을 가지고 있습니다.

- Composable 함수는 순서와 관계없이 실행할 수 있습니다.
- Composable 함수는 동시에 실행할 수 있습니다.
- Composable Tree에서 특정 Composable이 변경이 되면 해당 Composable과 해당 자식 Composable만 새로 구성을 합니다.
- Composable 은 취소가 될수 있습니다. 예를 들어 State가 변경이 되어 재구성을 하는 중에 다시 State가 변경이 되면 기존에 수행중인 재구성을 취소하고 다시 재구성을 시도합니다.
- UI 애니메이션 동작을 처리할 때 Composable 함수는 자주 실행될 수 있습니다.

## Reference

- [Compose 이해](https://developer.android.com/jetpack/compose/mental-model?continue=https%3A%2F%2Fdeveloper.android.com%2Fcourses%2Fpathways%2Fcompose%23article-https%3A%2F%2Fdeveloper.android.com%2Fjetpack%2Fcompose%2Fmental-model)
