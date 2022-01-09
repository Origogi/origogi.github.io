---
published: true
title: "[CSS] 셀렉터, 기초 이론 정리"
excerpt : " "
layout: single
author_profile: true
read_time: false # read_time을 출력할지 여부 1min read 같은것!
toc: true #Table Of Contents 목차 보여줌
toc_label: "My Table of Contents" # toc 이름 정의
toc_icon: "cog" #font Awesome아이콘으로 toc 아이콘 설정
toc_sticky: true # 스크롤 내릴때 같이 내려가는 목차
categories :
 - HTML
tags: 
  - CSS
  - FE
comments: true
header:
  overlay_image: https://wi.wallpapertip.com/wsimgs/31-312238_html-css-javascript-transparent.png
  overlay_filter: 0.5
  teaser: https://media.vlpt.us/images/ensi/post/9db78823-6458-4609-a6ec-14014214acc5/htmlcssjs.jpg
sitemap :
  changefreq : daily
  priority : 1.0
---

![](https://media.vlpt.us/images/chayezo/post/2d640f76-1ba2-477f-8725-784e11a073ee/cssImage.png)

## Overview

CSS 의 가장 기본 및 기초에 대해 정리한 포스트입니다.

## 1. 의미, 정의

CSS 는 Cascading Style Sheet 의 약자이다.  
CSS는 HTML 요소들이 각종 미디어에서 어떻게 보이는가를 정의하는 데 사용되는 스타일 시트 언어이다.

### Cascading

![11111](https://user-images.githubusercontent.com/35194820/148683780-16e84b67-2e24-440b-b845-4497bce48c66.png)

HTML 의 스타일 적용 우선 순위는 `Author Style` > `User Style` > `Browser Style` 순으로 결정이 된다.

`Author Style` 은 .css 의 문서를 뜻하며 `User Style` 은 유저가 선택한 스타일이다. 예를 들어 다크 모드나 라이드 모드 전환이 있다. 마지막으로 `Browser Style` 은 웹 브라우저에 내장된 스타일를 뜻한다.

HTML 의 요소의 디자인/스타일을 결정할 때 위에 언급된 우선 순위로 탐색을 하기 때문에 `Cascading` 특징을 가진다.
  
추가적으로 위 스타일의 연결고리를 끊고 싶을 때에는 `!important` 키워드를 사용한다. 하지만 가능다면 `!important` 사용을 배제를 해야한다.

## 2. Selector

![33](https://user-images.githubusercontent.com/35194820/148685121-7eaf1267-a838-4665-9ebe-f68eaa6db47b.png)

CSS 에서 Selector 란 HTML 에서 어떤 태그를 골라서 스타일을 적용할지 를 결정하는 문법

### Universal

HTML 내에 모든 태그에 대해서 적용

~~~css
* {
  color : green;
}
~~~

위 CSS 를 적용하면 모든 태그에 대해 색상이 green으로 적용이 된다.

### Type

HTML 내에 지정된 같은 타입의 태그들에 대해서 스타일 적용
  
예를 들어 `div` 를 선택했으면 해당 HTML에 `div` 태그에 대해서 스타일이 일괄 적용이 된다.




## Reference

[youtube, 드림코딩 by 엘리](https://www.youtube.com/watch?v=gGebK7lWnCk)