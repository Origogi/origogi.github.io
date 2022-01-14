---
published: true
title: "[CSS] Flexbox 정리"
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

예제 코드를 통해서 CSS의 속성 중 Flexbox 의 동작 방식에 대해 정리한 포스트입니다.

## Flexbox란?

아래와 같이 Container 에 여러개의 item 을 배치를 개발자가 쉽게 컨트롤할수 있도록 도와준다.

![CSS Flexbox 완전 정리  포트폴리오 만드는 날까지! _ 프론트엔드 개발자 입문편_ HTML, CSS, Javascript 2-47 screenshot](https://user-images.githubusercontent.com/35194820/149147412-cdbbf8c4-3784-4868-ad87-e60969b77bab.png)

예를 들어 각 아이템의 height 를 동일하게 하거나 각 아이템의 간격을 동일하게 하는 작업을 좀 더 편하게 수행할수 있다.

flexbox 는 크게 두 가지 타입에 대해 적용하게 되는 데

![CSS Flexbox 완전 정리  포트폴리오 만드는 날까지! _ 프론트엔드 개발자 입문편_ HTML, CSS, Javascript 4-22 screenshot](https://user-images.githubusercontent.com/35194820/149148924-896b8ef8-a080-44f3-a0f5-6ffbeecff0a3.png)

- container : item 을 담고 있는 박스
- item : container 에 포함되어 있는 요소들

### container

container에 지정되어 있는 속성은 아래와 같다.

![CSS Flexbox 완전 정리  포트폴리오 만드는 날까지! _ 프론트엔드 개발자 입문편_ HTML, CSS, Javascript 4-27 screenshot](https://user-images.githubusercontent.com/35194820/149149134-b47f8d8b-0efa-4f8d-ba44-0fd0593e3ffe.png)

### item

item에 지정되어 있는 속성은 아래와 같다.

![CSS Flexbox 완전 정리  포트폴리오 만드는 날까지! _ 프론트엔드 개발자 입문편_ HTML, CSS, Javascript 4-46 screenshot](https://user-images.githubusercontent.com/35194820/149149626-04c2031e-a932-47d5-a56d-3ffcf9d89f7c.png)

### axis

flexbox 는 수평 축, 수직 축을 가지고 있고 둘중 하나를 main axis으로 선택하면 남은 하나는 cross axis 가 된다.

<div align="center">
<img src="https://user-images.githubusercontent.com/35194820/149156154-765f320b-bf67-41fa-b44f-c8c3ce7a3f72.png" width=300>
<img src="https://user-images.githubusercontent.com/35194820/149156163-f9fb705c-c430-4b14-af66-77479f1a9b9e.png" width=300>
</div>

## Flexbox 적용하기

아래와 같이 10개의 블록을 선언해보자
  
![CSS Flexbox 완전 정리  포트폴리오 만드는 날까지! _ 프론트엔드 개발자 입문편_ HTML, CSS, Javascript 8-47 screenshot](https://user-images.githubusercontent.com/35194820/149166004-4008f056-6089-4b77-9436-4605ab440241.png)

위 샘플 코드는 아래와 같다.

__HTML__

~~~html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width">
  <title>JS Bin</title>
</head>
<body>
  <div class="container">
    <div class="item item1">1</div>
    <div class="item item2">2</div>
    <div class="item item3">3</div>
    <div class="item item4">4</div>
    <div class="item item5">5</div>
    <div class="item item6">6</div>
    <div class="item item7">7</div>
    <div class="item item8">8</div>
    <div class="item item9">9</div>
    <div class="item item10">10</div>
  </div>
</body>
</html>
~~~

__CSS__

~~~css
.container {
  background : beige;
  height :100vh;
}

.item {
  width : 40px;
  height :40px;
}

.item1 {
  background : #e57373;
}

.item2 {
  background : #f06292;
}

.item3 {
  background : #ba68c8;
}

.item4 {
  background : #9575cd;
}

.item5 {
  background : #7986cb;
}

.item6 {
  background : #64b5f6;
}

.item7 {
  background : #4fc3f7;
}

.item8 {
  background : #4dd0e1;
}

.item9 {
  background : #4db6ac;
}

.item10 {
  background : #81c784;
}
~~~
  
container 를 flexbox 로 적용하기 위해서 display:flex 를 추가하면 아래와 같이 블록이 정렬이 되는 것을 확인할 수 있다.

![CSS Flexbox 완전 정리  포트폴리오 만드는 날까지! _ 프론트엔드 개발자 입문편_ HTML, CSS, Javascript 9-37 screenshot](https://user-images.githubusercontent.com/35194820/149166323-988c427d-f88e-4b7b-bbf4-4811ee7d0a35.png)

### flex-direction

블록의 방향을 변경할 때 사용하며 기본 값은 `row` 이다.

~~~css
.container {
  background : beige;
  height : 100vh;
  display :flex;
  flex-direction: row;
}
~~~

그 외 값은 아래와 같다.

|row|row-reverse|
|------|---|
|![image](https://user-images.githubusercontent.com/35194820/149167069-8d1767d4-71e2-49d8-bfa7-7560b5bdbd82.png)|![image](https://user-images.githubusercontent.com/35194820/149167399-8f5b7a2c-72b5-4d78-a080-27bc45c5a39a.png)|

|column|column-reverse|
|------|---|
|![image](https://user-images.githubusercontent.com/35194820/149167582-23d31f3c-5979-46c1-90ed-5d3918cab1fc.png)|![image](https://user-images.githubusercontent.com/35194820/149167813-c1da7288-e595-4f2d-9e98-69dfa9be031a.png)|

### flex-wrap

한 줄에 아이템이 너무 많을 경우 아이템을 다른 줄로 줄 바꿈을 할지 아님 아이템 사이즈를 축소시켜서 한 줄에 표시할지를 결정한다.

~~~css
{
  ... 
  flex-wrap : nowrap; // or `wrap`
}
~~~

기본 값은 `nowrap` 이며 `nowrap` 일 경우 아래와 같이 item 을 감싸는 container 가 작아져도 무조건 한줄로 표시됨을 볼 수있다.

![111](https://user-images.githubusercontent.com/35194820/149457393-0e5e8069-8b50-41db-bee0-9517ac362ebb.gif)
  
`flex-wrap` 값을 `wrap` 으로 지정한 경우 아래와 같이 container 가 작아지면 자동으로 줄 바꿈을 하는 것을 볼수 있다.

![2222](https://user-images.githubusercontent.com/35194820/149457400-d220ba7b-4cdc-4587-88a0-893a72712bac.gif)

### justify-content

중심 축 기준으로 item 을 어떻게 배치할지 결정한다.

~~~css
{
    justify-content : start // or end, space-around, ...
}
~~~

#### __start__

디폴트 값으로 중심 축이 수평 방향이면 item 을 왼 쪽에 그리고 수직 방향이면 item 을 위 쪽에 배치를 한다.

#### __end__

중심 축이 수평 방향이면 아이템을 오른 쪽 에 그리고 수직 방향이면 아이템을 아래 쪽에 배치를 한다.

#### __center__

item 을 중앙으로 배치한다.

![image](https://user-images.githubusercontent.com/35194820/149459069-6aa21c6d-3fbc-4e03-8df9-480b56d46e1d.png)

#### __space-around__

중심 축이 수평 방향이면 아이템 좌 우에 동일한 크기의 space 가 추가된다. 그리고 수직 방향이면 위, 아래에 동일한 크기의 space 가 추가된다.

![image](https://user-images.githubusercontent.com/35194820/149458581-7f4b7bbd-bd6e-4a07-a5c5-44baeaab8ea1.png)

#### __space-between__

item 과 item 사이의 space 의 사이즈는 모두 동일하고 item 과 container 의 space 사이즈는 0가 된다.

![image](https://user-images.githubusercontent.com/35194820/149458619-cf1c5017-c828-4db1-98c9-7b002a6af41d.png)

#### __space-evenly__

item 과 item 사이 그리고 item 과 container 의 space 사이즈는 모두 동일하다.

![image](https://user-images.githubusercontent.com/35194820/149458680-5c613b5e-6a68-4a26-976a-e0025ef8eb8e.png)

### align-content

반대 축의 item 을 어떻게 배치할지 결정

|center|space-around|space-between|
|------|---|---|
|![image](https://user-images.githubusercontent.com/35194820/149462601-775d7eb7-cdeb-4e18-965d-94f5ff917747.png)|![image](https://user-images.githubusercontent.com/35194820/149462880-9d8904e6-b86d-4812-bbcc-c778cbb65309.png)|![image](https://user-images.githubusercontent.com/35194820/149462761-d8e81e0f-b008-45e1-982f-70464c90d251.png)|

~~~css
{
    align-content : start // or end, space-around, ...
}
~~~

## 참고 자료

좀 더 자세한 flexbox 정보는 아래 사이트 참고
![image](https://user-images.githubusercontent.com/35194820/149463176-d6b2a1ac-8380-4551-834f-8738912e0142.png)
[A Complete Guide to Flexbox
](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)

## Reference

[youtube, 드림코딩 by 엘리](https://www.youtube.com/watch?v=7neASrWEFEM)
