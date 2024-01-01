---
published: true
title: "[Flutter] Github actions 에서 CCache 를 적용해서 iOS 빌드 속도를 개선해 보기"
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

이번 포스트에서는 Github Actions 을 이용한 Flutter CI/CD workflow에서 Ccache 를 적용하여 ios 빌드 성능을 개선하였던 내용에 대해서 다루고 있습니다.

## 1. 문제 인식

Ccache 를 적용하기 앞서서 먼저 아래 포스트를 참고하시면 좋을 것 같습니다.

> [Flutter Github actions 에서 iOS 빌드/배포 속도 개선](https://junsukim.dev/flutter/2023-01-06-github-actions-ios/)

위 포스트를 보면 iOS 빌드 속도를 줄이기 위해 [firebase-prebuilt library](https://github.com/invertase/firestore-ios-sdk-frameworks)를 사용하고 있었는데요 Firebase 버전을 최신으로 올리면서 해당 Library를 사용하게 되면 앱이 빌드가 안되는 문제가 발견되었습니다. 이 문제는 아직 수정이 되지 않고 있습니다. 해당 문제는 아래 링크를 참고하시면 좋을 것 같습니다.

> [any version 10.17.0 or higher: Error (Xcode): Undefined symbol](https://github.com/invertase/firestore-ios-sdk-frameworks/issues/79)

## 2. Local 환경에서 Ccache 적용

Ccache 는 C/C++ 컴파일러를 위한 캐시 시스템으로 컴파일된 오브젝트 파일을 캐시에 저장하여 다음 빌드 시에 재사용할 수 있도록 해줍니다. 이를 통해서 빌드 시간을 줄일 수 있습니다.

### 1. Ccache 설치

brew 를 통해서 Ccache 를 설치합니다.

```bash
brew install ccache
```

설치가 완료되면 아래 명령어를 통해서 Ccache 가 잘 설치되었는지 확인합니다.

```bash
ccache -V
```
### 2. 환경 변수 추가

`.bashrc`, `.bash_profile` 나 `.zshrc` 파일에 아래와 같이 환경 변수를 추가합니다. 

```bash
export PATH="/opt/homebrew/opt/ccache/libexec:$PATH"
```

### 3. 심볼릭 링크 걸기

아래와 같이 심볼릭 링크를 걸어줍니다.

```bash
ln -s $(which ccache) /usr/local/bin/gcc
ln -s $(which ccache) /usr/local/bin/g++
ln -s $(which ccache) /usr/local/bin/cc
ln -s $(which ccache) /usr/local/bin/c++
ln -s $(which ccache) /usr/local/bin/clang
ln -s $(which ccache) /usr/local/bin/clang++
```

### 4. 설정 확인

아래 명령어를 통해서 Ccache 설정이 잘 되었는지 확인합니다.

```bash
which gcc
```

정상적으로 설정이 되었다면 아래와 같이 출력이 됩니다.

```bash
 which gcc
/opt/homebrew/opt/ccache/libexec/gcc
```

### 5. ios/Podfile 수정

ios/Podfile 에 아래와 같이 스크립트를 추가합니다.

```ruby
...
post_install do |installer|
  installer.generated_projects.each do |project|
      project.targets.each do |target|
        target.build_configurations.each do |config|
            config.build_settings["CC"] = "clang"
            config.build_settings["LD"] = "clang"
            config.build_settings["CXX"] = "clang++"
            config.build_settings["LDPLUSPLUS"] = "clang++"
        end
      end
  end
```

### 6. 빌드 시간 측정

아래 명령어를 통해서 빌드 시간을 측정합니다.

```bash
flutter build ipa
```

첫 번째 빌드 시간은 아래와 같이 327,777ms 가 소요되었습니다. 그리고 ccache -s 명령어를 통해서 캐시 효율을 확인해보면 0% 입니다.
왜냐하면 첫 번째 빌드이기 때문에 아직 cache 가 생성되지 않았기 때문입니다.

```bash

"flutter ipa" took 327,777ms.

 ccache -s
Cacheable calls:    2655 / 2655 (100.0%)
  Hits:                0 / 2655 ( 0.00%)
    Direct:            0
    Preprocessed:      0
  Misses:           2655 / 2655 (100.0%)
Local storage:
  Cache size (GiB):  0.6 /  5.0 (11.18%)
  Hits:                0 / 2655 ( 0.00%)
  Misses:           2655 / 2655 (100.0%)
```

두 번째 빌드 시간은 아래와 같습니다.

```bash
"flutter ipa" took 130,970ms.

 ccache -s
Cacheable calls:    5310 / 5310 (100.0%)
  Hits:             2655 / 5310 (50.00%)
    Direct:         2655 / 2655 (100.0%)
    Preprocessed:      0 / 2655 ( 0.00%)
  Misses:           2655 / 5310 (50.00%)
Local storage:
  Cache size (GiB):  0.6 /  5.0 (11.18%)
  Hits:             2655 / 5310 (50.00%)
  Misses:           2655 / 5310 (50.00%)

```

시간이 절반으로 줄어들었습니다. 그리고 ccache -s 명령어를 통해서 캐시 효율을 확인해보면 50% 입니다. 즉, 캐시가 잘 생성되었다는 것을 알 수 있습니다. 50%인 이유는 두 번째 빌드 시에는 첫 번째 빌드 시에 생성된 캐시를 사용하기 때문입니다.

자 이제 Github Actions 에서 Ccache 를 적용해보도록 하겠습니다.

## 3. Github Actions 에서 Ccache 적용

### 1. 워크플로우 구성

```yaml
- name: Update env  // Ccache 환경 변수 추가
  run: |
    echo "/usr/lib/ccache:/usr/local/opt/ccache/libexec:$PATH" >> $GITHUB_PATH
    echo "CCACHE_SLOPPINESS=clang_index_store,file_stat_matches,include_file_ctime,include_file_mtime,ivfsoverlay,pch_defines,modules,system_headers,time_macros" >> $GITHUB_ENV
    echo "CCACHE_FILECLONE=true" >> $GITHUB_ENV
    echo "CCACHE_DEPEND=true" >> $GITHUB_ENV
    echo "CCACHE_INODECACHE=true" >> $GITHUB_ENV

  - name: Install Ccache  // Ccache 설치
    uses: hendrikmuhs/ccache-action@v1.2
    with:
      key: ${{ runner.os }}-v2
      max-size: 5G

  - name: Build ipa  // 빌드
    run: flutter build ipa
```
### 2. 확인

workflow 를 실행하면 최초 빌드 시 캐시가 없기 때문에 시간이 오래 걸리지만 두 번째 빌드 시에는 캐시를 사용함으로써 빌드 시간이 단축되었습니다.
그리고 workflow 의 출력을 통해 Ccahe의 캐시 효율을 확인해볼수 있습니다.

<img width="500" alt="image" src="https://github.com/Origogi/leetcode/assets/35194820/7e24836a-8a0b-41fd-9c03-c6decffe3ced">

### 3. 결과

Ccache 를 적용한 결과 빌드 시간은 약간 유동적이긴 하지만 분명히 빌드 시간을 크게 줄이는 데 성공했습니다.

#### Ccache 적용 전

<img width="273" alt="image" src="https://github.com/Origogi/leetcode/assets/35194820/64388354-1b49-494c-ae2a-481e3588625d">

#### Ccache 적용 후

<img width="273" alt="image" src="https://github.com/Origogi/leetcode/assets/35194820/b20a8ac6-0b77-4196-b928-bf148982b857">

## Reference

- [Ccache](https://ccache.dev/)
- [Using ccache instead of this pod](https://github.com/invertase/firestore-ios-sdk-frameworks/discussions/82)
- [Optimize Flutter iOS Build using ccache](https://dev.to/leehack/optimize-flutter-ios-build-using-ccache-2oi2)