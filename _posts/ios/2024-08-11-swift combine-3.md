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


### tryMap

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

예외처리를 위해서는 map 대신 `tryMap` 을 사용해야합니다. 추가적으로 예외를 받아서 처리해야하는 코드를 작성해야합니다.

### catch

예외가 발생했을 시 예외처리를 위한 가장 기본적인 방법은 `catch` 연산자를 사용하는 것입니다.

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

### replaceError

`replaceError` 연산자는 예외가 발생했을 때 대체 값을 방출합니다. catch 연산자보다 더 간단하게 사용할 수 있습니다.

```swift
let cancellable = pub
    .tryMap {
        if $0 == 3 {
            throw CustomError.operationFailed
        }
        return $0
    }
    .replaceError(with: -1)  // -1 을 방출하고 Publisher 를 종료한다.
    .sink {
        print($0)
    }
```

위 코드는 `catch` 와 동일한 결과를 반환합니다.

### retry

`retry` 연산자는 예외가 발생했을 때, 지정된 횟수만큼 재시도합니다.

```swift
let pub = [1, 2, 3, 4, 5].publisher

let cancellable = pub
    .tryMap {
        if $0 == 3 {
            throw CustomError.operationFailed
        }
        return $0
    }
    .retry(2)
    .sink { completion in  // 반드시 completion 을 받아야합니다.
        print(completion)
    } receiveValue: {
        print($0)
    }

// 1
// 2
// 1
// 2
// 1
// 2
// failure(__lldb_expr_87.CustomError.operationFailed) , 두번 재시도했지만 실패
```

retry 를 사용할때 반드시 sink 에서 completion 을 받아야합니다. 그렇지 않으면 컴파일 에러가 발생합니다.