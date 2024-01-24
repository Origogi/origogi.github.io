---
published: true
title: "[iOS] iOS 배포 시 Certification, Provisioning Profile 정리"	
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
 - iOS	
tags: 	
  - iOS
  - CI/CD
  - Fastlane

comments: true	
header:
  overlay_image: https://coderkube.com/wp-content/uploads/2017/10/iPhone-app-development-banner.png
  overlay_filter: 0.4
  teaser: https://github.com/Origogi/leetcode/assets/35194820/72f4c4de-3fb2-4e78-89b5-2618594cea7b
sitemap :	
  changefreq : daily	
  priority : 1.0	
---

## 서론

해당 포스트는 iOS app 을 빌드 과정 및 배포 과정에서 사용되는 Certification, Provisioning Profile 에 대해 정리한 내용입니다. 특히 CI/CD 환경에서 iOS 앱을 빌드할 때 Certification, Provisioning Profile 을 어떻게 셋팅하는지 알아보도록 하겠습니다.

### 코드 서명이란?

iOS에서 코드 서명(Code Signing)이 필요한 이유는 여러 가지가 있습니다. 이는 주로 보안, 신뢰성 및 사용자 경험과 관련이 있습니다:

- 보안 강화: 코드 서명은 앱이 변조되지 않았으며 신뢰할 수 있는 소스에서 왔음을 확인합니다. 이는 악성 소프트웨어가 시스템을 해치는 것을 방지하는 데 도움이 됩니다.
- 신뢰성 보장: 애플은 개발자가 등록하고 고유한 인증서를 통해 앱을 서명하도록 요구함으로써, 개발자가 그들의 앱에 대한 책임을 질 것임을 보장합니다. 이는 사용자에게 해당 앱이 신뢰할 수 있는 개발자에 의해 생성되었다는 확신을 줍니다.
- App Store 정책 준수: iOS 앱은 Apple의 App Store를 통해 배포됩니다. App Store에 앱을 제출하기 위해서는 코드 서명이 필요합니다. 이는 애플이 앱의 품질과 보안을 관리하고, 사용자에게 안전하고 신뢰할 수 있는 앱만 제공하도록 하는 데 중요한 역할을 합니다.
- 시스템 무결성 및 안정성 유지: 코드 서명은 앱이 iOS 시스템의 기본 보안 정책과 호환되도록 보장합니다. 이는 앱이 시스템의 안정성이나 다른 앱의 기능에 부정적인 영향을 미치지 않도록 합니다.
- 사용자 개인정보 보호: 코드 서명을 통해 애플은 개발자가 iOS 장치의 보안 기능을 우회하거나 사용자의 개인 정보를 부적절하게 수집하는 앱을 배포하지 못하도록 방지합니다.

### 코드 서명 과정

iOS에서의 코드 서명 과정은 인증서(Certification)와 프로비저닝 프로파일(Provisioning Profile) 두 가지 주요 요소를 이용합니다. 이 과정은 대략 다음과 같이 진행됩니다:

1. 개발자 인증서 생성 및 등록: 개발자는 먼저 Apple Developer Program에 등록합니다. 그 후, 개발자는 자신의 Mac에서 고유한 공개 키/개인 키 쌍을 생성합니다. 이 개인 키는 개발자만이 알고 있어야 합니다. 공개 키는 Apple에 제출되어 개발자 인증서(Certificate)를 생성하는 데 사용됩니다. 이 인증서는 앱이 해당 개발자에 의해 개발되었음을 증명하는 역할을 합니다.
2. 프로비저닝 프로파일 생성: 프로비저닝 프로파일은 특정 앱, 특정 디바이스, 그리고 특정 개발자 인증서에 대한 정보를 포함합니다. 이 프로파일은 애플의 개발자 웹사이트에서 생성되며, 앱이 설치될 수 있는 디바이스와 이를 개발할 수 있는 인증서를 명시합니다.
3. 앱 서명: 개발자는 앱을 빌드하고, 자신의 개인 키를 사용하여 앱에 서명합니다. 이 과정에서 개발자의 인증서와 프로비저닝 프로파일이 사용됩니다. 서명된 앱은 프로비저닝 프로파일에 명시된 디바이스에서만 실행될 수 있으며, 해당 인증서로 인증된 개발자에 의해 생성된 것으로 인식됩니다.
4. 앱 배포 및 검증: 서명된 앱은 이제 배포될 준비가 되었습니다. 사용자가 앱을 설치할 때, iOS는 앱의 서명을 검증하여 인증서의 유효성과 프로비저닝 프로파일의 정보가 일치하는지 확인합니다. 이 검증 과정을 통해 앱의 신뢰성과 안전성이 확보됩니다.

