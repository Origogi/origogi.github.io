---
published: true
title: "Machine Learning 을 위한 Pre Processing - Quiz 정리"
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
 - Machine Learning
tags: 
  - ML
  - Machine Learning
  - Python
  - Pandas
  - Matplotlib
comments: true
header:
  overlay_filter: 0.5
  # overlay_image: https://matplotlib.org/_static/logo2_compressed.svg
  # teaser: https://matplotlib.org/_static/logo2_compressed.svg
sitemap :
  changefreq : daily
  priority : 1.0
---

# Quiz 정리

## Day 1

### 1번 문제

~~~
1.40시간 초과 근무를 하면, 1.5배의 금액을 지불하는 어떤 사람의 주급을 계산하는 프로그램
을 작성하라. main 함수는 다음 3개의 함수를 호출해야 한다.

1)입력 함수
2)값 계산 함수
3)출력 함수

ex> 
	Enter hours worked : 42
	Enter hourly pay: 10.00
	Week's pay:$430.00
~~~

### 모범 답안

<details>
<summary>접기/펼치기</summary>
<div markdown="1">

~~~python
def getWageAndHours():
    hoursworked = eval(input("Enter hours worked: "))
    hourlyWage = eval(input("Enter hourly pay: "))
    return(hourlyWage, hoursworked)


def pay(wage, hours):
    if hours <= 40:
        amount = wage * hours
    else:
        amount = (wage * 40) + ((1.5) * wage * (hours - 40))
    return amount

def displayEarnings(payForWeek):
    print("Week’s pay: ${0:,.2f}".format(payForWeek))


(wage, hours) = getWageAndHours()
payForWeek = pay(wage, hours)
displayEarnings(payForWeek)
~~~

</div>
</details>



### 2번 문제

~~~
2.쇼핑 웹사이트에서 사용자가 카트에 담은 항목을 확인하는 프로그램을 작성하여라.
이 프로그램은 구매한 단일 품목의 정보를 유지하기 위해 Purchase 클래스를 사용하고 
Purchase 클래스의 객체 목록을 유지하기 위해 Cart클래스를 사용해야한다.

ex>
	Enter description of article : shirt
	Enter price of article:35
	Enter quantity of article:3
	Do you want to enter more articles (Y/N)? Y
	Enter description of article : tie
	Enter price of article:15
	Enter quantity of article:2
	Do you want to enter more articles (Y/N)? N

	ARTICLE      PRICE     QUANTITY
	shirt	    $35.00  	  3
	tie	    $15.00	  2

	TOTAL COST: $135.00
~~~

### 모범 답안

~~~python
class Purchase:
    def __init__(self, description="", price=0, quantity=0):
        self._description = description
        self._price = price
        self._quantity = quantity
        
    def setDescription(self, description):
        self._description = description
        
    def getDescription(self):
        return self._description
    
    def setPrice(self, price):
        self._price = price
        
    def getPrice(self):
        return self._price
    
    def setQuantity(self, quantity):
        self._quantity = quantity
        
    def getQuantity(self):
        return self._quantity


class Cart:
    def __init__(self, items=[]):
        self._items = items
        
    def addItemToCart(self, item):
        self._items.append(item)
        
    def getItems(self):
        return self._items
    
    def calculateTotal(self):
        amount = 0
        for item in self._items:
            amount += item.getPrice() * item.getQuantity()
        return amount


def printReceipt(myPurchases):
    print("\n{0:12} {1:<s} {2:<12}".format("ARTICLE", "PRICE", "QUANTITY"))

    for purchase in myPurchases.getItems():
        print("{0:12s} ${1:,.2f} {2:5}".format(purchase.getDescription(),purchase.getPrice(), purchase.getQuantity()))

    print("\nTOTAL COST: ${0:,.2f}".format(myPurchases.calculateTotal()))


myPurchases = Cart()
carryOn = 'Y'

while carryOn.upper() == 'Y':
    description = input("Enter description of article: ")
    price = float(input("Enter price of article: "))
    quantity = int(input("Enter quantity of article: "))
    article = Purchase(description, price, quantity)
    myPurchases.addItemToCart(article)
    carryOn = input("Do you want to enter more articles (Y/N)? ")

printReceipt(myPurchases)
~~~

## Day 2

### 1번 문제

~~~
1990년 초에 크레욜라 크레용은 72개의 색상(Pre1990.txt)을 갖고 있었다. 1990년대  8색상(Retired.txt)이 
  빠지게 되었고 56개의 새로운 색상(Added.txt)이 추가되었다. 지난 1990년대 119개의 크레용 색상을 알파벳 
  순서로 나열한 텍스트 파일을 생성하는 프로그램을 작성하라.
