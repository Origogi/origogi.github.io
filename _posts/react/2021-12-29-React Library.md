---
published: true
title: "[React] 자주 사용하는 라이브러리"
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
 - React
tags: 
  - React
comments: true
header:
  overlay_image: assets/images/flutter_dart.png
  overlay_filter: 0.5
  teaser: https://venturebeat.com/wp-content/uploads/2019/09/google-dart-flutter.png?w=578&strip=all
sitemap :
  changefreq : daily
  priority : 1.0
---

## Overview

자주 사용하는 React Library 를 정리한 포스트 입니다.

## 1. Material UI (MUI)

![image](https://media.vlpt.us/images/sunkim01/post/ab1bc5e7-5c3d-4d61-8b49-6feba35492a0/MeterialUI.png)
  
~~~bash
npm install @mui/material
~~~

### Icons

~~~bash
npm install @mui/icons-material
~~~

`MUI` 사용 시 `Module not found: Can't resolve '@emotion/react'` 에러가 발생 시 아래 패키지를 추가로 설치

~~~bash
npm install @emotion/react 
npm install @emotion/styled
~~~
