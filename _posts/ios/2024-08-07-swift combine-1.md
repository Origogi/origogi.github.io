---
published: true
title: "[iOS][Swift] Combine 정리 (1)"	
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

## Combine 사용 예시

### Simple Example

```swift
import Combine
import UIKit

let publisher = Just("Hello Combine")

let cancellable = publisher.sink { value in
    print(value)
}

cancellable.cancel()
```

- Just는 값을 한 번만 발행하는 Publisher입니다. 
- sink 함수는 Subscriber를 생성하고, 값을 받아 처리합니다. 값을 받아 처리하 기 위해 sink 함수에 클로저를 전달합니다.
- sink 함수의 리턴값은 `AnyCancellable` 객체입니다. 이 객체를 통해 구독을 취소할 수 있습니다.

### Array Example

```swift
let numbers = (1 ... 10).publisher
let doublePublisher = numbers.map { Double($0) }

numbers.sink { value in
    print(value)
}

doublePublisher.sink { value in
    print(value)
}
```
참고로 모든 데이터가 소비되면 `AnyCancellable` 객체가 자동으로 해제됩니다.

### Timer Example

```swift 
let timerPublisher = Timer.publish(every: 1, on: .main, in: .common)

timerPublisher
    .autoconnect()
    .sink { date in
        print(date)
    }
```

- `Timer.publish`는 주어진 간격으로 타이머를 생성하는 Publisher입니다.
- every 매개변수는 타이머의 간격을 나타냅니다.
- on 매개변수는 타이머가 실행되는 스레드를 나타냅니다. 여기서 .main은 메인 스레드를 나타냅니다. 즉 해당 클로저에서 ui 업데이트가 가능합니다.
- in 매개변수는 타이머가 실행되는 모드를 나타냅니다. .common과 .default가 있지만 차이점은 없습니다.

#### connect() vs autoconnect()

connect() 함수는 Publisher를 수동으로 연결하는 함수입니다. 이 함수를 호출해야만 Publisher가 값을 발행합니다. 반면 autoconnect() 함수는 Subscriber가 생기면 자동으로 Publisher를 연결하는 함수입니다.

모든 publisher 에서 사용할수 없으며, connectable publisher 에서만 사용할 수 있습니다.
대표적인 connectable publisher로는 Timer가 있습니다.

```swift
final public class TimerPublisher : ConnectablePublisher {
  ///
}
```

선택 기준

- connect(): 세밀한 제어가 필요하거나, 여러 구독자를 설정한 후 동시에 시작해야 하는 경우
- autoconnect(): 간단한 사용 사례, 즉시 시작이 필요한 경우

사용 예시

__connect()__

```swift
let publisher = Timer.publish(every: 1, on: .main, in: .common)
let cancellable = publisher
    .sink { date in
        print(date)
    }
// 나중에 원하는 시점에 시작
let connection = publisher.connect()
```

__autoconnect()__

```swift
let cancellable = Timer.publish(every: 1, on: .main, in: .common)
    .autoconnect()
    .sink { date in
        print(date)
    }  // 바로 시작
```

## .store(in: &set) 메소드

Combine 프레임워크를 사용하다 보면 자주 마주치는 `store(in:)` 메서드에 대해 알아보겠습니다. 이 메서드는 구독(subscription) 관리에 매우 유용한 도구입니다.

### store(in:) 메서드란?

`store(in:)` 메서드는 `AnyCancellable` 객체를 지정된 컬렉션에 저장하는 Combine의 유틸리티 메서드입니다. 주로 구독의 수명 주기를 관리하고 메모리 누수를 방지하는 데 사용됩니다.

### 주요 특징

1. **목적**: 구독의 수명 주기 관리 및 메모리 누수 방지
2. **작동 방식**: `AnyCancellable` 객체를 지정된 컬렉션(주로 `Set<AnyCancellable>`)에 추가
3. **자동 취소**: 컬렉션이 해제될 때 저장된 모든 구독도 자동으로 취소됨

> `AnyCancellable` 은 deinit 될 때 자동으로 구독을 취소하는 객체입니다. 따라서 cacellable 프로퍼티가 deinit 될 때, 구독도 자동으로 취소됩니다.

__참고__:
[[iOS]Combine의 Cancellable 딥다이브(1)](https://velog.io/@kimscastle/iOSCombine%EC%9D%98-Cancellable-%EC%82%AC%EC%9A%A9-%EB%B0%A9%EB%B2%951)

### 사용 예시

```swift
@main
struct MyApp: App {
    private var cancellables: Set<AnyCancellable> = []

    init() {
        NotificationCenter.default.publisher(for: UIDevice.orientationDidChangeNotification)
            .sink { _ in
                let currentOrientation = UIDevice.current.orientation
                print("Orientation changed to \(currentOrientation)")
            }.store(in: &cancellables)
    }

    var body: some Scene {
        WindowGroup {
            ContentView()
        }
    }
}
```

여기서 store() 를 사용하지 않으면, NotificationCenter.default.publisher(for: UIDevice.orientationDidChangeNotification) 의 구독이 즉시 해제되어, sink 클로저가 호출되지 않습니다.

### 장점

1. 코드 간결성 : 구독 취소 로직을 별도로 작성할 필요 없음
2. 자동 관리 : 뷰 컨트롤러 들이 해제될 때, 저장된 모든 구독도 자동으로 취소됨
3. 다중 구독 관리 : 여러 개의 구독을 한 번에 관리할 수 있음

## 예외 처리

```swift
enum NumberError: Error {
    case operationFailed
}

let numberPublisher = [1, 2, 3, 4, 5].publisher

let doublePublisher = numberPublisher.tryMap { num in
    if num == 4 {
        throw NumberError.operationFailed
    }

    return num * 2
}.catch { error in
    if let error = error as? NumberError {
        print("Error: \(error)")
    }
    return [1, 2, 3, 4].publisher
}

let subscriber = doublePublisher.sink { value in
    print(value)
}
```

`tryMap`은 `map`과 유사하지만, 클로저가 에러를 던질 수 있습니다.

```swift
public func tryMap<T>(_ transform: @escaping (Self.Output) throws -> T) -> Publishers.TryMap<Self, T>
```

`catch`는 에러를 처리하고, 새로운 `Publisher` 를 반환합니다. 그리고 남은 Publisher 는 버려지고 catch 블록에서 반환된 Publisher 가 대신 사용됩니다.

```swift

아니면 catch를 사용하지 않고, sink에서 에러를 처리할 수도 있습니다.

```swift
let numberPublisher = [1, 2, 3, 4, 5].publisher

let doublePublisher = numberPublisher.tryMap { num in
    if num == 4 {
        throw NumberError.operationFailed
    }

    return num * 2
}

let subscriber = doublePublisher.sink { completion in
    print("Completion: \(completion)")
} receiveValue: { value in
    print("Value: \(value)")
}

```

위 코드를 실행 한 결과는 다음과 같습니다.

```
Value: 2
Value: 4
Value: 6
Completion: failure(__lldb_expr_31.NumberError.operationFailed)
```