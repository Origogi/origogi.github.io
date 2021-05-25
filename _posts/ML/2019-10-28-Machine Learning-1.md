---
published: true
title: "Machine Learning 을 위한 Pre Processing - Python"
excerpt : " "
layout: single
# classes : wide
author_profile: false
read_time: false # read_time을 출력할지 여부 1min read 같은것!
toc: true #Table Of Contents 목차 보여줌
toc_label: "My Table of Contents" # toc 이름 정의
toc_icon: "cog" #font Awesome아이콘으로 toc 아이콘 설정
toc_sticky: true # 스크롤 내릴때 같이 내려가는 목차
categories :
 - ML
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

# Python

![logo](https://i.redd.it/qvlxqyp8x1311.png)

## 1. 기본 특성

- 인터프리터 언어(성능이 느리다) 성능 향상을 위해 NumPy lib을 사용하기도 한다.
- 간략화된 문법(배우기가 쉽다)
- 배열이 없다.
- ML 관련 유명 라이브러리
  - Numpy
  - Pandas(데이터 join)
  - 크롤링
  - Matplotlib(그래프)

- 문자와 문자열 구분이 없다. (문자열은 Linked List 형태로 저장)
  - Operator
  
~~~python
hello = 'hello'
world = 'world'
hello_wordl = hello + ', ' + world
# 링크드 리스트 연결이 됨
~~~

## 2. Tool

### 2.1 Jupyter notebook

- Python을 실행해 볼수 있는 간단한 에디터
- 아나콘다 설치 시 같이 설치가 된다.
![notebook-2](https://user-images.githubusercontent.com/35194820/67646429-77521700-f971-11e9-92cf-5ea9c71b7206.PNG)
  
#### 2.2 PyCharm

![ss](/assets/images/2019-10-29-08-29-13.png)

##### 프로젝트 생성 방법

1. `File` -> `New Project`
2. `more` 메뉴 클릭
![ㅇㅇ](/assets/images/2019-10-29-08-30-45.png)

3. 미리 설치한 `interpreter`를 선택
![ㅇㅇ](/assets/images/2019-10-29-08-33-32.png)

4. `create` 클릭

5. `File` -> `Python File`

6. 코드 작성 후 `run`을 하기 위해서 `Alt` + `Shift` + `F10`

### 3. Python 문법

#### 3.1 문자열

~~~python
s = 'Good morning'
s[0:4]  # 'Good'
s[-1] # 'g' 맨 마지막 글자
s[2:] # 'od morning'
s[:5] # 'Good '
len(s) # 12 , 문자열 길이

#Format api
a = 'My name is {}. I am {} years old'.format('Mario', 20)
# 'My name is Mario. I am 20 years old'
e = '%10.3f'%(323.323323323) # '   323.323'
'{:10.3f}'.format(323.3233232323)  # '   323.323'
~~~

### 3.2 자료구조

#### 3.2.1 List
  
  > 참고로 List는 Linked List 를 사용한다.
  > 대용량 데이터를 처리 할 때 느리다.
  
~~~python
a = ['jone' , 'kim', 'aaa']
b = b = [ 1, 2.0, 'kim', True, False] # 타입에 구애 받지 않는다.
b[2:] # ['kim', True, False]  #String 과 마찬가지로 슬라이싱이 가능하다.
a + b # ['jone', 'kim', 'aaa', 1, 2.0, 'kim', True, False]
a * 2 + b * 1 # ['jone', 'kim', 'aaa', 'jone', 'kim', 'aaa', 1, 2.0, 'kim', True, False]
a.pop() # 'aaa'

a = [5, 4, 1, 0]
a.sort()
a # [0,1,4,5]
~~~
  
#### 3.2.2 Tuple

  > List와 비슷하나 수정이 불가능 하다.

~~~python
a = (1,2,3,4) # 소괄호 생략 가능
type(a) # tuple
a[1] = 10 # Error 발생
b = 1, # 원소가 하나인 경우
type(b) # tuple
# 패킹
a  = 1,2,3
one, two ,three = a

#언패킹
one # 1
two # 2
three # 3
# 패킹, 언패킹은 List에서도 동일하게 적용이 된다.
name, age, address = 'kim', 30 , 'seoul'
~~~

#### 3.2.3 Dictionary

- Key 값과 Value를 쌍으로 저장
- Key는 Set으로 구현이 되어있고 Value는 Collection으로 구현이 되어 있다.
  
~~~python
 data = {'name' : 'Kim', 'age' : 30, 'address' : 'seoul'}
 data['age'] # 30
 data['height'] = 180 # 새로운 값 삽입
 data.keys() ## dict_keys(['name', 'age', 'address', 'height'])
 data.values() ## dict_values(['Kim', 30, 'seoul', 180])
 data.items() ## dict_items([('name', 'Kim'), ('age', 30), ('address', 'seoul'), ('height', 180)])
~~~
  
#### 3.2.4 Set

- 저장된 순서 보장하지 않는다.
- 접근 속도가 빠르다.
- 중복을 허용하지 않는다.

~~~python
setA = {11, 1 ,2 ,3 ,3 ,11}
type(setA) ## set
setA #{1, 2, 3, 11}
~~~

### 3.3 흐름 제어

- `None` : 값이 없음(empty)을 의미
  
#### 3.3.1 코드 블록(Code Block)
  
- 여러 코드가 이루는 일정한 구역

> python은 들여쓰기로 구분 되기 때문에 들여쓰기를 유의해서 작성하자

~~~python
print('수를 입력하시오')

a = int(input())

if a == 0:
    print('0은 나눗셈에 이용할 수 없습니다.')
else :
    print('3/', a, '=', 3/a)

num = int(input('숫자를 입력하시오'))

if (num > 90) :
    print('num >90')
elif (num > 80):
    print('num >80')
else :
    print('num<=80')
~~~

#### 3.3.2 반복문

> for `반복변수` in `순서열` :
> `코드 블록`
>
> `순서열`은 `List`, `Tuple`, `Dictionary` 가 될수 있다.

~~~python
for i in ['one', 'two', 'three', 'for'] :
  print(i)

for i in 'name', 'age', 'address' :
    print(i)

# (초기값, 종료값, 증강값)
for i in range(0,10,1) :
  print(i)

# 초기값, 증강값 생략 가능
for i in range(7) :
    print(i)

dic = {'애플' : 'www.apple.com',
      '파이썬' : 'www.python.org',
      '마이크로소프트' : 'www.microsoft.com'}

for k,v in dic.items() :
    print('{} : {}'.format(k,v))

~~~

### 3.4 함수

- 함수를 정의할 때 `def` 키워드를 사용한다.

~~~python
def hello() :
  print('hello')

def add(a,b) :
    return a +b

add(10,20) #30
add ('hello ' , 'world') # hello world

def print_personnel(name, position = 'staff', nationality = 'Korea') :
    print('name = {}'.format(name))
    print('position = {}'.format(position))
    print('nationality = {}'.format(nationality))

print_personnel(name = '박상현')
# name = 박상현
# position = staff
# nationality = Korea

print_personnel(name = '박상현', position = '인턴')
# name = 박상현
# position = 인턴
# nationality = Korea

# 인자의 순서도 변경 가능
print_personnel(position = '인턴', name = '박상현' )
# name = 박상현
# position = 인턴
# nationality = Korea
~~~

> Python은 리턴 타입을 명시 하지 않는다. 왜냐하면 변수 자체에 타입이 없기 때문
> 함수 인자 타입에 따른 함수 오버로드도 의미가 없다. 함수 인자도 타입이 없기 때문

#### 3.4.1 가변 매개변수

~~~python
def add (*data) :
    result = 0
    for d in data :
        result += d
    return result

add(4,5,1) #10
add(2,3) # 5

def print_team(**players) :
    for k in players.keys() :
        print('{} = {}'.format(k, players[k]))

print_team(카시야스='GK', 호날두='FW', 알론소='MF', 페페='DF')
# 카시야스 = GK
# 호날두 = FW
# 알론소 = MF
# 페페 = DF
~~~

#### 3.4.2 함수 포인터

~~~python
def plus(a,b) :
    return a + b
def minus(a,b) :
    return a- b

func = [plus, minus]
func[0](2,3) # 5

func[1](2,3) # -1

def hello_korean() :
    print('안녕하세요')

def hello_english() :
    print('hello')

def get_greeting(where) :
    if where == 'K' :
        return hello_korean
    else :
        return hello_english

hello = get_greeting('K')
hello() # 안녕하세요

hello = get_greeting('E')
hello() # hello
~~~

### 3.5 객제 지향 프로그래밍

#### 3.5.1 생성자

~~~python
class Car :
  # 생성자는 __init__
  # 자기 자신(this)은 self
def __init__(self):
    print('init call')
    self.color = 'RED'
    self.wheel_size = 16
    self.displacement = 2000
def info_print(self) :
  print('color : {}, wheel size : {}'.format  (self.color, self.wheel_size))

a = Car() # init call
a.info_print() # color : RED, wheel size : 16
~~~

만약 맴버 변수를 `__init__` 이 아닌 바깥 스코프에 선언하면 어떻게 동작할까?

~~~python
class InstanceC :
    text_list = []

    def __init__(self) :
        print('init')
    def add(self, text):
        self.text_list.append(text)
    def print_list(self) :
        print(self.text_list)

a = InstanceC()
a.add('kim')
a.print_list() # ['kim']

b = InstanceC()
b.add('Lee')
b.print_list() # ['kim', 'Lee']
# text_list 가 static 으로 동작한다.
~~~

- 위 코드를 아래와 같이 수정하면 의도대로 동작한다.

~~~python
class InstanceC :
    def __init__(self) :
        self.text_list = []
        print('init')
    def add(self, text):
        self.text_list.append(text)
    def print_list(self) :
        print(self.text_list)

a = InstanceC()
a.add('kim')
a.print_list() # ['kim']

b = InstanceC()
b.add('Lee')
b.print_list() # ['Lee']
~~~

#### 3.5.2 Class Private Member

~~~python
class HasPrivate :
    def __init__(self) :
        self.pubValue = 'public value'
        self.__priValue = 'private value'

    def print_data(self) :
        print(self.pubValue)
        print(self.__priValue)

obj = HasPrivate()
obj.print_data()
# public value
# private value

obj.pubValue
obj.__priValue # error 발생!!
~~~

#### 3.5.3 Inheritance

~~~python
class AA :
    def __init__(self):
        print('AA init call')

class BB(AA) :
    def __init__(self):
        print('BB init call')

bb = BB() # BB init call
~~~

- 위 코드와 같이 부모 클래스의 생성자를 명시적으로 호출하지 않으면 자식 클래스의 생성자만 호출이 된다.
귀찮더라도 아래와 같이 부모 생성자를 꼭 호출하도록 하자

~~~python
class BB(AA) :
    def __init__(self):
        super().__init__()
        print('BB init call')

bb = BB()

# AA init call
# BB init call
~~~

### 3.6 Iteration

~~~python
iterator = range(3).__iter__()

iterator.__next__() # 0
iterator.__next__() # 1
iterator.__next__() # 2
iterator.__next__() # exception!!

~~~

#### 3.6.1 Custom Iteration

~~~python
class MyRange:
    def __init__(self, start, end):
        self.current = start
        self.end = end
    def __iter__(self):
        return self
    def __next__(self):
        if self.current < self.end :
            current = self.current;
            self.current += 1
            return current
        else :
            raise StopIteration()

for i in MyRange(0,5):
    print(i) # o 1 2 3 4
~~~

### 3.7 Generator

~~~python
def YourRange(start, end):
    current = start
    while current <end:
        yield current
        current += 1
    return

for i in YourRange(0,5) :
    print(i)
~~~

### 3.8 Iterator

#### 3.8.1 Comprehension

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

#### 3.8.2 Emurate

~~~python
for i,v in enumerate(data) :
    print(i,v)

str = 'Team is an academic institude located in south korea'.split()

result = {i:j for i,j in enumerate(str)}
print(result)
~~~

### 3.8.3 Zip

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

### 3.9 File 입출력

#### 3.9.1 File read/write 기본

~~~python
file = open('text.txt', 'w')

file.write('hello')
file.close()

file2 = open('text.txt', 'r')

str = file2.read()
print(str)
file2.close()
~~~

#### 3.9.2 Iterator 사용

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

#### 3.9.3 file을 list로 read 한 후 정렬한다음 write

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

### 3.10 집합 API(Set)

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

### 3.11 Lambda

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

### 3.12 바이너리 파일

#### 3.12.1 pack(), unpack()

~~~python
import struct

packed = struct.pack('i', 256) # data => bytes

# integer는 4byte이고 byte 단위로 출력한다.
for i in packed:
    print(i)

unpacked = struct.unpack('i',packed) # bytes => tuple(data)
print(unpacked) # (256,)
~~~

#### 3.12.2 Pickle library

- Pickle library를 이용하여 쉽게 바이너리 파일을 read/write 할 수 있다.

~~~python
import pickle

outfile = open('list.pickle', 'wb')
data = [1,2,3,4,5]
pickle.dump(data,outfile)
outfile.close()

infile = open('list.pickle', 'rb')
rData = pickle.load(infile)

print(rData)
~~~
