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

## 1. 환경 설정

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

## 2. 실행하기

환경 설정까지 모두 맞췄다면 이제 실행할 차례입니다. 실행 방법은 매우 간답합니다.

터미널에서 아래 명령어를 입력해 봅시다.

~~~
$ flutter run -d chrome
~~~

그러면 빌드를 시작하고 1분 정도 기다리게 되면...

<div align="center">
  <img src="https://user-images.githubusercontent.com/35194820/87436921-a22bea80-c628-11ea-9da0-70b016046193.PNG" width="60%">
</div>
  

짜잔! 위와 같이 chrome이 실행이 되고 localhost 환경으로 web 이 실행되는 것을 확인 할 수 있습니다.
  
<div align="center">
  <img src="https://media2.giphy.com/media/XreQmk7ETCak0/giphy.gif?cid=ecf05e4786b38b56df4820b7de5208216ff665b297163caa&rid=giphy.gif">
</div>

### 에러 발생 시

chrome이 실행되지 않고 알수 없는 error가 발생할 경우 당황하지 말고 빌드 폴더를 `clean` 후 다시 `run`을 해보시기 바랍니다.

~~~
$ flutter clean
~~~

## 3. Github 페이지 설정하기

1. Web으로 배포하고자 하는 프로젝트를 Github Repository에 업로드를 합니다.

2. https://github.com/settings/tokens 에 접속하여 TOKEN을 생성합니다. TOKEN 명은 `commit_secret`으로 하고 scope를 아래와 같이 설정하고 생성합니다.
<div align="center">

<img src="https://user-images.githubusercontent.com/35194820/87496727-21540980-c68f-11ea-9f78-008946957071.png">
</div>

3. 1단계에서 생성된 Github Repository > Settings > Secrets 로 이동한 후 `New secret` 버튼을 눌러 Secret 를 생성합니다. 이름은 `COMMIT_SECRET`으로 합니다.
value에는 2단계에서 생성된 TOKEN 값을 set 합니다.

<div align="center">
<img src="https://user-images.githubusercontent.com/35194820/87502655-06889180-c69d-11ea-930f-c856f34f4806.png">
</div>

## 4. Project에 index.html 파일 추가

1. lib 폴더가 존재하는 path에 web/index.html 파일을 추가합니다.

<div align="center">
<img src="https://user-images.githubusercontent.com/35194820/87506976-86b3f480-c6a7-11ea-8550-c5ccdc40687f.png" width="200">
</div>

2. index.html을 아래와 같이 수정합니다.

~~~html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>Flutter Web</title>
</head>
<body>
  <script src="main.dart.js" type="application/javascript"></script>
</body>
</html>
~~~

## 5. Project에 Github workflow 설정하기

1. Project 에 .github/workflows/workflow.yml 를 생성합니다.

<div align="center">
<img src="https://user-images.githubusercontent.com/35194820/87503012-1b195980-c69e-11ea-8d5e-5a87d0cf49ac.png" width="200">
</div>

2. workflow.yml 파일을 아래 코드를 복사/붙여넣기를 합니다.

~~~yml
name: Flutter Web
on:
  push:
    branches:
      - master
jobs:
  build:
    name: Build Web
    env:
      my_secret: ${{secrets.commit_secret}}
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v1
      - uses: subosito/flutter-action@v1
        with:
          channel: 'dev'
      - run: flutter config --enable-web
      - run: flutter pub get
      - run: flutter build web --release
        # 1. change path which exist lib/main.dart
        working-directory: ./example_macos
      - run: |
          # 2. change path to [existed lib/main.dart path]/build/web
          cd example_macos/build/web
          git init
           # 3. change your email
          git config --global user.email rlawjdxo1212@naver.com
          # 4. change your name
          git config --global user.name origogi
          git status
          # 5. change your repository
          git remote add origin https://${{secrets.commit_secret}}@github.com/Origogi/Vertical_Card_Pager.git 
          git checkout -b gh-pages
          git add --all
          git commit -m "update"
          git push origin gh-pages -f
~~~

3. 위 코드에서 주석으로 표시된 5군데를 자신의 환경에 맞게 수정을 합니다.

4. 추가된 파일을 `git push` 를 합니다.

5. Github repository > Action 에 가보면 workflow의 명령어들을 쭉 실행하는 것을 볼 수 있습니다. 그리고 작업이 완료 될 때까지 기다려 줍시다.

<div align="center">
<img src="https://user-images.githubusercontent.com/35194820/87507167-ddb9c980-c6a7-11ea-8069-2db1303940c0.png" width="50%">
</div>

약 2분 후 아래와 같이 그린 라이트가 보인다면 빌드가 성공 한 것입니다!

<div align="center">
<img src="https://user-images.githubusercontent.com/35194820/87507211-f1fdc680-c6a7-11ea-8ea3-87ce30a020eb.png" width="50%">
</div>

## 6. Github Page enable 하기

1. Github repository > Settings 으로 이동합니다.

2. `Github Pages` 를 enable 합니다.

3. source를 `master`에서 `gh-pages` 로 변경합니다.

<div align="center">
<img src="https://user-images.githubusercontent.com/35194820/87507962-4d7c8400-c6a9-11ea-9c88-17da4aaeed16.png" width="50%">
</div>

## 7. 접속해보기

이제 마지막입니다!

위 `Github Pages` 에 보이는 링크를 눌러서 정상 동작하는 지 확인 해봅니다. 주소는 [github id].github.io/[Your Repository] 입니다.

<div align="center">
<img src="https://user-images.githubusercontent.com/35194820/87509054-b2d17480-c6ab-11ea-81c3-298f15b78964.png" width ="50%">
</div>


짜잔 위와 같이 웹페이지에서 잘 나오고 동작도 잘되는 것을 확인 할수 있습니다.

<div align="center">
<img src="https://media.giphy.com/media/kudIERso2pFiE/giphy.gif" width ="50%">
</div>

참 쉽지 않습니까? 저 같이 Web을 1도 모르는 사람도 Web page를 이렇게 만들어 봤습니다.

위 화면는 아래 링크로도 확인 가능합니다.

https://origogi.github.io/Vertical_Card_Pager/#/

## 8. 결론

명령어 몇 줄만 입력하면 손 쉽게 Web으로 만들어진다는 것이 무척 매력적으로 느껴질수 있느나 맨 처음 언급했드시 아직 stable 이 되지 않았기 때문에 iOS, Android app 처럼 완벽하게 동작하지 않을수도 있습니다. 그리고 프래임도 조금 떨어지는 것을 느낄수 있습니다.

그래도 한번 쯤 내가 만든 Flutter app을 한번 Github page로 호스팅하여 친구들이나 동료 개발자에게 한번 자랑해보는 건 어떨까요??

혹시 따라하시다가 막히는 부분이 있으면 댓글로 남겨주세요.