~~~

### 모범 답안

~~~python
def getSetOfNewColors():
    infile = open("Pre1990.txt", 'r')
    colors = {line.rstrip() for line in infile}
    infile.close()
    infile = open("Retired.txt", 'r')
    retiredColors = {line.rstrip() for line in infile}
    infile.close()
    infile = open("Added.txt", 'r')
    addedColors = {line.rstrip() for line in infile}
    infile.close()
    colorSet = colors.difference(retiredColors)
    colorSet = colorSet.union(addedColors)
    return colorSet


def createFileOfNewColors(setOfNewColors):
    orderedListOfColors = sorted(setOfNewColors)
    orderedListOfColorsString =('\n').join(orderedListOfColors)
    outfile = open("NewColors.txt", 'w')
    outfile.write(orderedListOfColorsString)
    outfile.close()

setOfNewColors = getSetOfNewColors()
createFileOfNewColors(setOfNewColors)

~~~

### 2번 문제

~~~
Justices.txt를 참고하라.(이름, 성, 임명한 대통령, 임명 당시 재직한 주, 임명 연도, 대법원을 사임한 연도)
  대통령의 이름을 입력 받은 후 해당 대통령이 임명한 대법관을 표시하는 프로그램을 작성하라. 
  대법관은 해당 법원에서 근무한 기간에 의해 내림 차순으로 정렬되어야 한다.
  (대법관 사임한 연도가 0인 경우 2015로 대체하여 사용한다)

ex>
	Enter the name of a president:George W. Bush
	Justice Appointed:
		John Roberts
		Smuel Alito
~~~

### 모범 답안

~~~python
def getJusticesByPresident(president):
    infile = open("Justices.txt", 'r')
    listOfRecords = [line for line in infile if line.split(',')[2] == president]
    infile.close()
    for i in range(len(listOfRecords)):
        listOfRecords[i] = listOfRecords[i].split(',')
        listOfRecords[i][4] = int(listOfRecords[i][4])
        listOfRecords[i][5] = int(listOfRecords[i][5])
    return listOfRecords

def fixCurrentJustices(justices):
    for justice in justices:
        if justice[5] == 0:
            justice[5] = 2015

president = input("Enter the name of a president: ")
justices = getJusticesByPresident(president)
fixCurrentJustices(justices)
justices.sort(key=lambda justice: justice[5] - justice[4], reverse=True)
if len(justices) > 0:
    print("Justices Appointed:")
    for justice in justices:
        print(" " + justice[0] + " " + justice[1])
else:
    print(president, "did not appoint any justices.")
~~~

## Day 3

### 1번 문제

~~~
1. 5 X 6 형태의 데이터 행렬을 만들고 이 데이터에 대해 다음과 같은 값을 구한다.

   a. 전체의 최대 값
   b. 각 행의 합
   c. 각 열의 평균
   d. 첫 번째 열 값으로 모든 행으로 정렬
   f. 두 번째 행 값으로 모든 열을 정렬
~~~

### 모범 답안

~~~python
import numpy as np


a = np.arange(30).reshape(5,6)
print(a,'\n')

#1
print(a.max(),'\n')

#2
print(a.sum(axis=1),'\n')

#3
print(a.mean(axis=0),'\n')

#4
print(a[np.argsort(a[:,0])],'\n')

#5
print(a[:,np.argsort(a[1,:])],'\n')
~~~

### 2번 문제

~~~
2. 두 개의 데이터프레임을 만들고 merge 명령으로 합친다. 단 데이터프레임은 다음 조건을 만족해야 한다

  a.각각 5 x 5 이상의 크기를 가진다.

  b.공통 열을 하나 이상 가진다. 다만 공통 열의 이름은 서로 다르다.
~~~

### 모범 답안

~~~python
import pandas as pd

df_1 = pd.DataFrame({
    '학교': ['서울대', '서강대', '부산대', '충남대', '경북대'],
    '지역': ['서울', '서울', '부산', '대전','대구'],
    '학생수':['10,712','8,179','11,935','10,991','11,314'],
    '학과 수': ['56', '45', '58','51','59'],
    '평균등록금' : ['2,110,000','5,340,000','2,430,000','2,347,000','2,522,000']},\
    columns=['학교', '지역', '학생수', '학과 수', '평균등록금'])
print(df_1,'\n')

df_2 = pd.DataFrame({
    '학교평가등급': ['B+', 'A', 'A', 'A-', 'A+'],
    '학생만족도':['81점','79점','77점','84점','82점'],
    '취업자수': ['5,122명', '4,987명', '5,001명','5,117명','4,777명'],
     'area': ['서울', '서울', '부산', '대전','대구'],
    '면적' : ['9,122','7,101','9,095','8,873','9,002']
})

