---
published: false
title: "[iOS][Swift] Combine 정리"	
excerpt : " "	
layout: single	
classes: wide
author_profile: true
read_time: false
toc: true
toc_label: "My Table of Contents"
toc_icon: "cog"
toc_sticky: true
categories :	
 - iOS	
tags: 	
  - iOS
  - Swift

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

해당 포스트는 `Combine` 에 대해 정리한 내용입니다.

## Combine 이란?

- `Combine`은 Swift에서 비동기 및 이벤트 기반 코드를 작성하는 데 사용되는 프레임워크입니다.
Combine은 비동기 및 이벤트 기반 코드를 보다 간소화되고 읽기 위운 방식으로 처리하도록 설계되었습니다.
- 애플은 WWDC 2019 에 첫 공개되었고 반응형 프로그래밍을 간소화시키는 것이 목표입니다.

## Key Concepts

Publisher, Subscriber, Operator, Subject

### Publisher

- `Publisher`는 값을 생성하고 전달하는 객체입니다.
- 대표적인 예 : 센서, API 호출, 사용자 입력 등

### Subscriber

- `Subscriber`는 `Publisher`로부터 값을 받아 처리하는 객체입니다.
- 데이터 스트림을 구독하고, 값을 받아 처리합니다.
- 대표적인 예 : UI 컴포넌트, Data Processors

### Operator

- `Operator`는 `Publisher`로부터 전달된 값을 변환하거나, 조작하는 객체입니다.
- 예제 : `map`, `filter`, `merge`, `combineLatest` 등

### Subject

- `Subject`는 `Publisher`와 `Subscriber`의 역할을 모두 수행할 수 있는 객체입니다.
- 동시에 여러 `Subscriber`에게 값을 전달할 수 있습니다. (Multicast)
- 예제 : 실시간 데티어 스트리밍, 사용자 인터페이스 업데이트

## Combine 장점

- 비동기 코드를 간소화하고, 읽기 쉽게 만들어줍니다.
- 쉬운 데이터 조작
- 확장성과 응답성이 뛰어납니다. (이벤트가 발생하면 구독자는 이벤트를 수신한 다음 사용자 인터페이스를 업데이트할 수 있습니다.)