---
published: true
title: "Machine Learning 을 위한 Pre Processing (4)"
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
  - Matplotlib
comments: true
header:
  overlay_filter: 0.5
  overlay_image: https://matplotlib.org/_static/logo2_compressed.svg
  teaser: https://matplotlib.org/_static/logo2_compressed.svg
sitemap :
  changefreq : daily
  priority : 1.0
---

# Matplotlib

![logo](https://img1.daumcdn.net/thumb/R800x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F99F963385B1E17B318)

- Data를 시각화 하기 위한 library(그래프, 도표)
- Matlat과 유사한 인터페이스를 지원한다.

## 1. 그래프

### 1.1 기본

- 코드

~~~python
plt.title("custom plot") # title
plt.plot([10,20,30,40], [1,4,9,16], c='b', lw = 5, ls='--', marker = 'o', ms=15, mec='g', mew =5, mfc = 'r')
plt.xlim(0,50) # X 축 범위 지정
plt.ylim(-10,30) # Y축 범위 지정
plt.show()
~~~

- 결과화면

![ss](/assets/images/Figure_1.png)

### 1.2 Tick 지정

~~~python
X =np.linspace(-np.pi, np.pi, 256)
C = np.cos(X)
plt.plot(X,C)
plt.xticks([-np.pi, -np.pi/2, np.pi/2, np.pi]) # 4개 지정
plt.yticks([-1,0,1]) # 3개 지정
plt.show()
~~~

- 결과 화면

![](/assets/images/2019-10-31-11-08-51.png)

### 1.3 Label, Title 지정

- 코드

~~~python
X =np.linspace(-np.pi, np.pi, 256)
C,S = np.cos(X), np.sin(X)
plt.plot(X,C, ls = '--', label = 'cosine')
plt.plot(X,S, ls = ':', label = 'sine')
plt.xlabel('time')
plt.ylabel('amplitude')
plt.legend(loc=2) # 범례 위치 지정
plt.show()
~~~

- 실행화면

![](/assets/images/2019-10-31-11-02-41.png)