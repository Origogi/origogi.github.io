---
published: true
title: "Machine Learning 을 위한 Pre Processing - Numpy"
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
  overlay_image: http://mathalope.co.uk/wp-content/uploads/2017/09/python-numpy-logo-672x263.png
  teaser: http://mathalope.co.uk/wp-content/uploads/2017/09/python-numpy-logo-672x263.png
sitemap :
  changefreq : daily
  priority : 1.0
---

# Numpy

![logo](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQlfNPTD2PVjwNWbsXM_VDSFW7xcxtS4W-k33zoqb4L3AleRCNM&s)

고성능의 과학 계산 컴퓨팅과 데이터 분석에 필요한 패키지이다.

- 빠르고 메모리를 효율적으로 사용하며, 벡터 산술 연산과 세련된 브로드캐스팅 기능을 제공한다.
- 선형 대수, 난수 발생기, 푸리에 변환이 가능하다.

## 1. ndarray

N 차원을 배열을 생성 할 때 사용
> ndarray를 생성할 때 같은 데이터 타입만 허용한다.

~~~python
import numpy as np

data1 = [6,7,8,0,1]

arr1 = np.array(data1)

print('data1:', data1)
print('arr1', arr1)

print(type(data1))
print(type(arr1))

data2 = [[1,2,3,4], [5,6,7,8]]

arr2 = np.array(data2)
print('arr2 : \n', arr2)
print('dim :', arr2.ndim) # dim : 2
print('shape :', arr2.shape) # shape : (2, 4)
print('dtype:', arr2.dtype) # dtype: int32

arr3 = np.array([1,2,3,4,5], dtype=np.float32)
print('arr3 dtype:', arr3.dtype)
arr3 = arr3.astype(np.int64) # 타입 변한
print('arr3 dtype:', arr3.dtype)

arr4 = np.array(['1.23', '-9.6', '42'],dtype=np.string_)
print(arr4) # [b'1.23' b'-9.6' b'42']

arr4 = arr4.astype(np.float32)
print(arr4) # [ 1.23 -9.6  42.  ]
~~~

## 2. 배열과 스칼라간의 연산

numpy 배열은 for문을 작성하지 않고 데이터를 일괄 처리 할 수 있는 벡터화를 지원한다.

~~~python
print('\n', arr)
print('\n', arr * arr)
print('\n', arr - arr)
print('\n', 1/ arr)
print('\n',  arr ** 0.5)

arr2 = np.array([[0., 4., 1. ], [7., 2., 12.]])
print('\n', arr2)
print('\n', arr2 > arr)

# 위 코드의 결과 값
'''
 [[1. 2. 3.]
 [4. 5. 6.]]

 [[ 1.  4.  9.]
 [16. 25. 36.]]

 [[0. 0. 0.]
 [0. 0. 0.]]

 [[1.         0.5        0.33333333]
 [0.25       0.2        0.16666667]]

 [[1.         1.41421356 1.73205081]
 [2.         2.23606798 2.44948974]]

 [[ 0.  4.  1.]
 [ 7.  2. 12.]]

 [[False  True False]
 [ True False  True]]

'''
~~~

~~~python

import numpy as np

arr = np.array([1, 2, 3, 4, 5, 6, 7, 8, 9])

print(arr[0])
print(arr[:5])

arr[2:5] = 10
print(arr)

arr2 = np.array(
    [[6, 2, 5, 3],
     [7, 9, 1, 4]]
)
print(arr2[1][2])
print(arr2[1, 2]) # 배열을 하나 접근 할 때 이 형식을 주로 사용한다.
print(arr2[1, 1:])
print(arr2[:, :2])

'''
1
[1 2 3 4 5]
[ 1  2 10 10 10  6  7  8  9]
1
1
[9 1 4]
[[6 2]
 [7 9]]
'''


~~~

## 3. 불리언 색인

~~~python
import numpy as np

np.random.seed(12345)

names = np.array(['Bob', 'Joe', 'Will', "Bob", 'Bob', 'Joe', 'Will'])

data = np.random.randn(7, 4)
print('\n', names, "\n")
print('\n', data)

print('\n', names == 'Bob')
print('\n', data[names == 'Bob'])

print('\n', data[names == 'Bob', 2:])
print('\n', data[names == 'Bob',])

