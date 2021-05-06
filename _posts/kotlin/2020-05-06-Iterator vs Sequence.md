---
published: false
title: "[Kotlin] Iterator vs Sequence"
excerpt : " "
layout: single
author_profile: true
read_time: false
toc: true
toc_label: "My Table of Contents"
toc_icon: "cog"
toc_sticky: true
categories :
  - Kotlin
tags: 
  - Kotlin
  - kotlin

comments: true
header:
  # overlay_image: https://cf.festa.io/img/2019-9-27/a6c0b457-c336-42ba-b06e-462de90ada91.jpg
  # overlay_filter: 0.5
  # teaser: https://cf.festa.io/img/2019-9-27/a6c0b457-c336-42ba-b06e-462de90ada91.jpg
sitemap :
  changefreq : daily
  priority : 1.0
---

Kotlin 이전에 Java에 익숙하신 분들은 Iterator에 대해서 잘 알고 많이들 사용하였을 것이다. Iterator 란 Collection에 저장되어 있는 요소(아이템)을 차례대로 순회하기 위해서 사용한다.
Kotlin 에서도 마찬가지로 Iterator 도 지원할 뿐만 아니라 Sequence 라는 인터페이스를 제공한다.

단순히 요소들을 순회하기 위한 목적이라면 Iterator 와 Sequence 의 차이점을 없을 것이라 생각할지도 모른다. 하지만 분명 두 개의 인터페이스는 다르고 동작 방식이 다르다.
심지어 특정 조건에서 성능 차이가 발생하기도 한다. 해당 포스트에서 Iterator 와 Sequence의 차이점을 정리해보도록 하자.

## Iterator

먼저 Interator의 interface를 살펴보자.

~~~kotlin
public interface Iterator<out T> {
    public operator fun next(): T
    public operator fun hasNext(): Boolean
}
~~~

다음 Iterator의 특징에 대해서 살펴보면

첫 번째, Iterator의 가장 큰 특징은 현재 Position을 기억을 하고 있다.

next() 를 호출하면 현재 Position이 가리키는 요소를 리턴하고 Position 값을 한칸 이동한다. 여기서 중요한 점은 Position은 이전 위치로 이동을 할수가 없다.  
아래 코드를 참고로 보면 iterator 변수는 현재 Position을 기억하고 있고 next() 를 호출할 때 마다 Position을 업데이트한다.

~~~kotlin
val iterator = listOf(1,2,3).iterator()
println(iterator.next()) // 1
println(iterator.next()) // 2
println(iterator.next()) // 3
println(iterator.next()) // Exception
~~~

추가로 Position 이 끝에 도달했을 때 next() 를 호출하면 __NoSuchElementException__ 이 발생하기 때문에 보통 next() 를 호출하기 전에 hasNext() 를 호출하여 안전한지를 확인한다.

뒤에 설명하겠지만 Sequence에서는 현재 Position을 저장하고 있지 않는다. 이것이 Iterator와 Sequence의 차이점 중 하나이다.

두 번째, 특정 Index에 바로 접근하지 못한다. 만약 List에서 3번 째에 있는 요소를 가져오고 싶다면 next() 를 3번 호출해야 접근이 가능하다.

## Sequence

다음 Sequence에 대해서 살펴보겠다. 
Sequence는 주로 List나 set 같이 iterator를 가지고 있는 Collection으로 부터 asSequence() 를 호출하거나 sequenceOf() 를 호출해서 Sequence를 생성한다.

~~~kotlin
val sequence1 = sequenceOf(1,2,3)
val sequence2 = listOf(1,2,3).asSequence()
~~~

다음 Sequence의 주요한 특징에 대해 살펴보면

첫 번째, Position 를 저장하지 않는다. 즉 Sequence는 사용할 때마다 reset이 되어 처음부터 접근하게 된다.
두 번째, 특정 Index에 바로 접근이 가능하다. 아래 코드와 같이 elementAt() 를 사용하여 요소를 읽을수 있으며 Iterator의 next() 같은 경우 계속 호출하면 Exception이 발생하지만 elementAt() 은 계속 호출해도 Exception은 발생하지 않는다.



  
## Reference

[코틀린 프로그래밍](http://acornpub.co.kr/book/programming-kotlin)
