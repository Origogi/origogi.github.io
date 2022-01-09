---
published: true
title: "[React] 스니펫 단축키 정리"
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
  # overlay_image: assets/images/flutter_dart.png
  # overlay_filter: 0.5
  # teaser: https://venturebeat.com/wp-content/uploads/2019/09/google-dart-flutter.png?w=578&strip=all
sitemap :
  changefreq : daily
  priority : 1.0
---

## Overview

VS Code extension 스니펫 단축키를 정리한 포스트입니다.

## Reference

![image](https://user-images.githubusercontent.com/35194820/145939927-ac72717d-319c-4927-b92c-fc53332cfa0b.png)

[link](https://marketplace.visualstudio.com/items?itemName=dsznajder.es7-react-js-snippets)

## Import

|Commnad|Method|
|------|---|
|imp|import moduleName from 'module'|
|imr|import React from 'react'|
|impt|import PropTypes from 'prop-types'|
|imrpc|import React, { PureComponent } from 'react'|
|imrpcp|import React, { PureComponent } from 'react' & import PropTypes from 'prop-types'|

## Component

### rcc

~~~js
import React, { Component } from 'react'

export default class FileName extends Component {
  render() {
    return <div>$2</div>
  }
}
~~~

### rpcp

~~~js
import React, { PureComponent } from 'react'
import PropTypes from 'prop-types'

export default class FileName extends PureComponent {
  static propTypes = {}

  render() {
    return <div>$2</div>
  }
}
~~~