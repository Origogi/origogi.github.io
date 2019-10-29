---
published: true
title: "Machine Learning 을 위한 Pre Processing (2)"
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
comments: true
header:
  overlay_filter: 0.5
  overlay_image: https://images.velog.io/post-images/leejh3224/bcb7de70-fc50-11e8-a0af-6d805f1eb60b/-2018-12-10-4.53.32.png
  teaser: https://images.velog.io/post-images/leejh3224/bcb7de70-fc50-11e8-a0af-6d805f1eb60b/-2018-12-10-4.53.32.png
sitemap :
  changefreq : daily
  priority : 1.0
---

## Machine Learning(ML)

Python을 이용한 ML Pre Processing 강의를 정리한 POST입니다.

## PyCharm

![ss](2019-10-29-08-29-13.png)

### 프로젝트 생성 방법

1. File -> New Project
2. `more` 메뉴 클릭
![ㅇㅇ](2019-10-29-08-30-45.png)

3. 미리 설치한 `interpreter`를 선택
![ㅇㅇ](2019-10-29-08-33-32.png)

4. `create` 클릭

5. `File` -> `Python File`

6. 코드 작성 후 `run`을 하기 위해서 `Alt` + `Shift` + `F10`

#### Sample code

~~~python
result = []

# List를 생성하는 방법
for i in range(10) :
    result.append(i)
print(result)

# Compression 방식
result2 = [i  for i in range(10)]
print(result2)

result3 = []
for i in range(10) :
    if i % 2 == 0 :
        result3.append(i)
print(result3)

result4 = [i  for i in range(10) if i %2 ==0]
print(result4)

result5 =[]
# else 문이 사용할 경우 if else 문을 앞에서 선언해야 한다.
result5 = [i  if i %2 ==0 else 10 for i in range(10) ]
print(result5)
# [0, 10, 2, 10, 4, 10, 6, 10, 8, 10]


data1 = 'hello'
data2 = 'world'
result6 = [i+j for i in data1 for j in data2 if not (i==j)]
print(result6)

words = 'the quick brown for jumps over the lazy dog'.split()
print(words)

result7 = [[w.upper(), w.lower(), len(w) ] for w in words]
print(result7)
~~~

> Compression 방식을 사용하면 가독성이 떨어지지만 처리 속도가 빨라진다.

