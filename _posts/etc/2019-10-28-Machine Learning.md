---
published: true
title: "Machine Learning 교육 정리"
excerpt : " "
layout: single
classes : wide
author_profile: false
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


### 기본 특성
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
  
  ### Jupyter notebook
  - Python을 실행해 볼수 있는 간단한 에디터
  - 아나콘다 설치 시 같이 설치가 된다.
![notebook-2](https://user-images.githubusercontent.com/35194820/67646429-77521700-f971-11e9-92cf-5ea9c71b7206.PNG)
  
  ### 문자열 
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
  ### 자료구조
  1. List
  
  > 참고로 List는 Linked List 를 사용한다.
  > 대용량 데이터를 처리 할 때 느리다.
  
  ~~~python
  a = ['jone' , 'kim', 'aaa']
  b = b = [ 1, 2.0, 'kim', True, False] // 타입에 구애 받지 않는다. 
  b[2:] // ['kim', True, False]  //String 과 마찬가지로 슬라이싱이 가능하다.
  a + b // ['jone', 'kim', 'aaa', 1, 2.0, 'kim', True, False]
  a * 2 + b * 1 // ['jone', 'kim', 'aaa', 'jone', 'kim', 'aaa', 1, 2.0, 'kim', True, False]
  a.pop() // 'aaa'

  a = [5, 4, 1, 0]
  a.sort()
  a // [0,1,4,5]
  ~~~
  
  2. Tuple
  > List와 비슷하나 수정이 불가능 하다.
  ~~~python
  a = (1,2,3,4) // 소괄호 생략 가능
  type(a) // tuple
  a[1] = 10 // Error 발생
  b = 1, // 원소가 하나인 경우
  type(b) // tuple
  // 패킹
  a  = 1,2,3
  one, two ,three = a

  //언패킹
  one // 1
  two // 2
  three // 3
  // 패킹, 언패킹은 List에서도 동일하게 적용이 된다.
  name, age, address = 'kim', 30 , 'seoul'
  ~~~
  3. Dictionary
  - Key 값과 Value를 쌍으로 저장
  - Key는 Set으로 구현이 되어있고 Value는 Collection으로 구현이 되어 있다.
  
  ~~~python
   data = {'name' : 'Kim', 'age' : 30, 'address' : 'seoul'}
   data['age'] // 30
   data['height'] = 180 // 새로운 값 삽입
   data.keys() // dict_keys(['name', 'age', 'address', 'height'])
   data.values() // dict_values(['Kim', 30, 'seoul', 180])
   data.items() // dict_items([('name', 'Kim'), ('age', 30), ('address', 'seoul'), ('height', 180)])
  ~~~
  
  4. Set
    - 저장된 순서 보장하지 않는다.
    - 접근 속도가 빠르다.
    - 중복을 허용하지 않는다.
  ~~~python
  setA = {11, 1 ,2 ,3 ,3 ,11}
  type(setA) // set
  setA //{1, 2, 3, 11}

  ~~~
