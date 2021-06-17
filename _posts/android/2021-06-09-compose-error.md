---
published: true
title: "[Android][Compose] Compose 개발 시 Exception Error 해결 방안"	
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

해당 포스트는 현재 Compose 개발 시 발생한 Exception 및 버그를 어떻게 해결 했는 지에 대해 다루는 포스트이며 Compose는 아직 알파 버전이기 때문에 이전에 적용했던 해결 방안이 새로운 Compose 버전이 업데이트 되면서 적용이 안될수도 있음을 명심하시길 바랍니다.

## java.lang.NoSuchMethodError: No static method drawRect 발생

### 발생 원인

`Scaffold` 나 `LazyList` 를 사용했을 시 아래와 같이 Exception이 발생함

~~~java
java.lang.NoSuchMethodError: No static method drawRect-w2WG-Gw$default(Landroidx/compose/ui/graphics/drawscope/DrawScope;JJJFLandroidx/compose/ui/graphics/drawscope/DrawStyle;Landroidx/compose/ui/graphics/ColorFilter;Landroidx/compose/ui/graphics/BlendMode;ILjava/lang/Object;)V in class Landroidx/compose/ui/graphics/drawscope/DrawScope$DefaultImpls; or its super classes (declaration of 'androidx.compose.ui.graphics.drawscope.DrawScope$DefaultImpls' appears in /data/app/com.example.listsample-PfSvYy5TEHC73HGmesZBpQ==/base.apk)
        at androidx.compose.foundation.Background.drawRect(Background.kt:111)
        at androidx.compose.foundation.Background.draw(Background.kt:103)
        at androidx.compose.ui.node.ModifiedDrawNode.performDraw(ModifiedDrawNode.kt:102)
        at androidx.compose.ui.node.LayoutNodeWrapper$invoke$1.invoke(LayoutNodeWrapper.kt:260)
        at androidx.compose.ui.node.LayoutNodeWrapper$invoke$1.invoke(LayoutNodeWrapper.kt:259)
        at androidx.compose.runtime.snapshots.SnapshotStateObserver.observeReads(SnapshotStateObserver.kt:121)
        at androidx.compose.ui.node.OwnerSnapshotObserver.observeReads$ui_release(OwnerSnapshotObserver.kt:75)
        at androidx.compose.ui.node.LayoutNodeWrapper.invoke(LayoutNodeWrapper.kt:259)
        at androidx.compose.ui.node.LayoutNodeWrapper.invoke(LayoutNodeWrapper.kt:53)
        at androidx.compose.ui.platform.ViewLayer.dispatchDraw(ViewLayer.android.kt:240)
~~~

### 해결 방안 (21.06.09 기준)

1. Project 폴더 `Root` 의 build.gradle를 아래와 같이 수정

~~~gradle
buildscript {
    ext {
        compose_version = '1.0.0-beta08' // 1. compose 버전 수정
    }
    ...
    dependencies {
      ...
      classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:1.5.10" // 2. Kotin plugin 버전 수정

    }
    ...
}
~~~

2. Project 폴더에서 `app` 의 build.gradle를 아래와 같이 수정

~~~gradle
...
android {
    ...
    composeOptions {
        kotlinCompilerExtensionVersion compose_version
        kotlinCompilerVersion '1.5.10' // 1. kotlin compile 버전 수정
    }
}
~~~

## java.lang.NoSuchMethodError startRestartGroup 발생

### 발생 원인

Compose 버전을 '1.0.0-beta08' 로 업그레이드 시 coil 라이브러리에서 아래와 같이 Exception 발생

~~~java
No interface method startRestartGroup(ILjava/lang/String;)Landroidx/compose/runtime/Composer; in class Landroidx/compose/runtime/Composer; or its super classes (declaration of 'androidx.compose.runtime.Composer' 
....
 at com.google.accompanist.coil.CoilImage__CoilKt.CoilImage(Coil.kt:245)
        at com.google.accompanist.coil.CoilImage.CoilImage(Coil.kt:1)
~~~

### 해결 방안 (2021.06.09 기준)

coil 라이브러리를 아래와 같이 최선 버전으로 업그레이드

~~~java
implementation "com.google.accompanist:accompanist-coil:0.10.0"
~~~