---
published: false
title: "[Android] Coroutine을 활용한 안드로이드 디자인 패턴 - MVVM (Data binding)"	
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
  - Glide
  - Coroutine
  - Kotlin
comments: true	
header:	
  overlay_filter: 0.5	
sitemap :	
  changefreq : daily	
  priority : 1.0	
---

<div align="center">
<img src="https://miro.medium.com/max/2140/1*pgFREsmroqa52ITl4iBPZQ.png" >
</div>


## 서두

저번 포스트에서는 MVP에서 MVVM 으로 패턴을 변경하는 과정을 살펴보았습니다. View Model에서 data 가 변경이 되었을 때 View를 업데이트가 되는 과정은 Data binding 이 아닌 LiveData 에 옵저버를 등록하고 Data 변경을 관찰함으로써 View를 직접 업데이트를 하였습니다. 이번 포스트에서는 Data binding을 적용하여 View를 업데이트를 해보도록 하겠습니다.

## Data binding 이란?

Data binding에 대해서 알기 전에 먼저 선언적 UI 패턴과 프로그래미틱 UI 패턴의 차이 점에 대해 알아 보도록합시다.

프로그래미틱 UI 패턴이란 이벤트가 발생하면 메인 컨트롤러가 Data 를 업데이트를 하고 업데이트 할 View 를 선택하여 Data를 사용하여 View 를 업데이트하는 방식입니다. 가장 기초적이고 고전적인 방식입니다.

선언적 UI 패턴이란 View가 Data 를 관찰하고 있으며 만약 Data가 변경이 되면 View가 Data 값을 읽어서 View 스스로 업데이트가 되는 방식입니다.
여기서 View와 Data 를 서로 이어줘야(Binding) 하는데 View와 Data 를 서로 이어주도록 하는 것이 Data bidning 입니다.

Android에서는 Jetpack에 databinding library로 제공하고 있으며 이를 사용하여 View와 Data간 Data binding을 만들어 줍니다.

## Data binding 적용

