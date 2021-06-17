---
published: false
title: "[Android][Compose] State hoisting"	
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

State hoisting is a pattern of moving state up to make a component stateless.

When applied to composables, this often means introducing two parameters to the composable.

value: T – the current value to display
onValueChange: (T) -> Unit – an event that requests the value to change

Single source of truth – by moving state instead of duplicating it, we're ensuring there's only one source of truth for the text. This helps avoid bugs.
Encapsulated – only TodoItemInput will be able to modify the state, while other components can send events to TodoItemInput. By hoisting this way, only one composable is stateful even though multiple composables use the state.
Shareable – hoisted state can be shared as an immutable value with multiple composables. Here we're going to use the state in both TodoInputTextField and TodoEditButton.
Interceptable – TodoItemInput can decide to ignore or modify events before changing its state. For example, TodoItemInput could format :emoji-codes: into emoji as the user types.
Decoupled – the state for TodoInputTextField may be stored anywhere. For example, we could choose to back this state by a Room database that is updated every time a character is typed without modifying TodoInputTextField.

When hoisting state, there are three rules to help you figure out where it should go

State should be hoisted to at least the lowest common parent of all composables that use the state (or read)
State should be hoisted to at least the highest level it may be changed (or modified)
If two states change in response to the same events they should be hoisted together
You can hoist state higher than these rules require, but underhoisting state will make it difficult or impossible to follow unidirectional data flow.