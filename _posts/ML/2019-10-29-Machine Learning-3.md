---
published: true
title: "Machine Learning 을 위한 Pre Processing (3)"
excerpt : " "
layout: single
classes : wide
author_profile: true
read_time: false # read_time을 출력할지 여부 1min read 같은것!
toc: true #Table Of Contents 목차 보여줌
toc_label: "My Table of Contents" # toc 이름 정의
toc_icon: "cog" #font Awesome아이콘으로 toc 아이콘 설정
toc_sticky: true # 스크롤 내릴때 같이 내려가는 목차
categories :
 - Machine Learning
tags: 
  - ML
  - Machine Learning
  - Python
  - Pandas
comments: true
header:
  overlay_filter: 0.5
  overlay_image: https://images.velog.io/post-images/leejh3224/bcb7de70-fc50-11e8-a0af-6d805f1eb60b/-2018-12-10-4.53.32.png
  teaser: https://images.velog.io/post-images/leejh3224/bcb7de70-fc50-11e8-a0af-6d805f1eb60b/-2018-12-10-4.53.32.png
sitemap :
  changefreq : daily
  priority : 1.0
---

# Pandas

pandas는 고 수준의 자료구조와 파이썬을 통한 빠르고 쉬운 데이터 분석 도구를 포함하고 있다.

- 명시적으로 축의 이름에 따라 데이터를 정렬할 수 있는 자료 구조를 제공한다.
- 누락된 데이터를 유연하게 처리를 할 수 있다.
- SQL과 같은 일반 데이터베이스처럼 데이터를 합치고 관계연산을 수행 할 수 있다.

## 1. Series
- 일렬의 객체를 담을 수 있는 1차원 백터
- 왼쪽에 색인을 보여주고 오른쪽에 해당 색의 값을 보여준다.

![series](https://image.slidesharecdn.com/slides-151008060416-lva1-app6892/95/pandas-powerful-data-analysis-tools-for-python-13-638.jpg?cb=1444284343)

~~~python
import pandas as pd
import numpy as np

obj = pd.Series([4,7,-5,3], index=['d', 'b', 'a', 'c'])
print(obj)
print('\n', obj.values) 
print('\n', type(obj.values))
print('\n', obj.index)

print('\n',obj['a'])
print('\n',obj[['a', 'c']])

print('\n', obj[obj>0])
print('\n', obj*2)
print('\n', np.exp(obj))
~~~
