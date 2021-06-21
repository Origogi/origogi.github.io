---
published: true
title: "[Android][Compose] Simple Animation - 작성중"	
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

Andoid Compose에서 Animation을 지원하기 위한 Composable 이나 State 에 대해서 정리한 포스트입니다.

## 1. animateColorAsState()

Color 값을 변경했을 때 이를 자연스럽게 표현하기 위한 State입니다.

~~~kotlin
val backgroundColor = if (tabPage == TabPage.Home) Purple100 else Green300

Scaffold(
  ...
  backgroundColor = backgroundColor,
  ...
)
~~~

위 코드를 적용하였을 때 Scaffold의 Color가 변화는 과정을 애니메이션으로 표현하게 됩니다.

<div align="center">
<img src="https://developer.android.com/codelabs/jetpack-compose-animation/img/6946feb47acc2cc6.gif" width="30%">
</div>

## 2. AnimatedVisibility()

Composable를 보여지고 사라지는 과정을 애니메이션으로 표현해주는 Composable 입니다.


~~~
var extended : Boolean
...
AnimatedVisibility(extended) {
    Text(
        text = stringResource(R.string.edit),
        modifier = Modifier
            .padding(start = 8.dp, top = 3.dp)
    )
}
~~~

AnimatedVisibility 에 지정된 Boolean값이 변경 될 때마다 애니메이션을 실행합니다. 

기본적으로 AnimatedVisibility 의 자식 Composable을 페이드 인 및 확장하여 표시하고 페이드 아웃 및 축소하여 숨깁니다.

<div align="center">
<img src="https://developer.android.com/codelabs/jetpack-compose-animation/img/37a613b87156bfbe.gif" width="20%">
</div>
