---
published: true
title: "[CSS] 레이아웃 정리(display, position)"
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

예제 코드를 통해서 CSS의 속성 중 display 와 position 의 동작 방식을 정리한 포스트입니다.

## display

CSS 의 display 속성은 태그를 `inline` 또는 `block` 타입으로 변경을 할수 있다.
  
예를 들어 아래와 같이 `div` 와 `span` 을 정의하였다고 할 때 `div` 는 기본적으로 `block` 타입 이고 `span` 은 `inline` 타입이다.

![CSS 레이아웃 정리 display, position 완성 _ 프론트엔드 개발자 입문편_ HTML, CSS, Javascript 3-11 screenshot](https://user-images.githubusercontent.com/35194820/148952972-fbe0ffbf-7d05-4199-a336-eb38f8bc9dff.png)
  
여기서 `span` 의 `display` 를 `block` 으로 선언하면 div 와 똑같이 한줄에 한 개 씩 표시가 된다.

![CSS 레이아웃 정리 display, position 완성 _ 프론트엔드 개발자 입문편_ HTML, CSS, Javascript 3-29 screenshot](https://user-images.githubusercontent.com/35194820/148953498-a089eb76-654a-4587-a8e7-dbe0a68c570a.png)

그리고 `div` 를 `inline-block` 으로 변경하게 되면 한줄에 여러개의 블록이 표시가 된다.

![CSS 레이아웃 정리 display, position 완성 _ 프론트엔드 개발자 입문편_ HTML, CSS, Javascript 3-38 screenshot](https://user-images.githubusercontent.com/35194820/148954154-ab1ab668-5a7c-4c36-8c7d-edd3e305ad18.png)

추가적으로 `div` 를 `inline` 으로 변경하게 되면 아무것도 표시가 되지 않는데 그 이유는 `inline` 은 내부 컨텐츠를 꾸며주는 것이고 현재 div 에는 컨텐츠가 아무것도 없기 때문이다.

![CSS 레이아웃 정리 display, position 완성 _ 프론트엔드 개발자 입문편_ HTML, CSS, Javascript 3-45 screenshot](https://user-images.githubusercontent.com/35194820/148954442-94b49760-a84c-4cee-85f4-26a2a67a8030.png)
content.com/35194820/148954154-ab1ab668-5a7c-4c36-8c7d-edd3e305ad18.png)

따라서 div 에 컨텐츠를 추가하면 span 처럼 화면에 표시가 되는 것을 볼수 있습니다.

![CSS 레이아웃 정리 display, position 완성 _ 프론트엔드 개발자 입문편_ HTML, CSS, Javascript 3-54 screenshot](https://user-images.githubusercontent.com/35194820/148954680-513abfa0-821b-4546-883d-18edf336bd7c.png)

## position


## Reference

[youtube, 드림코딩 by 엘리](https://www.youtube.com/watch?v=jWh3IbgMUPI)