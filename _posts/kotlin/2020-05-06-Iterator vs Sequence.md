---
published: true
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

~~~kotlin
val sequence = sequenceOf(1,2,3)
println(sequence.elementAt(0))  // 1
println(sequence.elementAt(1))  // 2
println(sequence.elementAt(2))  // 3
println(sequence.elementAt(0))  // 1
~~~

## 동작 방식 비교

위와 같은 특성의 차이 때문에 map 이나 filter 같은 api 도 동작방식이 차이가 발생하며 동작방식의 차이 때문에 똑같은 결과를 출력하는 코드라 할지라도 성능의 차이가 발생한다.

코드를 보면서 설명하자면 아래 코드는 Iterator를 사용한 코드이다.

~~~kotlin
val words = "The quick brown fox jumps over the lazy dog".split(" ")
//convert the List to a Sequence
val wordsSequence = words.asSequence()

val lengthsSequence = wordsSequence.filter { println("filter: $it"); it.length > 3 }
    .map { println("length: ${it.length}"); it.length }
    .take(4)

println("Lengths of first 4 words longer than 3 chars")
// terminal operation: obtaining the result as a List
println(lengthsSequence.toList())
~~~

그리고 그 다음은 Sequence 를 활용한 예이다.

~~~kotlin
val time2 = measureTimeMillis {
    val words = "The quick brown fox jumps over the lazy dog".split(" ")
    val wordsSequence = words.asSequence()
    val result = wordsSequence.filter { println("filter: $it"); it.length > 3 }
        .map { println("length: ${it.length}"); it.length }
        .take(4)
        .toList()

}
println("Sequence : ${time2} mills")
~~~

위 코드는 차이 점은 sequence 같은 경우 마지막에 toList() 를 사용한다는 점이다.

위 코드를 실행하면 아래와 같다.

~~~
Iterator : 11 mills
Sequence : 4 mills
~~~

왜 이런 차이가 발생하는 지 살펴보면 위 코드는 filter, map, take 라는 3개의 함수를 수행한다.
Iterator 같은 경우 모든 요소에 대해서 filter를 수행하고 새로운 List를 생성한다. 
그 다음 새로 생성된 리스트에 대해서 map를 수행하고 또 다시 List를 생성한다.
마지막으로 take를 수행하여 마지막 결과 List를 생성한다.

위 수행 과정을 다이어그램으로 표시하면 다음과 같다.

![image1](https://kotlinlang.org/docs/images/list-processing.png)

참고로 filter, map, take 코드는 아래와 같이 List를 리턴하고 있음을 볼수 있다.

~~~kotlin
public inline fun <T> Iterable<T>.filter(predicate: (T) -> Boolean): List<T> {
    return filterTo(ArrayList<T>(), predicate)
}
~~~

그러면 Sequence는 어떻게 동작하는지 살펴보면

첫 번째 요소는 filter를 수행하고 그 다음 map 을 수행한다. 그 다음 두 번째 요소는 filter를 수행하고 그 다음 map 을 수행한다. 위 행위를 take 의 갯 수 만큼 수행을 하기에 총 4번을 수행한다. 중요한 점은 함수를 수행할 때 마다 매번 List를 생성하지 않는 다는 점이다.

참고로 filter, map, take 코드는 아래와 같이 Sequence를 다시 리턴하고 있음을 볼수 있다.

~~~kotlin
public fun <T> Sequence<T>.filter(predicate: (T) -> Boolean): Sequence<T> {
    return FilteringSequence(this, true, predicate)
}
~~~

위 수행 과정을 다이어그램으로 표시하면 다음과 같다.

![image1](https://kotlinlang.org/docs/images/sequence-processing.png)

위 과정을 비교를 하면 Sequence는 Iterator에 대해서 불필요한 동작을 수행하지 않고 중간 중간에 새로운 List를 새로 생성하지 않기에 실행 속도는 Sequence 가 더 빠르다고 판단할수 있다.

## 무조건 Sequence를 써야 할까?

결론은 __아니다!__ 라고 말할 수 있다. Sequence 같은 경우는 종단 함수를 수행해야 Sequecne를 수행한다. 종단 함수라 함은 toList, sum 같이 새로운 Sequence 가 아닌 다른 값을 리턴하는 함수이다.

~~~kotlin
public fun <T> Sequence<T>.toList(): List<T> {
    return this.toMutableList().optimizeReadOnlyList()
}
~~~

위 Sequence 코드에서 toList를 제거하면 아무것도 수행하지 않는다.

종단 함수를 만나야 비로소 실행되기 때문에 Sequence는 __lazy evaluation__ 이라는 특성을 가지게 된다.
그리고 단순한 연산에서는 불필요한 오버헤드 때문에 Sequence가 더 느리게 동작하는 경우도 있다.

## 결론

Sequence 나 Iterator는 비슷하지만 서로 다른 특성을 가지고 있기 때문에 현재 코드에서 어떤 게 적합한지를 잘 생각해보고 선택하는 것이 가장 중요하다고 볼수 있다.

## Reference

[코틀린 프로그래밍](http://acornpub.co.kr/book/programming-kotlin)
