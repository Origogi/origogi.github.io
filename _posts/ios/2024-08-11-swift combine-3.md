---
published: false
title: "[iOS][Swift] Combine 정리 (3) - 예외 처리"	
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
  - Combine

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

해당 포스트는 `Combine` 의 예외 처리에 대해 정리한 내용입니다.


### tryMap - catch

일반적으로 `Publisher` 는 예외를 던지지 못합니다. 아래 코드에서 컴파일 에러가 발생합니다.

```swift
enum CustomError: Error {
    case operationFailed
}

let pub = [1, 2, 3, 4, 5].publisher

let cancellable = pub
    .map {   // 컴파일 에러!!! No exact matches in call to instance method 'map'
        if $0 == 3 {
            throw CustomError.operationFailed
        }
        return $0
    }.sink {
        print($0)
    }
```

예외처리를 위해서는 `tryMap` 과 `catch` 를 사용해야 합니다.

```swift
enum CustomError: Error {
    case operationFailed
}

let pub = [1, 2, 3, 4, 5].publisher

let cancellable = pub
    .tryMap {
        if $0 == 3 {
            throw CustomError.operationFailed
        }
        return $0
    }
    .catch { error in
        print("Error: \(error)")  
        return Just(-1)  // 예외가 발생하면 예외를 처리하고 새로운 값을 방출해야한다. 그리고 새로운 값은 방출되지 않는다.
    }.sink { value in
        print("Value: \(value)")
    }
```
