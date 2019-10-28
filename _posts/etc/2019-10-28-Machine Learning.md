---
published: true
title: "Machine Learning 교육 정리"
excerpt : " "
layout: single
author_profile: true
read_time: false # read_time을 출력할지 여부 1min read 같은것!
toc: true #Table Of Contents 목차 보여줌
toc_label: "My Table of Contents" # toc 이름 정의
toc_icon: "cog" #font Awesome아이콘으로 toc 아이콘 설정
toc_sticky: true # 스크롤 내릴때 같이 내려가는 목차
categories :
 - etc
tags: 
  - ML, Machine Learning
comments: true
header:
  #overlay_image: https://cf.festa.io/img/2019-9-27/a6c0b457-c336-42ba-b06e-462de90ada91.jpg
  #overlay_filter: 0.5
  #teaser: https://cf.festa.io/img/2019-9-27/a6c0b457-c336-42ba-b06e-462de90ada91.jpg
sitemap :
  changefreq : daily
  priority : 1.0
---


# Machine Learning(ML)

## Data 주요 개념

- Data 가 가장 중요하다.
- Data 에 대해서 전처리 과정을 통해 의미 있는 Data를 학습을 해야 ML의 예측 성능이 높아진다.
- Data를 모을수록 좋다. (좋은 Data)
- Data가 편향될 수록 성능이 떨어진다.(Overfiting)
- Data를 그래프화하여 특성을 파악하는 것이 중요하다.

## 환경 셋업

1. 아나콘다 설치
2. Jetbrain PyCharm 설치
3. python 설치

## Python 

1. 인터프리터 언어(성능이 느리다) 성능 향상을 위해 NumPy lib을 사용하기도 한다.
  
  ~~~python
  a = 10
  // 변수 타입을 따로 지정하지 않음
  // 인스턴스 주소만 저장함
  ~~~
2. 간략화된 문법(배우기가 쉽다)
3. 배열이 없다.
4. ML 관련 유명 라이브러리
  - Numpy
  - Pandas(데이터 join)
  - 크롤링
  - Matplotlib(그래프)
5. 문자와 문자열 구분이 없다. (문자열은 Linked List 형태로 저장)
  - Operator
  ~~~python
  hello = 'hello'
  world = 'world'

  hello_wordl = hello + ', ' + world
  // 링크드 리스트 연결이 됨
  ~~~
  - 문자열 기본
  ~~~python
  s = 'Good morning'
  s[0:4]  // 'Good'
  s[-1] // 'g' 맨 마지막 글자
  s[2:] // 'od morning'
  s[:5] // 'Good '
  len(s) // 12 , 문자열 길이
  
  //Format api
  a = 'My name is {}. I am {} years old'.format('Mario', 20)
  // 'My name is Mario. I am 20 years old'
  e = '%10.3f'%(323.323323323) // '   323.323'
  '{:10.3f}'.format(323.3233232323)  // '   323.323'

  ~~~

  ## Jupyter notebook
  - Python을 실행해 볼수 있는 간단한 에디터
  - 아나콘다 설치 시 같이 설치가 된다.
![notebook-2](https://user-images.githubusercontent.com/35194820/67646429-77521700-f971-11e9-92cf-5ea9c71b7206.PNG)