'''
 ['Bob' 'Joe' 'Will' 'Bob' 'Bob' 'Joe' 'Will']


 [[-0.20470766  0.47894334 -0.51943872 -0.5557303 ]
 [ 1.96578057  1.39340583  0.09290788  0.28174615]
 [ 0.76902257  1.24643474  1.00718936 -1.29622111]
 [ 0.27499163  0.22891288  1.35291684  0.88642934]
 [-2.00163731 -0.37184254  1.66902531 -0.43856974]
 [-0.53974145  0.47698501  3.24894392 -1.02122752]
 [-0.5770873   0.12412128  0.30261356  0.52377207]]

 [ True False False  True  True False False]

 [[-0.20470766  0.47894334 -0.51943872 -0.5557303 ]
 [ 0.27499163  0.22891288  1.35291684  0.88642934]
 [-2.00163731 -0.37184254  1.66902531 -0.43856974]]

 [[-0.51943872 -0.5557303 ]
 [ 1.35291684  0.88642934]
 [ 1.66902531 -0.43856974]]

 [[-0.20470766  0.47894334 -0.51943872 -0.5557303 ]
 [ 0.27499163  0.22891288  1.35291684  0.88642934]
 [-2.00163731 -0.37184254  1.66902531 -0.43856974]]
'''
~~~

## 4. 유니버셜 함수

데이터 요소 별로 수학적 함수를 적용하는 것

~~~python
import numpy as np

arr = np.arange(10)

print('sqrt: ', np.sqrt(arr), '\n')
print('exp: ', np.exp(arr), '\n')

np.random.seed(12345)

x = np.random.randn(8)
y = np.random.randn(8)

print('x: \n',x, '\n')
print('y: \n', y, '\n')

print('Maxmun x,y : \n')
print(np.maximum(x, y), '\n')

arr = np.random.randn(7) * 5

print(arr, '\n')
print('modf: \n', np.modf(arr), '\n')

'''
sqrt:  [0.         1.         1.41421356 1.73205081 2.         2.23606798
 2.44948974 2.64575131 2.82842712 3.        ] 

exp:  [1.00000000e+00 2.71828183e+00 7.38905610e+00 2.00855369e+01
 5.45981500e+01 1.48413159e+02 4.03428793e+02 1.09663316e+03
 2.98095799e+03 8.10308393e+03]

x:
 [-0.20470766  0.47894334 -0.51943872 -0.5557303   1.96578057  1.39340583
  0.09290788  0.28174615]

y:
 [ 0.76902257  1.24643474  1.00718936 -1.29622111  0.27499163  0.22891288
  1.35291684  0.88642934]

Maxmun x,y :

[ 0.76902257  1.24643474  1.00718936 -0.5557303   1.96578057  1.39340583
  1.35291684  0.88642934] 

[-10.00818655  -1.85921269   8.34512655  -2.19284868  -2.69870723
   2.38492505  16.2447196 ] 

modf: 
 (array([-0.00818655, -0.85921269,  0.34512655, -0.19284868, -0.69870723,
        0.38492505,  0.2447196 ]), array([-10.,  -1.,   8.,  -2.,  -2.,   2.,  16.]))

'''
~~~

### 5. 재형성

~~~python
import numpy as np

arr = np.arange(8)

print(arr, '\n')
arr2 = arr.reshape(4,2)
print(arr, '\n')
print(arr2, '\n')
print(arr2.T, '\n')

xarr = np.array([1.1, 1.2, 1.3, 1.4, 1.5])
yarr = np.array([2.1, 2.2, 2.3, 2.4, 2.5])
zarr = np.array([True, False, True, True, False])

result = [x if b  == True else y for x, y, b in zip(xarr, yarr, zarr)]
print(result)

# 위 식을 아래와 같이 함축 시킬수 있다. where( condition, arr1, arr2)
result = np.where(zarr, xarr, yarr)
print(result)

'''
[0 1 2 3 4 5 6 7]

[0 1 2 3 4 5 6 7]

[[0 1]
 [2 3]
 [4 5]
 [6 7]]

[[0 2 4 6]
 [1 3 5 7]]

[1.1, 2.2, 1.3, 1.4, 2.5]
[1.1 2.2 1.3 1.4 2.5]
'''

~~~

## 6. 수학 메소드와 통계 메소드

~~~python
import numpy as np

arr = np.random.randn(5, 4)

print('\n', arr)

print('\nmean1 : ', arr.mean()) # 평균 구하기
print('\nmean2 : ', np.mean(arr))
print('\nsum : ', arr.sum()) # total sum

print('\n', arr.mean(axis=1)) # axis = 0 , Row
print('\n', arr.sum(axis=0)) # axis = 1 , Column

arr = np.array([0, 1, 2, 3, 4, 5, 6, 7])
print('\n', arr.cumsum()) # 누적 합

