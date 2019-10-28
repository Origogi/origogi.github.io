---
published: true
title: "Machine Learning 을 위한 Pre Processing"
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

## Data 주요 개념

- Data 가 가장 중요하다.
- Data 에 대해서 전처리 과정을 통해 의미 있는 Data를 학습을 해야 ML의 예측 성능이 높아진다.
- Data를 모을수록 좋다. (좋은 Data)
- Data가 편향될 수록 성능이 떨어진다.(Overfiting)
- Data를 그래프화하여 특성을 파악하는 것이 중요하다.

## 환경 셋업

1. 아나콘다 설치
2. Jetbrain PyCharm 설치
3. Python 설치


## Python

### 기본 특성

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
  
### Jupyter notebook

- Python을 실행해 볼수 있는 간단한 에디터
- 아나콘다 설치 시 같이 설치가 된다.
![notebook-2](https://user-images.githubusercontent.com/35194820/67646429-77521700-f971-11e9-92cf-5ea9c71b7206.PNG)
  
### 문자열

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

### 자료구조


#### 1. List
  
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
  
#### 2. Tuple

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

#### 3. Dictionary

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
  
#### 4. Set

- 저장된 순서 보장하지 않는다.
- 접근 속도가 빠르다.
- 중복을 허용하지 않는다.

~~~python
setA = {11, 1 ,2 ,3 ,3 ,11}
type(setA) ## set
setA #{1, 2, 3, 11}
~~~

### 흐름 제어


- `None` : 값이 없음(empty)을 의미
  
#### 1. 코드 블록(Code Block)
  
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

#### 2. 반복문

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

### 함수


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

#### 1. 가변 매개변수

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

#### 2. 함수 포인터

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

### 객제 지향 프로그래밍


#### 1. 생성자

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

위 코드를 아래와 같이 수정하면 의도대로 동작한다.

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

#### 2. Class Private Member

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

#### 3. Inheritance

~~~python
class AA :
    def __init__(self):
        print('AA init call')

class BB(AA) :
    def __init__(self):
        print('BB init call')

bb = BB() # BB init call
~~~

위 코드와 같이 부모 클래스의 생성자를 명시적으로 호출하지 않으면 자식 클래스의 생성자만 호출이 된다.

~~~python
class BB(AA) :
    def __init__(self):
        super().__init__()
        print('BB init call')

bb = BB()

# AA init call
# BB init call
~~~

귀찮더라도 부모 생성자를 꼭 호출하도록 하자

#### 4. Iteration

~~~python
iterator = range(3).__iter__()

iterator.__next__() # 0
iterator.__next__() # 1
iterator.__next__() # 2
iterator.__next__() # exception!!

~~~

##### Custom Iteration

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

#### 5. Generator

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