print(df_2,'\n')

print(pd.merge(df_1, df_2, left_on = '지역', right_on = 'area'),'\n')

df_3 = pd.merge(df_1, df_2, left_on = '지역', right_on = 'area')
df_3 = df_3.drop(['area'], axis=1)
print(df_3,'\n')
~~~

### 3번 문제

~~~
어느 회사의 전반기(1월 ~ 6월) 실적을 나타내는 데이터프레임과 후반기(7월 ~ 12월) 실적을 나타내는 데이터프레임을 
   
    만든 뒤 합친다. 실적 정보는 "매출", "비용", "이익" 으로 이루어진다. (이익 = 매출 - 비용).

    또한 1년간의 총 실적을 마지막 행으로 덧붙인다.

  df1 = pd.DataFrame({
    '매출' : [10000000, 12000000, 9000000, 6000000, 8000000, 1100000],
    '비용' : [15000000, 1300000, 1200000, 9000000, 9900000, 9500000]},
    index = ['1월', '2월', '3월', '4월', '5월', '6월'])

 df2 = pd.DataFrame({
    '매출' : [13000000, 14000000, 17000000, 15400000, 16500000, 16600000],
    '비용' : [11000000, 10400000, 11000000, 12100000, 9000000, 9500000]},
    index = ['7월', '8월', '9월', '10월', '11월', '12월'])

~~~

### 모범 답안

~~~python
import pandas as pd

df_1 = pd.DataFrame({
    '매출' : [9834, 11923, 17342, 7121, 31082, 28094],
    '비용' : [6523, 6129, 16199, 3254, 10883, 19453]},
    index = ['1월', '2월', '3월', '4월', '5월', '6월'])
print(df_1,'\n')

df_2 = pd.DataFrame({
    '매출' : [23340, 17984, 11712, 15987, 8912, 12189],
    '비용' : [22100, 11106, 7690, 17891, 7436, 14780]},
    index = ['7월', '8월', '9월', '10월', '11월', '12월'])
print(df_2,'\n')
df_3 = pd.concat([df_1, df_2])
print(df_3,'\n')

df_4 = pd.concat([df_3, pd.DataFrame
                  ({'이익': df_3['매출'] - df_3['비용']})], axis=1)
df_4 = pd.concat([df_4, pd.DataFrame
                  ({'매출': df_4['매출'].sum(),
                    '비용': df_4['비용'].sum(),
                    '이익': df_4['이익'].sum()},
                   index=['실적'])], axis=0)
print(df_4,'\n')
~~~

## Day 4

### 1번 문제

~~~
1. A 반 학생 5명과 B반 학생 5명의 국어, 영어, 수학 점수를 나타내는 데이터프레임 df_score를 다음과 같이 만든다.

   a. "반", "번호", "국어", "영어", "수학" 을 열로 가지는 데이터프레임을 만든다.

   b. 1차 행 인덱스로 "반"을 2차 행 인덱스로 "번호"을 가지는 데이터프레임으로 변형한다.

   c. 위 데이터 프레임에 각 학생의 평균을 나타내는 행을 오른쪽에 추가한다.

   d. 행 인덱스로 "번호"을, 1차 열 인덱스로 "국어", "영어", "수학"을, 2차 열 인덱스로 "반"을 가지는 
      데이터프레임으로 변형한다.
 
   e. 위 데이터 프레임에 각 반별 각 과목의 평균을 나타내는 행을 아래에 추가한다.
~~~

### 모범 답안

~~~python
import pandas as pd

# a
df_score = pd.DataFrame({
        '반' : ['A', 'A', 'A', 'A', 'A', 'B', 'B', 'B', 'B', 'B'],
        '번호' : [1, 2, 3, 4, 5, 1, 2, 3, 4, 5],
        '국어' : [90, 80, 90, 70, 100, 80, 90, 100, 70, 80],
        '영어' : [100, 90, 100, 80, 70, 90, 100, 70, 80, 90],
        '수학' : [80, 100, 80, 90, 80, 100, 70, 80, 90, 100]},
        columns = ["반", "번호", "국어", "영어", "수학"]
    )
print(df_score,'\n')

# b
df1 = df_score.set_index(["반", "번호"])
print(df1,'\n')

# c
df1["평균"] = df1.mean(axis = 1)
print(df1,'\n')

# d
df2 = df_score.set_index(["반", "번호"])
df3 = df2.unstack("반")
print(df3,'\n')

# e
df3.loc["평균", :] = df3.mean()
print(df3,'\n')
~~~