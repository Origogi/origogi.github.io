---
published: true
title: "[Android] Activity 전환 시 Life cycle 순서"	
excerpt : " "	
layout: single	
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
comments: true	
header:
  overlay_image: https://user-images.githubusercontent.com/35194820/119770376-18f76c80-bef7-11eb-8b3e-abca9300d1c1.gif
  overlay_filter: 0.8
  teaser: https://www.pngkey.com/png/full/27-278037_banner-freeuse-library-android-transparent-app-android-development.png
sitemap :	
  changefreq : daily	
  priority : 1.0	
---

Android Activity 전환 시 각 Activity Life cycle 순서를 정리 한 포스트입니다.

## Main Activity에서 Detail Activity 실행

아래와 같이 Gallary 앱이 동작을 할 때

![detail activity](https://user-images.githubusercontent.com/35194820/104006608-0ef3fc00-51ea-11eb-97da-7994d2869fc0.gif)

아래와 같은 순서로 Life cycle 이 호출이 됩니다.

|Time Line|Main Activity|Detail Activity|
|------|---|---|
|1|onPasue()| |
|2| |onCreate()|
|3| |onStart()|
|4| |onResume()|
|5| onStop()||
  
## Detail Activity가 종료되고 Main Activity 복귀

아래와 같이 Gallary 앱이 동작을 할 때

![main activity](https://user-images.githubusercontent.com/35194820/104006681-27fcad00-51ea-11eb-8d9e-9180370be700.gif)

  
아래와 같은 순서로 Life cycle 이 호출이 됩니다.
  
|Time Line|Main Activity|Detail Activity|
|------|---|---|
|1|onRestart()| |
|2|onStart()| |
|3| |onPause()|
|4|onResume()||
|5| |onStop()|
|6| |onDestroy()|