arr = np.array([[0, 1, 2],
                [3, 4, 5],
                [6, 7, 8]])

print('\n', arr.cumsum(axis=0))
print('\n', arr.cumprod(axis=1))

'''
[[-0.79563133 -1.17517227  0.62650881 -1.0231121 ]
 [ 1.01007041 -1.98169661 -0.92232807  0.29811058]
 [ 0.28212076 -0.65852621 -0.03278394  0.97964221]
 [ 0.50173792  1.39187473  0.41808586 -0.90839659]
 [-0.34202882 -1.18326469 -0.76332477 -0.18529098]]

mean1 :  -0.22317025430414433

mean2 :  -0.22317025430414433

sum :  -4.463405086082886

 [-0.59185172 -0.39896092  0.14261321  0.35082548 -0.61847731]

 [ 0.65626894 -3.60678504 -0.67384211 -0.83904687]

 [ 0  1  3  6 10 15 21 28]

 [[ 0  1  2]
 [ 3  5  7]
 [ 9 12 15]]

 [[  0   0   0]
 [  3  12  60]
 [  6  42 336]]
'''
~~~

## 6.1 불리언 배열을 위한 메소드

~~~python
import numpy as np

np.random.seed(12345)

arr = np.random.randn(100)

print((arr>0).sum())

bools = np.array([[False, False,True,False], [False, False, True, False]])

print(bools.any(axis=1)) # any는 하나라도 true이면 true를 리턴
print(bools.all(axis=0))

data = np.random.randn(10,4)
data = data * 2
print('\n', data)

print('\n',data[(data>3).any(axis=1)]) # 연관된 record를 가져온다.
'''
[[-1.17897537  3.16340019 -1.05746965  0.91400374]
 [ 3.26947241  1.9780166   0.91588029  1.11030882]]
'''

~~~

## 6.2 정렬

numpy 정렬은 각 개별적으로 sorting 되기 때문에 연관된 data에 대해서는 적합하지 않다.

~~~python
import numpy as np

arr = np.random.randn(5,3)

print('\n', arr)

arr.sort(axis=1)
print('\n', arr)

arr.sort(axis=0)
print('\n', arr)
~~~

### 6.2.1 간접 정렬

~~~python
import numpy as np

np.random.seed(12345)

values = np.array([5,0,1,3,2])

indexer = values.argsort()
print(indexer)
print(values[indexer])


data = np.random.rand(3,5)
data[0] = values

print(data)
print('\n', data[:, data[0].argsort()])

'''
[1 2 4 3 0]
[0 1 2 3 5]
[[5.         0.         1.         3.         2.        ]
 [0.5955447  0.96451452 0.6531771  0.74890664 0.65356987]
 [0.74771481 0.96130674 0.0083883  0.10644438 0.29870371]]

 [[0.         1.         2.         3.         5.        ]
 [0.96451452 0.6531771  0.65356987 0.74890664 0.5955447 ]
 [0.96130674 0.0083883  0.29870371 0.10644438 0.74771481]]
'''
~~~

## 6.3 집합 함수

- unique
- union
- diff.
- intersect

~~~python
import numpy as np

names = np.array((['Bob', 'Joe', 'Will', 'Bob', 'Will', 'Joe', 'Joe']))
print('\n', np.unique(names))

ints = np.array([3,3,3,2,2,1,1,4,4])
print('\n', np.unique(ints)) # 정렬까지 기본으로 해준다.

values = np.array([6,0,0,3,2,5,6])
print('\n', np.in1d(values, [2,3,6])) # Contain이랑 비슷하게 동작 , A[index] in B ??
print('\n', np.union1d(values, [2,3,6])) # #교집합
print('\n', np.intersect1d(values, [2,3,6])) # 합집합
print('\n', np.setdiff1d(values, [2,3,6])) # 차집합
~~~

## 6.4 고급 배열

### 6.4.1 집합 합치기

~~~python
import numpy as np

np.random.seed(12345)

arr1 = np.arange(1,7).reshape(2,3)
arr2 = np.arange(7,13).reshape(2,3)

print(arr1)
print('\n', arr2)
print('\n', np.concatenate([arr1, arr2], axis=0))
print('\n', np.concatenate([arr1, arr2], axis=1))

print('\n', np.vstack([arr1, arr2])) #vertical
print('\n', np.vstack([arr1, arr2])) #horizontal

print('\n', np.r_[arr4,arr5]) #vertical
print('\n', np.r_[arr4,arr5]) # horizontal
print('\n', np.c_[np.r_[arr4, arr5], arr3])
~~~
