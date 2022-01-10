---
published: true
title: "[HTML] Sections"
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
  - HTML
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

## HTML Section

HTML 에서 권장하는 기본 레이아웃 구성

![111](https://user-images.githubusercontent.com/35194820/148676504-17785e80-f51b-4a25-a011-da8147420fed.png)

## Type

Tag는 아래와 같이 크게 두 종료가 있으며  

`Box` 는 또 다른 Tag 들을 묶어주는 역할을 하며 사용자에게 보여지지 않는다.  
`Item` 은 사용자에게 컨텐츠를 보여주는 역할을 한다.

![HTML 태그 완성, 웹 포트폴리오 만들기 전에 꼭 보세요 _ 프론트엔드 개발자 입문편_ HTML, CSS, Javascript 6-3 screenshot](https://user-images.githubusercontent.com/35194820/148676608-2fd88a65-0a18-4d88-8210-2a607a00c492.png)

## Block, Inline

![333](https://user-images.githubusercontent.com/35194820/148676909-6db17fbc-8b23-4a8d-8504-a5a003d70419.png)

태그는 `Block` 또는 `Inline` 타입으로 나눠진다. 예를 들어 아래 그림에서 핑크색 태그가 있는 상태에서 노란 색 태그를 배치하고자 할 때  
  
`Block` 타입은 핑크 색 태그 옆에 충분한 공간이 있음에도 불구하고 다음 줄에 배치된 것을 볼수 있는데, 즉 `Block` 타입은 한줄에 하나 만 배치를 한다.  

`Inline` 타입은 핑크 색 태그 옆에 여유 공간에 노란 색 블록이 배치가 되는 데, 즉 `Inline` 은 공간이 허용이 되면 한 줄에 여러개의 태그를 배치할수 있다.

## Tag & Element

![444](https://user-images.githubusercontent.com/35194820/148676991-525f0502-4de9-4012-b4eb-ffebf3df4f79.png)

꺽쇠로 감싸 있는 부분을 `Tag` 라 하며 `Opening Tag` 와 `Closing Tag` 사이를 `Content` 라고 한다. 그리고 `Tag` 와 `Content` 를 합쳐서 `Element` 또는 `Node` 라고 한다.

## 자주 사용하는 Tag 들

### A tag (Anchor Tag)

~~~html
  <a href="https://www.google.com" target=_blank />Click</a>
~~~

하이퍼링크를 가능하게 하는 태그, 즉 다른 페이지로 이동이 가능하다.

### P tag (Paragraph Tag)

~~~html
  <p>This is a sentence. <b>That</b> is.. </p> 
  <p>This is a sentence. <span>That</span> is.. </p> 
  <p>This is a sentence. <div>That</div> is.. </p> 
~~~

문단을 정의하는 태그이며 `b`, `span`, `div` 태그를 포함 할수 있다.

### List tag (ol vs ul)

ol 태그는 순서가 있는 태그이다.

~~~html
 <ol>
    <li>1</li>
    <li>2</li>
    <li>3</li>
  </ol>
~~~

위 코드를 실행하면 브라우져에서 아래와 같이 순서를 나타내는 prefix가 추가가 된다.

![image](https://user-images.githubusercontent.com/35194820/148677502-675d08ff-7e85-4a7b-8b28-56c38143b24e.png)

ul 태그는 순서가 없는 태그이다.

~~~html
 <ul>
    <li>1</li>
    <li>2</li>
    <li>3</li>
  </ul>
~~~

위 코드를 실행하면 브라우져에서 아래와 같이 기호 prefix가 추가가 된다.

![image](https://user-images.githubusercontent.com/35194820/148677589-651ab5ac-e8f1-4bda-95f7-d5ae09e5dec1.png)

### Input tag

~~~html
<lable>Name: </lable>
<input id="input_name" type="text">
~~~

사용자로 부터 입력받을수 있는 태그이다. 보통 앞에 `label` 태그를 붙여서 어떤 데이터를 입력받을지를 나타낸다.
  
attr의 `id` 같은 경우 하나의 page에 여러개의 input 태그가 존재할 경우 각 태그들을 구별하기 위해 사용한다.
  
attr 의 `type` 같은 경우 date, color, number, password, checkbox 등 다양한 타입이 존재한다.

## Reference

[Youtube, 드림코딩 by 엘리](https://www.youtube.com/watch?v=OoA70D2TE0A)