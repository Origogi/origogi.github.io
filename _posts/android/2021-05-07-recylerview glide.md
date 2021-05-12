---
published: false
title: "[Android] Coroutine을 활용한 안드로이드 디자인 패턴 - MVC"	
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
  - Glide
comments: true	
header:	
  overlay_filter: 0.5	
sitemap :	
  changefreq : daily	
  priority : 1.0	
---

## 서두

Coroutine을 이용하여 비동기적으로 서버로 부터 데이터를 다운받고 그 결과를 화면에 뿌려주는 기능을 구현한다고 가정했을 때 가장 대표적인 디자인 패턴인 MVC, MVP, MVVM 을 적용한다고 했을 때 어떻게 구현하는 지를 간단한 샘플코드를 이용하여 나타 내고자 합니다. 먼저 디자인 패턴 중 MVC 부터 살펴보도록 하겠습니다.

## 실행 화면

<div align="center">
<img src="https://user-images.githubusercontent.com/35194820/117981811-5e049600-b370-11eb-97d4-52fca92cf356.gif" >
</div>
</br>

앱에 대해 간단히 설명하자면 서버로 부터 HTML 문서를 다운받고 문서로 부터 이미지-제목을 파싱하여 RecyclerView에 뿌려주는 갤러리 앱입니다. 비동기 처리를 위해 Coroutine을 사용했습니다.

## 샘플 코드

아래 Repository를 참고바랍니다.

[Repository](https://github.com/Origogi/Android-Coroutine-Galley-App)

## MVC

MVC 는 Model-View-Controller의 약자로써 3개의 집합으로 구분합니다.

### Model 

