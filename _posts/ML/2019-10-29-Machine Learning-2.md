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

# Machine Learning(ML)

Python을 이용한 ML Pre Processing 강의를 정리한 POST입니다.

## 1. PyCharm

![ss](assets/images/2019-10-29-08-29-13.png)

### 1.1 프로젝트 생성 방법

1. `File` -> `New Project`
2. `more` 메뉴 클릭
![ㅇㅇ](assets/images/2019-10-29-08-30-45.png)

3. 미리 설치한 `interpreter`를 선택
![ㅇㅇ](assets/images/2019-10-29-08-33-32.png)

4. `create` 클릭

5. `File` -> `Python File`

6. 코드 작성 후 `run`을 하기 위해서 `Alt` + `Shift` + `F10`

## 2. Iterator

### 2.1 Comprehension

~~~python
result = []

# For loop 방식
for i in range(10) :
    result.append(i)
print(result)

# Comprehension 방식
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

### 2.2 Emurate

~~~python
for i,v in enumerate(data) :
    print(i,v)

str = 'Team is an academic institude located in south korea'.split()

result = {i:j for i,j in enumerate(str)}
print(result)
~~~

### 2.3 Zip

~~~python
a = (1,2,3)
b = 10,20,30
c = 100, 200, 300

result2 = [x+y+z for x,y,z in zip(a,b,c)]
print(result2)

# x는 tuple로 받고 x를 sum 함수를 통해 tuple의 합을 구한다.
result3 = [sum(x) for x in zip(a,b,c)]
print(result3)
~~~

## 3. File 입출력

### 3.1 File read/write 기본

~~~python
file = open('text.txt', 'w')

file.write('hello')
file.close()

file2 = open('text.txt', 'r')

str = file2.read()
print(str)
file2.close()
~~~

### 3.2 Iterator 사용

~~~python
def displayForLoop(file) :
    infile = open(file, 'r')
    for line in infile :
        print(line, end= '')
    infile.close()

def displayWithComprehension(file) :
    infile = open(file,'r')
    # new line 제거
    listData = [line.rstrip() for line in infile]
    infile.close()
    return listData

filepath = 'FirstPresidents.txt'
displayForLoop(filepath)
print(displayWithComprehension(filepath))
~~~

### file을 list로 read 한 후 정렬한다음 write

~~~python
def readListFromFile(file) :
    infile = open(file,'r')
    # new line 제거
    listData = [line for line in infile]
    infile.close()
    return listData

def writeListToFile(list, file) :
    outfile = open(file + '_sorted','w')
    outfile.writelines(list) # 한번에 list를 write 할 수 있음
    outfile.close()

file = 'States.txt'
list = readListFromFile(file)
list.sort()
writeListToFile(list,file)
~~~

## 4. 집합 API(Set)

~~~python
infile = open('File1.txt', 'r')

firstSet = {line for line in infile}
infile.close()

infile = open('File2.txt', 'r')

secondSet = {line for line in infile}
infile.close()

# 합집합
outfile = open('Union.txt', 'w')
outfile.writelines(firstSet.union(secondSet))

# 교집합
outfile = open('Intersection.txt', 'w')
outfile.writelines(firstSet.intersection(secondSet))

# 차집합
outfile = open('Difference.txt', 'w')
outfile.writelines(firstSet.difference(secondSet))
~~~

## 5. Lambda

~~~python
#람다 식을 이용해 함수를 축약해서 사용 가능하다.
f = lambda x,y : x+y

def f2(x,y) :
    return x+ y

print(f(10,20))
print(f2(10,20))

ex = [1,2,3,4,5]
print(list(map(lambda x:x**2, ex)))
~~~

## 6. 바이너리 파일

### 6.1 pack(), unpack()

~~~python
import struct

packed = struct.pack('i', 256) # data => bytes

# integer는 4byte이고 byte 단위로 출력한다.
for i in packed:
    print(i)

unpacked = struct.unpack('i',packed) # bytes => tuple(data)
print(unpacked) # (256,)
~~~