---
published: false
title: "[iOS][Swift] Combine 정리 (4) - Networking"	
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

해당 포스트는 `Combine` 의 `Networking` 처리에 대해 정리한 내용입니다.

## Networking

아래는 `URLSession` 을 이용하여 서버로 부터 데이터를 받고 json 데이터를 디코딩하고 AnyPublisher 로 반환하는 코드입니다.

```swift
struct Post: Codable {
    let userId: Int
    let id: Int
    let title: String
    let body: String
}

func fetchPosts() -> AnyPublisher<[Post], Error> {
    return URLSession.shared.dataTaskPublisher(for: URL(string: "https://jsonplaceholder.typicode.com/posts")!)
        .map(\.data)
        .decode(type: [Post].self, decoder: JSONDecoder())
        .eraseToAnyPublisher()
}

let cancellable = fetchPosts()
    .receive(on: DispatchQueue.main).sink { completion in
    print(completion)
} receiveValue: { posts in
    print(posts)
}
```

### dataTaskPublisher()

`URLSession.shared.dataTaskPublisher(for: URL)` 는 `URLSession` 의 `dataTaskPublisher(for: URL)` 을 이용하여 `URL` 로 부터 데이터를 받아오는 `Publisher` 를 생성합니다.

### eraseToAnyPublisher()

dataTaskPublisher() 의 리턴값은 단순한 `Publisher` 타입이 아닌 좀 더 복잡한 타입입니다. 하지만 데이터를 수신하는 쪽에서는 복잡한 타입이 아닌 단순한 `Publisher` 타입을 원합니다. 이때 `eraseToAnyPublisher()` 를 사용하여 복잡한 타입을 단순한 `Publisher` 타입으로 변환합니다.

만약 `eraseToAnyPublisher()` 를 사용하지 않으면 아래와 같은 타입의 Publisher 가 리턴됩니다.

```swift
func fetchPosts() -> Publishers.Map<Publishers.Decode<URLSession.DataTaskPublisher, [Post], JSONDecoder>, Data> {
    // 실제로는 더 복잡할 수도 있음
}
```

따라서 `eraseToAnyPublisher()` 를 호출하여 사용하기 단순한 `AnyPublisher` 타입으로 변환합니다.

## Error Handling

아래는 네트워크 요청 중 발생하는 에러를 처리하는 코드입니다.

```swift
struct Post: Codable {
    let userId: Int
    let id: Int
    let title: String
    let body: String
}

enum NetworkError: Error {
    case badServerResponse
}

func fetchPosts() -> AnyPublisher<[Post], Error> {
    return URLSession.shared.dataTaskPublisher(for: URL(string: "https://jsonplaceholder.typicode.com/posts")!)
        .tryMap { _, _ in
            print("retries")
            throw NetworkError.badServerResponse

//            guard let httpResponse = response as? HTTPURLResponse, httpResponse.statusCode == 200 else {
//                throw NetworkError.badServerResponse
//            }
//            return data
        }
        .retry(3)
        .decode(type: [Post].self, decoder: JSONDecoder())
        .eraseToAnyPublisher()
}

let cancellable = fetchPosts().receive(on: DispatchQueue.main).sink { completion in
    print(completion)
} receiveValue: { posts in
    print(posts)
}
```

tryMap 연산자를 이용해서 예외를 발생시키고 retry 연산자를 이용해서 지정된 횟수만큼 재시도합니다. 그리고 completion 을 받아서 에러를 처리합니다.

## 두 개의 Publisher(네트워크 요청)를 합치기

두 개의 Publisher 를 합치는 방법은 `CombineLatest` 연산자를 사용하면 됩니다.

아래 코드는 두 개의 Publisher 를 합치는 예제입니다.

```swift
struct MovieResponse: Decodable {
    let Search: [Movie]
}

struct Movie: Decodable {
    let title: String

    private enum CodingKeys: String, CodingKey {
        case title = "Title"
    }
}

func fetchMovies(_ searchTerm: String) -> AnyPublisher<[Movie], Error> {
    guard let url = URL(string: "https://www.omdbapi.com/?s=\(searchTerm)&page=2&apiKey=564727fa") else {
        fatalError("Invalid URL")
    }

    return URLSession.shared.dataTaskPublisher(for: url)
        .map(\.data)
        .decode(type: MovieResponse.self, decoder: JSONDecoder())
        .map(\.Search)
        .eraseToAnyPublisher()
}

let cancellable = Publishers.CombineLatest(fetchMovies("batman"), fetchMovies("superman"))
    .sink(receiveCompletion: { completion in
        print(completion)
    }, receiveValue: { batmanMovies, supermanMovies in
        print("Batman Movies: \(batmanMovies.count)")
        print("Superman Movies: \(supermanMovies.count)")
    })

```

fetchMovies() 함수를 이용해서 두 개의 Publisher 를 생성하고 CombineLatest 연산자를 이용해서 두 개의 Publisher 를 합칩니다. 그리고 두 개의 Publisher 가 모두 값을 방출하면 receiveValue 클로저가 호출됩니다.