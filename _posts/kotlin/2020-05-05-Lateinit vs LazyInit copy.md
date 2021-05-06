---
published: true
title: "[Kotlin] lateinit vs by lazy"
excerpt : " "
layout: single
author_profile: true
read_time: false
toc: true
toc_label: "My Table of Contents"
toc_icon: "cog"
toc_sticky: true
categories :
  - Kotlin
tags: 
  - Kotlin
  - kotlin

comments: true
header:
  # overlay_image: https://cf.festa.io/img/2019-9-27/a6c0b457-c336-42ba-b06e-462de90ada91.jpg
  # overlay_filter: 0.5
  # teaser: https://cf.festa.io/img/2019-9-27/a6c0b457-c336-42ba-b06e-462de90ada91.jpg
sitemap :
  changefreq : daily
  priority : 1.0
---

## 지연 초기화

가끔은 객체를 처음 사용하기 전까지 객체의 인스턴스 생성을 지연시키고 싶은 경우가 있습니다. 이러한 기교를 지연 초기화 또는 지연 인스턴스화 라고 부릅니다.

지연 초기화의 주요한 목적은 성능을 향상하고 메모리를 차지하는 공간을 줄이는 데 있습니다.

Kotlin에서는 지연 초기화를 위해 __lateinit__ 과 __by lazy__ 두 가지 옵션을 제공합니다.

## lateinit

1. var 앞에만 선언이 가능하며 불변성을 얻을수 없습니다.
2. null 로 초기화가 불가능합니다.
3. 객체가 보이는 어느 곳애서든 초기화가 가능합니다.
4. 초기화 전까지 접근이 불가능하며 접근 시 Exception을 발생합니다.
5. kotlin Primitive type 에서는 사용 불가합니다. 예를 들어 Int, Double, Float, Boolean,..
6. getter, setter 를 override 가 불가능합니다.

## by lazy

1. 변수를 접근 시점에 by lazy { } 의 함수 리터럴을 통해 초기화를 합니다.
2. val 에서만 사용이 가능하며 당연히 값을 변경할수 없습니다.
3. 기본적으로 Synchronized 로 동작합니다.
  
## Reference

[코틀린 프로그래밍](http://acornpub.co.kr/book/programming-kotlin)
