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

AnimatedVisibility 에 `enter`와 `exit` 매개 변수를 사용하여 Composable이 보여지고 사라지는 사용자 정의 애니메이션을를 사용할 수 있습니다.

~~~kotlin
AnimatedVisibility(
    visible = shown,
    enter = slideInVertically(
        // Enters by sliding down from offset -fullHeight to 0.
        initialOffsetY = { fullHeight -> -fullHeight },
        animationSpec = tween(durationMillis = 150, easing = LinearOutSlowInEasing)
    ),
    exit = slideOutVertically(
        // Exits by sliding up from offset 0 to -fullHeight.
        targetOffsetY = { fullHeight -> -fullHeight },
        animationSpec = tween(durationMillis = 250, easing = FastOutLinearInEasing)
    )
) {
  ...
}
~~~

`enter` 매개변수에 `EnterTransition` 을 인스턴스화 하여 넘겨주며 `EnterTransition` 는 해당 Composable의 시작 위치 및 `AnimationSpec` 를 정의합니다.
그리고 exit 매개변수에 `ExitTransition` 을 인스턴스화 하여 넘겨주며 `ExitTransition` 는 해당 Composable의 종료 위치 및 `AnimationSpec` 를 정의합니다.

위 매개변수를 통해 Composable이 위/아래 방향으로 보여지고 사라지는 것을 볼수 있습니다.

<div align="center">
<img src="https://developer.android.com/codelabs/jetpack-compose-animation/img/76895615b43b9263.gif" width="30%">
</div>
   
AnimatedVisibility의 디폴트 매개변수는 아래와 같습니다.

~~~kotlin
@Composable
fun AnimatedVisibility(
    visible: Boolean,
    modifier: Modifier = Modifier,
    enter: EnterTransition = fadeIn() + expandIn(),
    exit: ExitTransition = shrinkOut() + fadeOut(),
    content: @Composable() AnimatedVisibilityScope.() -> Unit
) {
    ...
}
~~~

## 3. Modifier.animateContentSize()

Composable 함수 내부에 사이즈가 변경이 되거나 컨텐츠가 변경이 될 경우 동적으로 사이즈 변화를 애니메이션으로 처리가 됩니다.

~~~kotlin
Column(
    modifier = Modifier
        .fillMaxWidth()
        .padding(16.dp)
        .animateContentSize()
) {
    // Column 안의 내용물이 변경이 된 경우
}
~~~

<div align="center">
<img src="https://developer.android.com/codelabs/jetpack-compose-animation/img/c0ad7381779fcb09.gif" width="30%">
</div>

