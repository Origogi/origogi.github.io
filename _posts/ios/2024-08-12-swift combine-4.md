---
published: true
title: "[iOS][Swift] Combine 정리 (3) - Subject"	
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

해당 포스트는 `Combine` 의 `Subject` 에 대해 정리한 내용입니다.

## Subjects

`Subject` 는 `Publisher` 이자 `Subscriber` 입니다. `Subject` 는 `Publisher` 로서 데이터를 발행할 수 있고, `Subscriber` 로서 데이터를 받을 수 있습니다.

`Subject` 는 다음과 같은 종류가 있습니다.

## PassthroughSubject

`PassthroughSubject` 는 가장 일반적인 `Subject` 입니다. `PassthroughSubject` 는 데이터를 받으면 그대로 다시 발행합니다. 그리고 초기값을 가질 수 없습니다. 그리고 구독을 한 시점 이후에 발행된 데이터만 받을 수 있습니다.

```swift
import Combine

let subject = PassthroughSubject<Int, Never>()

subject.send(1) // nothing happens

let cancellable = subject.sink { value in
    print("Received value: \(value)")
}

subject.send(2) // 0
subject.send(3) //1

```

## CurrentValueSubject

`CurrentValueSubject` 는 초기값을 가질 수 있는 `Subject` 입니다. `CurrentValueSubject` 는 가장 최근의 값을 저장하고, 새로운 `Subscriber` 가 구독을 시작하면 가장 최근의 값을 전달합니다.

```swift
let subject = CurrentValueSubject<Int, Never>(-1)

subject.send(0)

let subscription = subject.sink { value in  // 구독을 시작 한 시점에 가장 최근 값인 0을 받는다.
    print("Received value: \(value)")
}

subject.send(1)
subject.send(2)
```

## Custom Subject

`Subject` 를 상속받아서 커스텀 `Subject` 를 만들 수 있습니다.

아래 코드는 짝수만 발행하는 `EvenNumberSubject` 를 만드는 예제입니다.

```swift
class EvenSubject<Failure: Error>: Subject {
    typealias Output = Int

    private let wrapped: CurrentValueSubject<Int, Failure>

    init(initialValue: Int) {
        let evenInitialValue = initialValue % 2 == 0 ? initialValue : 0

        wrapped = CurrentValueSubject(evenInitialValue)
    }

    func send(_ value: Int) {
        if value % 2 == 0 {
            wrapped.send(value)
        }
    }

    func send(completion: Subscribers.Completion<Failure>) {
        wrapped.send(completion: completion)
    }

    func receive<S>(subscriber: S) where S: Subscriber, Failure == S.Failure, Int == S.Input {
        wrapped.receive(subscriber: subscriber)
    }

    func send(subscription: Subscription) {
        wrapped.send(subscription: subscription)
    }
}

let evenSubject = EvenSubject<Never>(initialValue: 2)
let cancellable = evenSubject.sink { value in.  // 최초 구독하자마자 2를 수신
    print("Received value: \(value)")
}


 evenSubject.send(3) // 3은 짝수가 아니므로 무시
 evenSubject.send(4)  // 4를 수신
 evenSubject.send(5)  // 5는 짝수가 아니므로 무시
```