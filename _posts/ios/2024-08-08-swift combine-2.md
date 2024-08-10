---
published: false
title: "[iOS][Swift] Combine 정리 (2)"	
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

![banner](https://github.com/user-attachments/assets/2e217acb-c4ae-4bb8-8747-f85e5d15a6e5)

## 서론

해당 포스트는 `Combine` 에 대해 정리한 두 번째 포스트입니다.

## 변환 연산자

대표적인 변환 연산자는 `map`, `flatMap`, `merge` 등이 있습니다.

## Map

가장 기본적인 변환 연산자로, `Publisher`로부터 전달된 값을 변환합니다.
정수형인 정수 값을 받아서 2배로 변환할수 있고 심지어 정수형이 아닌 다른 타입으로 변환할 수도 있습니다.

```swift
[1, 2, 3, 4, 5].publisher
    .map { $0 * 2 }
    .sink { print($0) }

// 2, 4, 6, 8, 10

[1, 2, 3, 4, 5].publisher
    .map { "Value is \($0)" }
    .sink { print($0) }

// Value is 1, Value is 2, Value is 3, Value is 4, Value is 5
```

## FlatMap

`Publisher`로부터 전달된 값을 다른 `Publisher`로 변환합니다.
`map` 은 단일 값을 반환하지만, `flatMap` 은 여러 값 즉 새로운 `Publisher`를 반환합니다.
하지만 sink로 값을 받을 때는 단일 값으로 받습니다.

```swift
let namePublisher = ["Alice", "Bob", "Charlie"].publisher

let flatMapPublisher = namePublisher.flatMap { name in
    ["Hello, \(name)!", "Hi, \(name)!"].publisher
}

flatMapPublisher.sink { value in
    print(value)
}
```

## Merge

여러 `Publisher`를 하나의 `Publisher`로 병합합니다.

```swift
let publisher1 = [1, 2, 3].publisher
let publisher2 = [4, 5, 6].publisher
let publisher3 = [7, 8, 9].publisher

let merged = publisher1.merge(with: publisher2, publisher3)

merged.sink { value in
    print(value)
}
```

## Filter

`Publisher`로부터 전달된 값을 필터링합니다.

```swift
[1, 2, 3, 4, 5].publisher
    .filter { $0 % 2 == 0 }
    .sink { print($0) }

// 2, 4  짝수만 출력
```

## CompactMap

`Publisher`로부터 전달된 값을 변환하고, `nil`이 아닌 값만 반환합니다.

```swift
["1", "2", "3", "four", "5"].publisher
    .compactMap { Int($0) }
    .sink { print($0) }

// 1, 2, 3, 5  
// "four"는 Int로 변환할 수 없기 때문에 제외됩니다.
```