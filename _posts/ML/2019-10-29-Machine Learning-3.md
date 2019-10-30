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

### 2.2 Reindexing

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

### 2.3 데이터 삭제(제외)

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