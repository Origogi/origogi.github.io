---
published: true
title: "[Flutter] Flutter Project를 GitHub Page로 Deploy 해보기"
excerpt : " "
layout: single
classes: wide
author_profile: true
read_time: false # read_time을 출력할지 여부 1min read 같은것!
toc: true #Table Of Contents 목차 보여줌
toc_label: "My Table of Contents" # toc 이름 정의
toc_icon: "cog" #font Awesome아이콘으로 toc 아이콘 설정
toc_sticky: true # 스크롤 내릴때 같이 내려가는 목차
categories :
 - Flutter
tags: 
  - Flutter
  - Dart
comments: true
header:
  overlay_image: assets/images/flutter_dart.png
  overlay_filter: 0.5
  teaser: 
sitemap :
  changefreq : daily
  priority : 1.0
---

![header](https://miro.medium.com/max/1920/1*2adBDBgzNounV9WhluUh5Q.png)

## Overview

Flutter는 Android와 iOS 뿐만 아니라 __Web__ 도 지원하는 Cross Platform입니다. 

이 포스트에서 Flutter를 작성한 코드를 Web으로 빌드해보고 그 결과물을 __Github pages__ 로 호스팅을 하는 과정을 다루도록 하겠습니다.

## 참고로..

<div align="center">
<img src="https://media3.giphy.com/media/bEVKYB487Lqxy/giphy.gif" >
</div>


Flutter는 이제 flutter 웹 애플리케이션 실행을 위한 초기 실험 지원을 제공합니다. 여전히 누락 된 기능과 알려진 성능 문제가 많이 있으므로 프로덕션 용도로는 권장되지 않습니다.
실제로 제가 작성한 코드가 iOS, Android로 정상동작하였지만 Web으로 빌드하였을 때 정상적으로 동작하지 않는 문제를 겪었습니다.
따라서 이걸로 상용 서비스를 위한 Web app을 만들기보다는 "Flutter web이 이렇게 보이는 구나" 로만 참고 바랍니다.

## 환경 설정

터미널을 실행 후 아래와 같이 명령어를 입력합니다.

~~~
$ flutter channel master
$ flutter upgrade
$ flutter config --enable-web
~~~

위 작업은 시간이 조금 소요가 됨으로 잠시 기다려 줍니다.

모두 완료가 되었다면 아래 명령어를 입력하여 web이 정상적으로 enable이 되었는 지 확인을 합니다.

~~~
$ flutter devices
~~~


<div align="center">
  <img src="https://user-images.githubusercontent.com/35194820/87435940-54fb4900-c627-11ea-860c-e2980ffb50f0.PNG" >
</div>

위에서 지원하는 device에서 `chrome` 이 추가가 되었다면 정상적으로 환경이 셋업이 된 것입니다.

## 실행하기

환경 설정까지 모두 맞췄다면 이제 실행할 차례입니다. 실행 방법은 매우 간답합니다.

터미널에서 아래 명령어를 입력해 봅시다.

~~~
$ flutter run -d chrome
~~~

그러면 빌드를 시작하고 1분 정도 기다리게 되면...

짜잔! 아래와 같이 chrome이 실행이 되고 localhost 환경으로 web 이 실행되는 것을 확인 할 수 있습니다.
</br></br>
<div align="center">
  <img src="https://user-images.githubusercontent.com/35194820/87436921-a22bea80-c628-11ea-9da0-70b016046193.PNG" width=30%>
  
</div>

</br>

<div align="center">
  <img src="https://media2.giphy.com/media/XreQmk7ETCak0/giphy.gif?cid=ecf05e4786b38b56df4820b7de5208216ff665b297163caa&rid=giphy.gif">
</div>

### 에러 발생 시

chrome이 실행되지 않고 알수 없는 error가 발생할 경우 당황하지 말고 빌드 폴더를 `clean` 후 다시 `run`을 해보시기 바랍니다.

~~~
$ flutter clean
~~~