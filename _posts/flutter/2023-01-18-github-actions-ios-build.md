---
published: true
title: "[Flutter] Github actions 에서 iOS 빌드/배포 속도 개선"
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
  overlay_image: assets/images/flutter_banner.jpeg
  overlay_filter: 0.4
  teaser: 
sitemap :
  changefreq : daily
  priority : 1.0
---

<div align="center">
<img width="982" alt="image" src="https://user-images.githubusercontent.com/35194820/211177673-7ec90307-162d-4696-89b3-6054415d2db5.png">
</div>

## Overview

이번 포스트에서는 Github Actions 을 이용한 Flutter CI/CD 구축 중에 iOS 빌드/배포 속도를 어떻게 개선했는지에 대해서 정리를 하였습니다.

## 1. 문제 인식

먼저 저는 현업에서 개발 중인 Flutter App 을 Github actions 를 이용하여 CI/CD 환경을 구축하는 업무를 진행하였고 거의 완성 되었을 때 iOS 빌드/배포 소요 시간이 너무 느리다는 것을 파악했습니다.

먼저 안드로이드 같은 경우 빌드/배포까지 평균 10분 정도 소요가 됨으로 그럭저럭 쓸만한 성능을 내고 있습니다.

<div align="center">
<img width="779" alt="image" src="https://user-images.githubusercontent.com/35194820/211176021-c370b94c-2b97-48ac-83b7-448f5a794a18.png">
</div>

하지만 iOS 같은 경우 빌드/배포까지 50분~60분 정도 소요가 되었습니다. 

<div align="center">
<img width="953" alt="image" src="https://user-images.githubusercontent.com/35194820/211177441-96f7d6ac-9cff-4cdd-a1b8-09bddbcd35fc.png">
</div>

앱 빌드/배포하는데 1시간이나 걸리게 되면 개발자의 편의성을 위해서 CI/CD 를 구축하는게 의미가 없어집니다.
따라서 이 문제를 해결하기 위해 많은 시행 착오를 겪었습니다.

## 2. Flutter iOS 빌드/배포 분석

먼저 iOS 빌드/배포 성능을 개선하기 위해서는 iOS 빌드/배포를 알아야 합니다.

보통 Flutter CI/CD는 아래와 같이 구축을 하게 됩니다.


<div align="center">
<img width="940" alt="image" src="https://user-images.githubusercontent.com/35194820/211176536-b15f64c4-97ad-4f2f-982a-124136a71658.png">
</div>

여기서 시간이 가장 소요되는 것은 `flutter build ios` 부분입니다. 총 32분이 소요되었습니다.

<div align="center">
<img width="906" alt="image" src="https://user-images.githubusercontent.com/35194820/211176879-37b0036c-d500-4dc6-bfdb-691cb2eea0f8.png">
</div>

`flutter build ios` 를 보면 내부적으로 `pod install`, `xcode build` 두 과정을 수행하게 됩니다.

`pod install` 은 약 10분 그리고 `xcode build` 은 약 22분이 소요가 되었습니다.

그래서 `flutter build ios` 이 부분를 개선해보도록 하겠습니다.

## 3. 개선 작업 (1)

먼저 `pod install` 과정을 개선해보겠습니다. `pod install` 은 ios 를 빌드할 때 필요한 외부라이브러리를 다운받고 설치하는 과정입니다. 여기서 사용되는 대표적인 외부 라이브러리는 Firebase 관련 라이브러리입니다.
그중 Firestore 관련 라이브러리가 시간을 어머어마하게 잡아먹습니다.

그래서 해결 방법은 Pre-build 된 Firestore 라이브러리를 직접 사용하는 것입니다.
아래와 같이 ios/Podfile 에 Pre-build 된 Firestore 라이브러리를 사용하도록 스크립트를 추가합니다.

~~~yaml
target 'Runner' do
  pod 'FirebaseFirestore', :git => 'https://github.com/invertase/firestore-ios-sdk-frameworks.git', :tag => '10.3.0'
  // ~~~
end
~~~


주의할 점은 Firestore tag 는 pubspec.yaml 의 firestore 의 버전에 따라 달라집니다. 꼭 버전을 확인해서 알맞은 tag 를 사용하도록 합니다.

좀 더 자세한 내용은 아래 링크를 확인 바랍니다.

[How to speed-up Cloud Firestore Xcode builds on your Flutter apps](https://codewithandrea.com/tips/speed-up-cloud-firestore-xcode-builds/)
 
## 3. 개선 작업 (2)

 `flutter build ios` 를 실행하면 내부적으로 `xcode build` 를 수행하게 되는데 문제는 Fastlane에서 xcode build 를 다시 수행하기 때문에 먼저 수행되는  xcode build 는 수행할 필요가 전혀 없습니다. 따라서 이 과정을 스킵하기 위해서 `flutter build ios` 를 수행할 때 `--config-only` 를 붙입니다.

 > flutter build ios --flavor prod --release --config-only --no-codesign -t lib/dev/main.dart

## 4. 결과

위 개선 작업을 적용한 결과 아래와 같이 iOS 빌드/배포 시간이 18분 정도로 약 30분 정도 시간을 단축하였습니다.

<div align="center">

<img width="955" alt="image" src="https://user-images.githubusercontent.com/35194820/211177631-38c271c1-076e-4c8f-95c5-ba8e670a3856.png">
</div>





 