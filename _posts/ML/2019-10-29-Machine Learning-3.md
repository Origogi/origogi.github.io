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
  overlay_image: https://pandas.pydata.org/_static/pandas_logo.png
  teaser: https://pandas.pydata.org/_static/pandas_logo.png
sitemap :
  changefreq : daily
  priority : 1.0
---

# Pandas

![logo](https://miro.medium.com/max/1200/1*93CVLqnQESmvfOhzvYUgQw.png)

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

## 2. DataFrame

- 표 값은 스프레드시트 형식의 자료 구조
- 2 차원 데이터

![dataframe](https://www.tutorialspoint.com/python_pandas/images/structure_table.jpg)

### 2.1 DataFrame 생성

~~~python
import pandas as pd
import numpy as np

df = pd.DataFrame(np.arange(8).reshape(4, 2),
                  index=['a', 'b', 'c', 'd'],
                  columns=['one', 'two'])

print(df)

data = {'state': ['Ohio', 'Ohio', 'Ohio', 'Nevada', 'Nevada', 'Nevada'],
        'year': [200, 2001, 2002, 2001, 2002, 2003],
        'pop': [1.5, 1.7, 3.6, 2.4, 2.9, 3.2]}

frame = pd.DataFrame(data)
print('\n',frame)

frame2 = pd.DataFrame(data, columns=['year', 'state', 'pop'])
print('\n',frame2)

frame3 = pd.DataFrame(data, columns=['year', 'state', 'pop', 'debt'],
                      index=['one', 'two','three','four', 'five', 'six'])
print('\n',frame3)

print('\n',frame3.year) # Series를 리턴한다.
print('\n',frame3['year']) # 위와 동일한 결과를 가져온다.
print('\n', frame3.loc['three']) # Series를 리턴한다.

'''
   one  two
a    0    1
b    2    3
c    4    5
d    6    7

     state  year  pop
0    Ohio   200  1.5
1    Ohio  2001  1.7
2    Ohio  2002  3.6
3  Nevada  2001  2.4
4  Nevada  2002  2.9
5  Nevada  2003  3.2

    year   state  pop
0   200    Ohio  1.5
1  2001    Ohio  1.7
2  2002    Ohio  3.6
3  2001  Nevada  2.4
4  2002  Nevada  2.9
5  2003  Nevada  3.2

        year   state  pop debt
one     200    Ohio  1.5  NaN
two    2001    Ohio  1.7  NaN
three  2002    Ohio  3.6  NaN
four   2001  Nevada  2.4  NaN
five   2002  Nevada  2.9  NaN
six    2003  Nevada  3.2  NaN

 one       200
two      2001
three    2002
four     2001
five     2002
six      2003
Name: year, dtype: int64

 one       200
two      2001
three    2002
four     2001
five     2002
six      2003
Name: year, dtype: int64

 year     2002
state    Ohio
pop       3.6
debt      NaN
Name: three, dtype: object
...
~~~

## 3. 핵심 기능

### 3.1 Reindexing

~~~python
import pandas as pd
import numpy as np

obj = pd.Series([4, 7, -5, 3], index=list('dbac'))
print(obj)

obj2 = obj.reindex(['a', 'b', 'c', 'd', 'e'])
print('\n', obj2)

obj3 = pd.Series(['blue', 'purple', 'yellow'], index=[0, 2, 4])
print('\n', obj3)
print('\n', obj3.reindex(np.arange(6), method='ffill'))  # NaN 값을 바로 앞에 있는 값으로 대체

frame = pd.DataFrame(np.arange(9).reshape(3, 3),
                     index=list('acd'),
                     columns=['Ohio', 'Texas', 'Califonia'])

print('\n', frame)
print('\n', frame.reindex(['a', 'b', 'c', 'd']))
print('\n', frame.reindex(columns=['Texas', 'Utah', 'Ohio']))

'''
d    4
b    7
a   -5
c    3
dtype: int64

 a   -5.0
b    7.0
c    3.0
d    4.0
e    NaN
dtype: float64

 0      blue
2    purple
4    yellow
dtype: object

 0      blue
1      blue
2    purple
3    purple
4    yellow
5    yellow
dtype: object

    Ohio  Texas  Califonia
a     0      1          2
c     3      4          5
d     6      7          8

    Ohio  Texas  Califonia
a   0.0    1.0        2.0
b   NaN    NaN        NaN
c   3.0    4.0        5.0
d   6.0    7.0        8.0

    Texas  Utah  Ohio
a      1   NaN     0
c      4   NaN     3
d      7   NaN     6
'''

~~~

### 3.2 데이터 삭제(제외)

- Row나 Column을 제외하기 위해 Drop api를 사용한다.
- 원본은 변경이 되지 않는다.

~~~python
import pandas as pd
import numpy as np

obj = pd.Series(np.arange(5.), index=['a','b','c', 'd', 'e'])
print(obj)

new_obj = obj.drop('c')
print('\n', new_obj)
print('\n', obj.drop(['d','c']))

data = pd.DataFrame(np.arange(16).reshape(4,4),
                    index=['Ohio', 'Colorado', 'Utah', 'New york'],
                    columns=['one', 'two', 'three', 'four'])

print('\n',data)
print('\n', data.drop(['Colorado', 'Ohio']))

print('\n',data.drop('two', axis=1))
print('\n',data.drop(['two', 'four'], axis='columns'))
~~~

### 3.3 색인, 선택, 거르기

- Series의 색인은 정수로 접근이 가능하고 column name으로 도 접근이 가능하다.

~~~python
import pandas as pd
import numpy as np

obj = pd.Series(np.arange(4.), index=['a', 'b', 'c', 'd'])

print('\n', obj)
print('\n', obj['b'])
print('\n', obj[1])
print('\n', obj[2:4])
print('\n', obj[['b','a','d']])
print('\n', obj[[1,3]])
print('\n', obj[obj<2])

print('\n', obj['b':'c'])
obj['b':'c'] = 5
print('\n', obj)

'''
 a    0.0
b    1.0
c    2.0
d    3.0
dtype: float64

 1.0

 1.0

 c    2.0
d    3.0
dtype: float64

 b    1.0
a    0.0
d    3.0
dtype: float64

 b    1.0
d    3.0
dtype: float64

 a    0.0
b    1.0
dtype: float64

 b    1.0
c    2.0
dtype: float64

 a    0.0
b    5.0
c    5.0
d    3.0
dtype: float64
'''
~~~

### 3.3.1 iloc[]

~~~python
data = pd.DataFrame(np.arange(16).reshape(4,4),
                    index=['Ohio', 'Colorado', 'Utah', 'New york'],
                    columns=['one', 'two', 'three','four'])

print(data)
print('\n', data.iloc[1,2]) # Data만 가져 온다. 색인은 제외
print('\n', data.iloc[1,[0,2,3]]) # Series를 가져 온다
print('\n', data[data['three'] > 5])

print('\n', data.iloc[:, :3][data['three'] >5])
# 앞의 대괄호 연산부터 수행하고 그 결과 값을 뒤의 대괄호가 처리한다
~~~

## 3.4 산술 연산

- Pandas에서 중요한 기능은 다른 객체간의 산술 연산이다.
- 객체를 연산 할 때 짝이 맞지 않는 색인 있다면 결과에 두 색인이 통합된다.

~~~python
import pandas as pd
import numpy as np

df1 = pd.DataFrame(np.arange(12.).reshape((3, 4)), columns=list('abcd'))

df2 = pd.DataFrame(np.arange(20).reshape(4, 5), columns=list('abcde'))

print('\n', df1, '\n')
print('\n', df2, '\n')
print('\n', df1 + df2, '\n')

# 짝이 맞지 않은 색인에 대해서는 NaN으로 셋이 된다.
'''
      a     b     c     d   e
0   0.0   2.0   4.0   6.0 NaN
1   9.0  11.0  13.0  15.0 NaN
2  18.0  20.0  22.0  24.0 NaN
3   NaN   NaN   NaN   NaN NaN
'''

print('\n', df1.add(df2, fill_value = 0), '\n')
# 짝이 맞지 않은 색인에 대해서 fill_value로 set이 된다.
~~~

## 3.5 함수 적용과 맵핑

- Pandas 객체에도 유니버셜 함수를 적용할 수 있다.

~~~python
import pandas as pd
import numpy as np

np.random.seed(12345)

frame = pd.DataFrame(np.random.randn(4,3),
                     columns=list('bde'),
                     index=['Utah', 'Ohio', 'Oregon', 'Texas'])

print(frame)

f = lambda x : x.max() - x.min()
print('\n', frame.apply(f))
print('\n', frame.apply(f, axis=1))

def f1(x):
    return pd.Series([x.min(), x.max()], index=['min','max'])

print('\n', frame.apply(f1))
print('\n', frame.apply(f1 ,axis=1))

'''
              b         d         e
Utah   -0.204708  0.478943 -0.519439
Ohio   -0.555730  1.965781  1.393406
Oregon  0.092908  0.281746  0.769023
Texas   1.246435  1.007189 -1.296221

 b    1.802165
d    1.684034
e    2.689627
dtype: float64

 Utah      0.998382
Ohio      2.521511
Oregon    0.676115
Texas     2.542656
dtype: float64

             b         d         e
min -0.555730  0.281746 -1.296221
max  1.246435  1.965781  1.393406

              min       max
Utah   -0.519439  0.478943
Ohio   -0.555730  1.965781
Oregon  0.092908  0.769023
Texas  -1.296221  1.246435
'''
~~~

## 3.6 Sorting

~~~python
import pandas as pd
import numpy as np

obj = pd.Series(np.arange(4) , index=list('dabc'))
print(obj)
print('\n', obj.sort_index())

frame = pd.DataFrame(
    np.arange(8).reshape(2,4),
    index=['three','one'],
    columns= list('dabc')
)

print('\n', frame)
print('\n', frame.sort_index())
print('\n', frame.sort_index(axis=1,  ascending = False))

obj = pd.Series([4,7,-5,2])
print('\n', obj.sort_values())

frame2 = pd.DataFrame({'b' : [4,7,5,-2], 'a' : [0,1,0,1]})

print('\n', frame2.sort_values(by='b'))
print('\n', frame2.sort_values(by=['a', 'b']))
 # a 기준으로 먼저 소팅 한 후 같은 a에 대해서 b 값 기준으로 다시 소팅


~~~

## 3.7 기술 통계 계산과 요약

~~~python
import pandas as pd
import numpy as np

np.random.seed(12345)
data = pd.DataFrame(
    np.random.rand(100, 2),
    columns=['one', 'two'])

print(data.sum())
print('\n', data.sum(axis=1))
print('\n', data.describe())
print('\n', data.info())
print('\n', data.head(5)) # 맨 앞 5개
print('\n', data.tail(5)) # 맨 뒤 5개
~~~

## 3.8 누락된 데이터 처리

- Pandas의 설계 목표 증 하나는 누락 데이터를 쉽게 처리하는 것이다.

~~~python
import pandas as pd
import numpy as np

data = pd.Series([1,np.nan,3, np.nan,7])
print(data)
print('\n',data.dropna())

df = pd.DataFrame(np.random.randn(7,3))
df.iloc[:4,1] = np.nan
df.iloc[:2,2] = np.nan
print('\n', df)
print('\n', df.fillna(0))
print('\n', df.fillna({1:0.5, 2:-1})) # Dictionary를 이용하여
print('\n', df.fillna(method='backfill')) # 뒤에 있는 값을 대치
~~~

## 4. Data Loading

### 4.1 CSV

~~~python
import pandas as pd
import numpy as np

df = pd.read_csv('ex1.csv')
print(df)

df1 = pd.read_csv('ex2.csv', header=None) # Header를 자동 생성
print('\n',df1)

df2 = pd.read_csv('ex2.csv',names=['one', 'two', 'three', 'four', 'five'])
print('\n',df2)

df3 = pd.read_csv('ex2.csv',names=['one', 'two', 'three', 'four', 'five'],
                  index_col='five')
print('\n',df3)

df4 = pd.read_csv('csv_mindex.csv', index_col=['key1', 'key2']) # 멀티 인덱스
print('\n',df4)

df5 = pd.read_csv('ex4.csv', skiprows=[0,2,3]) # 특정 열을 skip
print('\n', df5)

chunker = pd.read_csv('ex6.csv', chunksize=1000)


tot = pd.Series([])
for piece in chunker:
    tot = tot.add(piece['key'].value_counts(), fill_value =0)
tot = tot.sort_values(ascending=False)
print('\n',tot[:10])
~~~

## 5. Data 변형 및 치환

### 5.1. 데이터베이스 스타일로 DataFrame 합치기

#### 5.1.1 merge()

- DB의 `join`연산과 동작 방식이 동일 하다.

~~~python
import pandas as pd
import numpy as np
import json

df1 = pd.DataFrame({'key' : ['b','b','a', 'c', 'a', 'a', 'b'], 'data1' :range(7)})

df2 = pd.DataFrame({'key' : ['a', 'b', 'd'], 'data2' :range(3)})

print('\n',df1, '\n')
print('\n',df2, '\n')

print('\n',pd.merge(df1, df2), '\n')
print('\n',pd.merge(df1, df2, on = 'key'), '\n') 
# join 기본으로 inner join이다.

#how를 통하여 어떤 join을 할 지를 선택 할 수 있다.
print('\n how')
print('\n',pd.merge(df1, df2, how = 'outer'), '\n') # outer join
print('\n',pd.merge(df1, df2, how = 'left'), '\n') # left join
print('\n',pd.merge(df1, df2, how = 'right'), '\n') # right join
print('\n',pd.merge(df1, df2, how = 'inner'), '\n') # inner join

left = pd.DataFrame({
    'key1' :['foo', 'foo','bar'],
    'key2': ['one', 'two', 'one'],
    'lval': [1,2,3]
})

right = pd.DataFrame({
    'key1' :['foo','foo', 'bar','bar'],
    'key2': ['one', 'one', 'one', 'two'],
    'rval': [4,5,6,7]
})

print('\n',left, '\n')
print('\n',right, '\n')
print('\n',pd.merge(left, right, on =['key1', 'key2'], how='outer'), '\n')

df1 = pd.DataFrame({'아이디' : [12,13,14,15,16],
                    '이름' : ['a','b','c','d','e'],
                    '생일' : [1111,2222,3333,4444,5555],
                    '취미' : ['h1','h2','h3','h4','h5'],
                    '주소' : ['add1','add2','add3','add4','add5']})

df2 = pd.DataFrame({'부모 생일' : [22,33,44,55,66],
                    '성명' : ['a','b','c','d','e'],
                    '동생' : [True,False,True,False,True],
                    '좌우명' : ['h1','h2','h3','h4','h5'],
                    '특기' : ['add1','add2','add3','add4','add5']})

print('\n',df1)
print('\n',df2)

print('\n',pd.merge(df1, df2, left_on='이름', right_on="성명", how='outer'), '\n')  
# join을 할 때 사용될 key를 left_on, right_on으로 직접 지정 할 수 있다.
~~~

#### 5.1.2 concat()

~~~python
s1 = pd.Series([0,1], index=list('ab'))
s2 = pd.Series([2,3,4], index=list('cde'))
s3 = pd.Series([5,6], index=list('fg'))

print(s1,'\n')
print(s2,'\n')
print(s3,'\n')

print(pd.concat([s1,s2,s3]))
print(pd.concat([s1,s2,s3], axis=1, sort= False))

s4= pd.concat([s1*5, s3])
print(s4,'\n')
print(pd.concat([s1,s4], axis=1, sort= False))
print(pd.concat([s1,s4], axis=1, join='inner'))
~~~

> DataFrame을 붙일 때 index가 블일치 하는 경우 NaN 값이 들어가기 때문에 주의하여 사용해야 한다.