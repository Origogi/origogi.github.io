---
published: false
title: "[Android][Compose] State 관리"	
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

## 상태란?

앱의 상태는 시간이 지남에 따라 변할 수 있는 값입니다. 이는 매우 광범위한 정의로서 데이터베이스에 저장 된 값부터 클래스 변수까지 모든 항목이 포함됩니다.
  
Android Compose 는 현재 상태에 따라 Composable를 구성하여 사용자에게 보여질 화면을 구성하며 만약 상태가 변화게 되면 __재구성(Re-Compose)__ 를 통해 화면을 업데이트 하게 됩니다.

## Stateful & Stateless Composable

Composable 은 두 가지 타입으로 나눠집니다. 상태를 가지고 있는 Stateful Composable 그리고 상태를 가자지 않는 Stateless Composable 입니다.

Stateful Composable 은 상태가 변하게 되면 자기 자신과 자식의 Composable 을 재구성하게 됩니다. Stateless Composable 은 상태가 없기 때문에 스스로 재구성을 할수 없으며 부모의 Composable이 재구성되어야 자신이 재구성을 하게 됩니다.

## Composable 의 상태

Composable 은 상태에 따라 UI를 구성하고 Composable 은 함수이기 때문에 상태값을 지역 변수로 저장하면 재구성이 될 때마다 함수가 새로 호출이 되기 때문에 지역 변수에 저장된 상태 값은 소실이 됩니다. 따라서 재구성 즉 Composable 함수가 호출이 되어도 상태 값을 계속 유지를 해야합니다. 이를 지원하는 것이 바로 `remember` 라는 delegate 입니다.

그리고 Composable에서 상태를 저장하고 상태가 변경이 되었을 때 재구성을 하기 위해서는 관찰가능한 객체를 사용하게 됩니다.  

이를 위해 `MutableState<T>` 를 사용하며 만약 State를 업데이트를 하게 된다면 해당 State를 관찰하고 있는 해당 Composable 를 재구성하게 됩니다. `MutableState<T>` 를 생성하기 위해서 `mutableStateOf()` api를 사용하게 됩니다.

~~~kotlin
interface MutableState<T> : State<T> {
   override var value: T
}
~~~

Composable에서 관찰가능하고 재구성이 여러번 호출되어도 상태 정보를 계속 유지하기 위해 위에서 언급한 `remember` 와 `MutableState<T>` 를 이용하여 구현합니다.
  
Composable 에서 MutableState 객체를 선언하는 데는 세 가지 방법이 있습니다.

- `val mutableState = remember { mutableStateOf(default) }`
- `var value by remember { mutableStateOf(default) }`
- `val (value, setValue) = remember { mutableStateOf(default) }`

## State Hoisting

State Hoisting 은 자신이 가지고 있는 상태를 부모 Composable 로 넘겨주어 자기 자신을 Stateless Composable로 바꾼다는 뜻입니다.
  
State Hoisting 를 사용하는 목적은 단반향 

## Reference