### 인증서 (Certification)

인증서는 개발자의 신원을 확인하고, 애플리케이션의 코드가 안전하게 서명되었음을 보증하는 데 사용됩니다.
인증서는 목적에 따라서 다음과 같이 나뉩니다.

#### 개발 인증서(Development Certificate):

- 목적: 개발 중인 앱을 테스트하기 위해 사용됩니다.
- 사용: 이 인증서를 사용하여 서명된 앱은 등록된 테스트 디바이스에서만 실행될 수 있습니다.
- 특징: 개발 인증서는 개별 개발자 또는 개발 팀에 할당됩니다.

#### 배포 인증서(Distribution Certificate):

- 목적: 앱 스토어에 앱을 배포하거나 Firebase App Distribution 배포를 위해 사용됩니다.

### 프로비저닝 프로파일 (Provisioning Profile)

#### 프로비저닝 프로파일의 역할

 - 디바이스 식별: 프로비저닝 프로파일은 특정 앱이 특정 iOS 디바이스에서 실행될 수 있도록 합니다. 이를 통해 개발자는 앱을 특정 디바이스에 설치하고 테스트할 수 있습니다.
- 앱 서명: 개발자가 앱에 서명하는 데 사용되는 인증서를 포함합니다. 이는 앱이 해당 개발자에 의해 만들어졌음을 보증합니다.
- 앱 실행 권한 관리: 프로비저닝 프로파일은 앱이 어떤 환경(개발 또는 배포)에서 실행될 수 있는지 정의합니다.

#### 프로비저닝 프로파일의 유형

1. development
  - 앱을 개발하고 테스트하는 동안 사용됩니다.
  - 특정 개발자 계정과 연결된 디바이스에서만 앱을 실행할 수 있도록 합니다.
2. appstore
  - 앱을 App Store 나 TestFlight 에 배포할 때 사용됩니다.
3. ad-hoc
  - 앱을 특정 디바이스에 배포할 때 사용됩니다.
  - 특정 개발자 계정과 연결된 디바이스에서만 앱을 실행할 수 있도록 합니다.
4. enterprise
  - 기업이 자체 개발한 iOS 애플리케이션을 그들의 직원이나 구성원에게 배포할 때 사용됩니다.
  -  Apple의 공식 App Store를 거치지 않고, 기업 내부에서만 사용되는 애플리케이션을 직접 관리하고 배포할 수 있습니다.

#### 요약

![](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*utY_Oy4GHDTqohQFqjRKQw.png)


### Fastlane 을 이용한 iOS Build

CI/CD 를 이용할 때 보통 Fastlane 을 이용하여 iOS 앱을 빌드하고 배포하는 경우가 많습니다. Fastlane 을 이용하여 iOS 앱을 빌드하는 과정에서 Certification, Provisioning Profile 을 어떻게 사용하는지 알아보도록 하겠습니다.

CI/CD 를 이용하여 iOS 앱을 서명할 때는 보통 수동으로 설정하고 `match` 를 이용하여 코드 서명을 진행합니다. 여기서 `match` 에 대해서는 해당 포스트에서 다루지 않겠습니다.

제가 match 를 통해서 관리하는 Certification, Provisioning Profile 은 아래와 같습니다.

<img width="347" alt="image" src="https://gist.github.com/assets/35194820/852f4f7f-847e-492b-8fc6-05d49e0c609e">

인증서는 development, distribution 나누어져 있고, 프로비저닝 프로파일은 adhoc, appstore, development 로 나누어져 있습니다.

참고로 아래 Fastlane code는 중요한 부분만 간략하게 작성한 것이며, 실제로는 더 많은 코드가 있습니다. 자신의 환경에 맞게 코드를 작성하시면 됩니다.

앱의 번들 아이디는 `com.sample.app` 이라고 가정하겠습니다.

