---
published: true
title: "Machine Learning 을 위한 Pre Processing - 웹 크롤링"
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
  - Pandas
  - Matplotlib
comments: true
header:
  overlay_filter: 0.5
  overlay_image: https://s3.amazonaws.com/codementor_content/2016-Nov/scraping.jpg
  teaser: https://s3.amazonaws.com/codementor_content/2016-Nov/scraping.jpg
sitemap :
  changefreq : daily
  priority : 1.0
---

# 웹 크롤링

![logo](https://cdn.inflearn.com/wp-content/uploads/python_crawler.jpg)

## 1. 개요

- 웹으로 부터 데이터를 수집하는 작업을 말한다.
- 프로그램을 만들어 웹 서버에 쿼리를 보내 데이터를 요청하고, 이를 파싱해 필요한 정보를 추출하는 작업을 자동으로 하는 것이다.
- 화면을 구성하는 data format
  - HTML
  - CSS
  - JavaScript

## 2. 뷰티풀 소프4

- HTML 파일을 쉽게 파싱을 하기 위한 도구

### 2.1 설치

1. 콘다 프롬프트 실행
2. 아래 명령어 실행

![](/assets/images/2019-11-01-13-22-37.png)

### 2.2 HTML 분석

#### 2.2.1 find(), findAll()

- find(), findAll()를 통해 내가 찾고자 하는 tag 정보를 가져 올 수 있다.
  - find() : 한 개 파싱
  - findAll() : 전부 파싱

### 2.3 예제

~~~python
from urllib.request import urlopen
from bs4 import BeautifulSoup
import re

pages = set()

def getLinks(pageUrl):
    global pages

    html = urlopen('https://en.wikipedia.org/' + pageUrl)
    bsObj = BeautifulSoup(html, 'html.parser')
    try:
        print(bsObj.h1.get_text())
        print(bsObj.find(id='mw-content-text').findAll('p'[0]))
        print(bsObj.find(id='ca-edit').find('span').find('a').attrs['href'])
    except:
        print('This page is missing something! No worries though!')

    for link in bsObj.findAll('a', href=re.compile('^(/wiki/)')):
        if 'href' in link.attrs:
            if link.attrs['href'] not in pages:
                newPage = link.attrs['href']
                print(newPage)
                pages.add(newPage)
                getLinks(newPage)
getLinks("")
~~~

- 실행 화면

![bandicam 2019-11-01 14-33-12-095](https://user-images.githubusercontent.com/35194820/68004562-5bbe7780-fcb5-11e9-83da-b470304e9cce.gif)