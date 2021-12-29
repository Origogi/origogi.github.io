---
published: true
title: "[React] 자주 사용하는 라이브러리"
excerpt : " "
layout: single
author_profile: true
read_time: false
toc: false
toc_label: "My Table of Contents"
toc_icon: "cog"
toc_sticky: true
categories :
 - React

comments: true
header:
  # overlay_image: https://cf.festa.io/img/2019-9-27/a6c0b457-c336-42ba-b06e-462de90ada91.jpg
  # overlay_filter: 0.5
  # teaser: https://cf.festa.io/img/2019-9-27/a6c0b457-c336-42ba-b06e-462de90ada91.jpg
sitemap :
  changefreq : daily
  priority : 1.0
---

자주 사용하는 React Library 를 정리한 포스트 입니다.

## Material UI (MUI)

<div align="center">
   <img src="https://media.vlpt.us/images/sunkim01/post/ab1bc5e7-5c3d-4d61-8b49-6feba35492a0/MeterialUI.png" width=40%/>
</div>
  
  
~~~bash
npm install @mui/material
~~~

### Icons

~~~
npm install @mui/icons-material
~~~

`MUI` 사용 시 `Module not found: Can't resolve '@emotion/react'` 에러가 발생 시 아래 패키지를 추가로 설치

~~~
npm install @emotion/react 
npm install @emotion/styled
~~~