#### 1. Firebase App Distribution 배포

Firebase App Distribution 을 이용하여 iOS 앱을 배포하는 경우는 사용되는 인증서와 프로비저닝 프로파일은 아래와 같습니다.

|인증서|프로비저닝 프로파일|
|---|---|
|distribution|adhoc|

추가적으로 `enterprise` 프로비저닝 프로파일을 사용할 수도 있습니다.

Fastlane 은 아래와 같이 구성합니다.

```ruby
match(
  type: "adhoc",
  app_identifier: ["com.sample.app"],
)

update_code_signing_settings(
  use_automatic_signing: false,
  path: "Runner.xcodeproj",
  code_sign_identity: 'iPhone Distribution'
)

update_target_provision(
  target: 'Runner', 
  config: 'Release' 
)

build_app(
  export_method: "ad-hoc",  
)

firebase_app_distribution()
```

#### 2. TestFlight or Appstore 배포

Firebase App Distribution 을 이용하여 iOS 앱을 배포하는 경우는 사용되는 인증서와 프로비저닝 프로파일은 아래와 같습니다.

|인증서|프로비저닝 프로파일|
|---|---|
|distribution|appstore|

Fastlane 은 아래와 같이 구성합니다.

```ruby
match(
  type: "appstore",
  app_identifier: ["com.sample.app"],
)

update_code_signing_settings(
  use_automatic_signing: false,
  path: "Runner.xcodeproj",
  code_sign_identity: 'iPhone Distribution'
)

update_target_provision(
  target: 'Runner', 
  config: 'Release' 
)

build_app(
  export_method: "app-store",
)

upload_to_testflight()
```

#### 3. Firebase Test Lab 배포

Firebase Test Lab 을 이용해서 E2E Test 또는 Integration Test 를 진행하는 시나리오에 대해서 알아보겠습니다.
이때 XCode 에서는 XCTest 을 위한 Runner 가 추가로 존재하고 해당 번들 아이디는 `com.sample.app.RunnerUITests` 라고 가정하겠습니다.

<img width="201" alt="image" src="https://github.com/Origogi/leetcode/assets/35194820/75332875-bfda-4109-bda1-b99ce7b492c5">

그리고 사용되는 인증서와 프로비저닝 프로파일은 아래와 같습니다.

|인증서|프로비저닝 프로파일|
|---|---|
|development|development|

```ruby

// 주의할 점은 XCode 에 사용되는 번들 아이디는 com.sample.app.RunnerUITests 이지만 실제 provisioning profile 에는 com.sample.app.RunnerUITests.xctrunner 를 사용해야 합니다.

match(
  type: "development",
  app_identifier: ["com.sample.app", "com.sample.app.RunnerUITests.xctrunner"],  
)

update_code_signing_settings(
  use_automatic_signing: false,
  path: "Runner.xcodeproj",
  code_sign_identity: 'iPhone Distribution'
)

update_target_provision(
  target: 'Runner', 
  config: 'Release' 
)

update_target_provision(
  target: 'RunnerUITests', 
  config: 'Release' 
)

// XCTestRunner 를 빌드합니다.
// 자세한 것은 아래 링크를 참고하세요.
// https://firebase.google.com/docs/test-lab/ios/run-xctest?hl=ko#project

# xcodebuild -project PATH/TO/YOUR_WORKSPACE/YOUR_PROJECT.xcodeproj \
#    -scheme YOUR_SCHEME \
#    -derivedDataPath FOLDER_WITH_TEST_OUTPUT \
#    -sdk iphoneos build-for-testing

// XCTesetRunner 를 zip 파일로 압축합니다.
# cd build/ios_integ/Build/Products
# zip -r ios_tests.zip Release-iphoneos/*.app *.xctestrun

// firebase test lab에 업로드
# gcloud firebase test ios run \
# 	--type xctest \
# 	--test "build/ios_integ/Build/Products/ios_tests.zip" \
# 	--device model="$IOS_DEVICE_MODEL",version="$IOS_DEVICE_VERSION",locale=en_US,orientation=portrait \
# 	--timeout 10m \
# 	--results-bucket= Firebase Storage 이름 \

```

### Reference

https://sujinnaljin.medium.com/ios-certificate-%EC%99%80-provisioning-profile-e1b9455e8a51

