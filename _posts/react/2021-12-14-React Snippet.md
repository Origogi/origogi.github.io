---
published: true
title: "[React] 자주 사용하는 코드 스니펫 정리"
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

자주 사용하는 VS Code extension 스니펫 정리한 포스트입니다.

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