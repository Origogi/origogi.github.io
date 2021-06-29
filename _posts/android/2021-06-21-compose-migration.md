---
published: true
title: "[Android][Compose] Migration"	
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

## Overview

이번 포스트에서는 기존 Android app 에 어떻게 Compose 를 적용할수 있는지 에 대해 다룰 것입니다.

## Strategy

<div align="center">
<img src="https://user-images.githubusercontent.com/35194820/123266042-9930e200-d536-11eb-90c3-162cc53b045a.png" width="50%">
</div>

기존 Android app에서 Compose 를 적용한다고 했을 때 크게 두 가지 방법을 고려 할 수 있습니다.

1. 전체 화면 즉 Activity를 Compose 로 구성
2. 화면 일부분 즉 View 나 Fragment 를 Compose 로 구성

## 전체 화면을 Compose 로 구성

<div align="center">
<img src="https://user-images.githubusercontent.com/35194820/123266652-31c76200-d537-11eb-9201-af8e384b6095.png"  width="50%">
</div>

Activity 의 onCreate() 에서 `setContents{ .. }` 를 호출하여 Compose 로만 화면을 구성하며 기존의 Layout xml는 사용하지 않습니다.

## 화면 일부분만 Compose 로 구성

먼저 기존 Layout XML에 `ComposeView` 를 선언합니다. 

<div align="center">
<img src="https://user-images.githubusercontent.com/35194820/123266880-5e7b7980-d537-11eb-9d93-a71c25f0d214.png" width="50%">
</div>

Activity 나 Fragment에서 Layout를 inflate를 한 후 findViewById를 통해 `ComposeView` 를 불러오고 `ComposeView` 의 `setContents{ .. }` 를 호출하여 화면 일부분을 Compose 로 구성하게 됩니다.

## 기존 Android View 를 Composable 로 변환

기존에 사용하던 TextView, ImageView 또는 Layout view를 Composable로 변환할수 있습니다.

이때 사용하는 것이 `AndroidView` 입니다.

~~~kotlin
@Composable
fun TestAndroidView() {
    AndroidView(
        factory = { context ->
            LinearLayout(context).apply {
                orientation = LinearLayout.VERTICAL
                repeat(5) {
                    addView(TextView(context))

                }
            }
        },
        update = { layout ->
            layout.forEachIndexed { index, view ->
                (view as TextView).text ="Hello world #$index"
            }
        }
    )
}
~~~

AndroidView 에 `factory 매개변수`는 실제 View 를 생성하는 람다식을 넣고 `update 매개변수`는 상태가 변경했을 때 View를 업데이트하기 위한 코드를 람다식으로 전달합니다.
  
위 코드에 대해 간략히 설명하자면 수직 방향의 Linear Layout를 생성하고 그 안에 5개의 TextView 을 생성합니다.  

위 코드를 실행했을 때 아래의 화면이 보여지게 됩니다.

<div align="center">
<img src="https://user-images.githubusercontent.com/35194820/123273947-098f3180-d53e-11eb-9cac-f73a7cf1501c.png" width="30%">
</div>

## Reference

[상호 운용성 API](https://developer.android.com/jetpack/compose/interop/interop-apis?hl=ko)