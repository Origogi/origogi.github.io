---
published: true
title: "[iOS][Swift] Combine 정리 (2) - 변환 연산자, 결합 연산자"	
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

### Map

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

### FlatMap

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

### Merge

여러 `Publisher`를 하나의 `Publisher`로 변환합니다.

```swift
let publisher1 = [1, 2, 3].publisher
let publisher2 = [4, 5, 6].publisher
let publisher3 = [7, 8, 9].publisher

let merged = publisher1.merge(with: publisher2, publisher3)

merged.sink { value in
    print(value)
}
```

### Filter

`Publisher`로부터 전달된 값을 필터링합니다.

```swift
[1, 2, 3, 4, 5].publisher
    .filter { $0 % 2 == 0 }
    .sink { print($0) }

// 2, 4  짝수만 출력
```

### CompactMap

`Publisher`로부터 전달된 값을 변환하고, `nil`이 아닌 값만 반환합니다.

```swift
["1", "2", "3", "four", "5"].publisher
    .compactMap { Int($0) }
    .sink { print($0) }

// 1, 2, 3, 5  
// "four"는 Int로 변환할 수 없기 때문에 제외됩니다.
```

## 결합 연산자

`Combine` 에서는 여러 `Publisher`를 결합하는 연산자가 있습니다. 대표적으로 `Zip`, `CombineLatest` 등이 있습니다.

## CombineLatest

여러 `Publisher`로부터 최신 값을 결합합니다.

```swift
let publisher1 = CurrentValueSubject<Int, Never>(1)
let publisher2 = CurrentValueSubject<Int, Never>(2)

let combinedPublisher = publisher1.combineLatest(publisher2)

let cancellable = combinedPublisher.sink { value1, value2 in
    print("Received values: \(value1), \(value2)")
}

publisher1.send(3)
publisher2.send(4)

// Received values: 1, 2
// Received values: 3, 2
// Received values: 3, 4
```

추가로 서로 결합하는 Publisher 가 서로 다른 타입이라도 결합이 가능합니다.

```swift
let publisher1 = CurrentValueSubject<Int, Never>(1)
let publisher2 = CurrentValueSubject<String, Never>("A")

let combinedPublisher = publisher1.combineLatest(publisher2)
let cancellable = combinedPublisher.sink { value1, value2 in
    print("Received values: \(value1), \(value2)")

}

// Received values: 1, A
```

### Zip

`CombineLatest` 와 비슷하지만, `Publisher`로부터 전달된 값이 모두 있을 때만 값을 전달합니다. 즉 두 `Publisher` 중 하나라도 값이 없으면 값을 전달하지 않습니다.

```swift
let publisher1 = CurrentValueSubject<Int, Never>(1)
let publisher2 = CurrentValueSubject<Int, Never>(2)

let combinedPublisher = publisher1.combineLatest(publisher2)

let cancellable = combinedPublisher.sink { value1, value2 in
    print("Received values: \(value1), \(value2)")
}

publisher1.send(3)  // publisher2에 값이 없기 때문에 출력되지 않음
publisher2.send(4)  // publisher1, publisher2 모두 값이 있기 때문에 출력됨

// Received values: 1, 2
// Received values: 3, 4
```

추가로 Publishers 에 확장함수를 이용해서 Zip을 생성할수 있고 3 개 이상의 Publisher 를 결합할 수 있습니다.

```swift
zipPub = Publishers.zip(pub1, pub2)
zipPub = Publishers.Zip3(pub1, pub2, pub3)
```

### SwitchToLatest

switchToLatest()는 Combine 프레임워크에서 사용되는 연산자로, 여러 퍼블리셔를 다룰 때 유용하게 사용됩니다.

동작 원리를 살펴보자면

먼저 Publisher 가 하나 존재하고 이를 Outer Publisher 가 있다고 가정합니다. Outer Publisher 는 일반적인 값이 아닌 또 다른 Publisher 를 방출합니다.

```swift
let outer = PassthroughSubject<AnyPublisher<Int, Never>, Never>()
```


이제 Outer Publisher 가 방출하는 Publisher 를 Inner Publisher 라고 부르겠습니다.

```swift
let inner1 = CurrentValueSubject<Int, Never>(1)
let inner2 = CurrentValueSubject<Int, Never>(2)
```

이제 Outer Publisher 가 Inner Publisher 를 방출하도록 설정합니다.

```swift
outer.send(AnyPublisher(inner1))
```

이제 SwitchToLatest 연산자를 사용하여 Inner Publisher 를 구독하고 Inner Publisher 가 방출하는 값을 출력합니다.

```swift
outer
    .switchToLatest()
    .sink { value in
        print(value)
    }
// 1
```

만약 outer 에서 switchToLastest() 를 사용하지 않고 바로 sink 를 사용하게 되면 Inner publisher 의 값을 구독하지 않고 publisher 자체를 구독하게 됩니다.

```swift
outer
    .sink { value in
        print(value)
    }
// AnyPublisher<Int, Never>
```

그리고 inner1 에 새로운 값을 전달하면 switchToLatest() 는 새로운 값을 출력합니다.

```swift
outer
    .switchToLatest()
    .sink { value in
        print(value)
    }

inner1.send(3)
// 3
```

다음 새로운 inner publsiher 를 변경해보겠습니다.

```swift
outer
    .switchToLatest()
    .sink { value in
        print(value)
    }

outer.send(AnyPublsiher(inner2))  // inner2 로 변경이 되고 inner1 은 무시됩니다. 그리고 inner2 의 값이 출력됩니다.
inner2.send(4) // 4
inner1.send(5) // outer 는 이미 inner2 로 변경되었기 때문에 inner1 의 값은 출력되지 않습니다.
```

switchToLatest()를 사용자 입력에 활용하는 좋은 예는 검색 기능을 구현할 때입니다. 사용자가 검색어를 입력할 때마다 네트워크 요청을 보내야 한다고 가정해 보겠습니다. 이 경우 사용자가 빠르게 입력을 변경할 수 있기 때문에, 이전 검색어에 대한 요청은 취소하고 가장 최근의 검색어에 대한 요청만 처리해야 합니다. switchToLatest()를 사용하면 이러한 동작을 쉽게 구현할 수 있습니다.

```swift
import Combine
import Foundation

// 검색어 입력을 받는 퍼블리셔
let searchTextPublisher = PassthroughSubject<String, Never>()

// 네트워크 요청을 시뮬레이션하는 함수
func search(query: String) -> AnyPublisher<String, Never> {
    // 실제 네트워크 요청 대신 1초 후에 결과를 반환하는 퍼블리셔
    return Just("Results for \(query)")
        .delay(for: 1.0, scheduler: RunLoop.main)
        .eraseToAnyPublisher()
}

// 검색어가 변경될 때마다 새로운 네트워크 요청을 생성하는 퍼블리셔
let searchResultsPublisher = searchTextPublisher
    .map { query in
        search(query: query)
    }
    .switchToLatest() // 가장 최근의 검색 결과만 구독
    .sink(receiveValue: { result in
        print(result)
    })

// 사용자가 입력하는 시뮬레이션
searchTextPublisher.send("Swift")
searchTextPublisher.send("SwiftUI")
searchTextPublisher.send("Combine")

// 이 경우 "Combine"에 대한 검색 결과만 출력됩니다.
```

