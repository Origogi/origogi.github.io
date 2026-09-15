---
published: true
title: "[Flutter][번역] Why use RxDart and how we can use with BloC Pattern in Flutter?"
excerpt : " "
layout: single
author_profile: true
read_time: false # read_time을 출력할지 여부 1min read 같은것!
toc: true #Table Of Contents 목차 보여줌
toc_label: "My Table of Contents" # toc 이름 정의
toc_icon: "cog" #font Awesome아이콘으로 toc 아이콘 설정
toc_sticky: true # 스크롤 내릴때 같이 내려가는 목차
categories :
 - Flutter
tags: 
  - Flutter
  - Dart
  - 번역
comments: true
header:
  teaser: https://venturebeat.com/wp-content/uploads/2019/09/google-dart-flutter.png?w=578&strip=all
sitemap :
  changefreq : daily
  priority : 1.0
---
> Original Source : <https://medium.com/flutter-community/why-use-rxdart-and-how-we-can-use-with-BloC-pattern-in-flutter-a64ca2c7c52d>


# Why use RxDart and how we can use with BloC Pattern in Flutter?

![](https://miro.medium.com/max/606/1*YY94QoK1UxPas487Ji615A.png)

다시 만나 반갑습니다! 혹시 Reactive 프로그래밍에 대해 들어 보신적이 있습니까? RxDart는 ReactiveX 기반의 Dart 언어 용 Reactive 프로그래밍 라이브러리입니다. Dart는 Streams와 함께 작동하기에 알맞은 패키지를 이미 가지고 있지만 RxDart는 그 위에 기능을 추가합니다. 그리고, Stream이란 무엇입니까?

## Streams과 Sinks

Stream은 데이터와 이벤트의 흐름을 나타냅니다. 그렇다면 가장 중요한 것은 무엇입니까? Stream을 사용하면 데이터 및 이벤트 변경 사항을 들을 수 있으며 Stream에서 오는 내용을 Listener와 처리 할 수 ​​있습니다. Flutter에서는 어떻게 적용 할 수 있을까요? 예를 들어, Stream과의 interaction에 대한 최신 Snapshot을 기반으로 자체 빌드하는 StreamBuilder라는 Flutter의 Widget이 있습니다. 그리고, 새로운 데이터 흐름이있는 경우 새 데이터를 처리하기 위해 Widget이 새로 고침됩니다.

[Flutter Dev Channel의 Widget Weekly](https://youtu.be/MkKEWHfy99Y)는 StreamBuilder 작동 방식에 대한 훌륭한 내용을 제공합니다.

그리고 Sinks란 무엇일까요? 만약 우리가 데이터 플럭스의 출력을 가지고 있다면, 우리는 또한 입력이 필요힙니다. 그것이 Sinks가 사용되는 것입니다. 간단해 보이지 않습니까? 이제 BloC 패턴에 대해 살펴보고, 어떻게 이 두 개념을 멋진 Flutter 앱으로 결합할 수 있는지 알아봅시다.

{% include image.html url="https://miro.medium.com/max/600/1*ccPKTUTwZtt8HADzCtBLwA.gif" description="Stream of Cats 😁" %}



## BloC Pattern

BloC (Bussiness Logic Component) 패턴은 2018 년 Dart Conference에서 Paolo Soares가 공식적으로 [발표](https://www.youtube.com/watch?v=PLHln7wHgPE)했습니다.

발표 비디오를 본다면 아마도 초기 목적이 다른 플랫폼(Angular Dart)에서 비즈니스 로직과 관련된 코드를 재사용하는 것임을 깨달았을 것입니다.

이 패턴이 추구하는 것은 모든 비즈니스 로직 코드를 UI에서 삭제하고 BloC 클래스에서만 사용하는 것입니다. 

BloC은 책임을 올바른 구성 요소에 두는 것 외에 프로젝트와 코드, 환경 및 플랫폼의 독립성을 가져올수 있습니다. 

그리고 BloC 패턴은 Stream의 사용에만 의존하기 때문에 이제 우리의 이야기는 훨씬 더 이치에 맞을 것입니다.

![](https://miro.medium.com/max/561/1*WJMUsjhRqwj40NQ7ovWJYw.png)

위의 이미지를 보면 흐름을 실감할 수 있습니다. Widget은 Sink를 통해 BloC 클래스로 데이터/이벤트를 전송하고 Stream에서 알림을 받습니다.

Widget에 비즈니스 로직가 없다는 것은 BloC에서 일어난 일이 UI의 책임이 아니라는 것을 의미합니다. 

이 아키텍처를 통해 비즈니스 로직 테스트 케이스들을 더 쉽게 테스트할 수 있도록 합니다. 왜냐하면 비즈니스 로직은 BloC class 에만 존재하기 때문입니다.

## RxDart 살펴보기

RxDart는 현재 0.21.0 버전으로 (이 게시물 작성 시점)에 있습니다. 여기에서는 해당 Libary에서 사용되는 몇몇 Object에 대해 이야기하겠습니다.

### Observable<T> class

Observable을 통해 Widget에 알림을 보내면 이를 관찰하고 데이터 흐름을 처리 할 수 ​​있습니다. RxDart의 관찰 가능한 클래스는 Stream에서 확장되며, 이는 다음과 같은 몇 가지 중요한 사항을 내포하고 있습니다.

- Stream 클래스에 정의 된 모든 메서드는 Observable 에도 있습니다.
- 모든 Observable은 Dart Stream을 입력을 이용하는 API (예 : StreamBuilder Widget 포함)로 전달할 수 있습니다.

### PublishSubject<T> class

이것은 매우 간단합니다. Subject 는 데이터, 오류 및 완료 이벤트를 Listener에게 보낼 수 있습니다. 여기서는 이전에 이야기했던 Sink와 함께 작동합니다. 아래의 코드를 참조하십시오.

~~~dart
PublishSubject<int> subject = new PublishSubject<int>();

/*this listener below will print every integer added to the subject: 1, 2, 3, ...*/
subject.stream.listen(print);
subject.add(1);
subject.add(2);

/*but this listener below will print only the integer added after his initialization: 3, .../*
subject.stream.listen(print);
subject.add(3);
~~~

### BehaviorSubject<T> class

이것은 PublishSubject와 유사합니다. 또한 데이터, 오류 및 완료 이벤트를 Listener 에게 보낼 수 있으며 추가로 Subject에 새로운 Listener가 추가 된다면 가장 최근에 받았던 이벤트를 새로운 Listener가 받을 것입니다.

~~~dart
BehaviorSubject<int> subject = new BehaviorSubject<int>();
subject.stream.listen(print); // prints 1,2,3 
subject.add(1);
subject.add(2);
subject.add(3);

subject.stream.listen(print); // prints 3
~~~

### ReplaySubject<T> class

ReplaySubject를 사용하면 데이터, 오류 및 완료된 이벤트를 Listener에게 전송하는 것도 동일합니다. 그러나 여기서 중요한 차이점이 있습니다. item이 Subject에 추가되면 ReplaySubject가 item을 기록하며 Listener가 Stream을 observe 할 때 기록 된 item이 Listener로 방출됩니다. 아래의 예를 참조하십시오

~~~dart
ReplaySubject<int> subject = new ReplaySubject<int>();

subject.add(1);
subject.add(2);
subject.add(3);

subject.stream.listen(print); // prints 1, 2, 3
~~~

### 실전 단계

이 Post 에서는 RxDart와 BloC 패턴의 원리를 사용하는 간단한 예를 보여줍니다. 시작해 봅시다!

{% include image.html url="https://miro.medium.com/max/500/1*bHkjiqKae77dh1LIj34StQ.gif" description="Now I really need your attention" %}


시작하기에 가장 좋은 방법은 Flutter Hello World 예제입니다. 아마도 당신은 앱의 incresement 기능에 익숙할 것입니다.

하지만 더 많은 것을 만들기 위해 decrement 기능을 만들어 봅시다. 우선 Flutter 프로젝트를 만들고 rxdart를 프로젝트로 가져오십시오.

~~~dart
import 'package:flutter/material.dart';

void main() => runApp(new MyApp());

class MyApp extends StatelessWidget {

  @override
  Widget build(BuildContext context) {
    return new MaterialApp(
      title: 'Flutter Demo',
      theme: new ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: new MyHomePage(title: 'Flutter Demo Home Page'),
    );
  }
}

class MyHomePage extends StatefulWidget {
  MyHomePage({Key key, this.title}) : super(key: key);

  final String title;

  @override
  _MyHomePageState createState() => new _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  int _counter = 0;

  void _incrementCounter() {
    setState(() {
      _counter++;
    });
  }

  void _decrementCounter() {
    setState(() {
      _counter--;
    });
  }

  @override
  Widget build(BuildContext context) {
    return new Scaffold(
      appBar: new AppBar(
      ),
      body: new Center(
        child: new Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            new Text('You have pushed the button this many times:',),
            new Text('$_counter', style: Theme.of(context).textTheme.display1),
          ],
        ),
      ),
      floatingActionButton: new Column(mainAxisAlignment: MainAxisAlignment.end, children: <Widget>[
        new Padding(padding: EdgeInsets.only(bottom: 10), child:
          new FloatingActionButton(
            onPressed: _incrementCounter,
            tooltip: 'Increment',
            child: new Icon(Icons.add),
          )
        ),
        new FloatingActionButton(
          onPressed: _decrementCounter,
          tooltip: 'Decrement',
          child: new Icon(Icons.remove),
        ),
      ])
    );
  }
}
~~~


보시다시피 이 코드는 Count 증가 및 감소 기능을 제공하지만 BloC 패턴 또는 Stream을 적용하지는 않았습니다. 이 코드는 간단하게 동작하지만 내부를 살펴보면 UI 코드에 증가 및 감소라는 두 가지 비즈니스 로직이 있음을 알 수 있습니다.

따라서 이 응용 프로그램이 열심히 일하고 있는 큰 응용 프로그램이지만 요구 사항이 변경되어 증분이 두 개씩 추가되어야한다고 상상해보십시오. 비즈니스 로직 변경 요구 사항이 UI 코드에 영향을 미치지 않아야한다는 점에 동의하십니까 (이 경우)? 그렇다면, 좋습니다! 당신은 그것을 얻었습니다. 그것은 책임을 분리하는 요점입니다.

만약 이 어플리케이션이 매우 크고 많은 일을 한다고 가정하고 요구 사항이 변경되어 Count 증가가 두 개 씩 발생한다고 상상해보십시오. 

비즈니스 로직 변경 요구 사항이 UI 코드에 영향을 미치지 않아야한다는 점에 동의하십니까 (위와 같은 경우에)? 그렇다면, 좋습니다! 당신은 책임을 분리해야 한다는 것을 알게 되었습니다.

이제 지금까지 배운 것을 활용하여 CounterBloC 클래스를 만들어 봅시다.

~~~dart
import 'package:rxdart/rxdart.dart';

class CounterBloC {

  int initialCount = 0; //if the data is not passed by paramether it initializes with 0
  BehaviorSubject<int> _subjectCounter;

  CounterBloC({this.initialCount}){
   _subjectCounter = new BehaviorSubject<int>.seeded(this.initialCount); //initializes the subject with element already
  }

  Observable<int> get counterObservable => _subjectCounter.stream; 

  void increment(){
    initialCount++;
    _subjectCounter.sink.add(initialCount);
  }

  void decrement(){
    initialCount--;
    _subjectCounter.sink.add(initialCount);
  }

  void dispose(){
    _subjectCounter.close();
  }

}
~~~

이제 위의 코드를 설명하겠습니다. 먼저 rxdart 라이브러리를 사용하는 CounterBloC이라는 클래스를 만들었습니다. 이 경우, 우리는 Count가 몇 번부터 시작해야 하는지 알 수 있는 Initial Count를 받아야 합니다. 이 예제에서는 BehaviorSubeject를 선택한 다음 매개 변수가 전달한 데이터로 Subject를 초기화했습니다. 즉, Widget이 Subject의 Listener가되면 Stream을 통해 전달 된 첫 번째 값은 CounterBloC 생성자에 설정된 initialCount가됩니다.

 이제 메소드에 대해 이야기하겠습니다. 이 경우 클래스에는 네 가지 메소드가 있습니다.

- increment () : initialCount를 증가시키고 새 값을 배출하여 Subject Listner에게 보냅니다.
- decrement () : initialCount를 감소시키고 새 값을 배출하여 Subject Listner에게 보냅니다.
- dispose () : 열린 Subject를 닫습니다.
- counterObeservable () : Subject의 Observable, 즉 Stream에서 변경이 발생할 때 Widget에 알리는 데 사용되는 객체를 반환합니다.

이제 BloC 클래스가 만들어졌으니 UI와 통합하는 것을 살펴보겠습니다.

~~~dart
import 'package:flutter/material.dart';
import 'package:BloC_example/BloC/CounterBloC.dart';

void main() => runApp(new MyApp());

class MyApp extends StatelessWidget {

  @override
  Widget build(BuildContext context) {
    return new MaterialApp(
      title: 'Flutter Demo',
      theme: new ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: new MyHomePage(title: 'Flutter Demo Home Page'),
    );
  }
}

class MyHomePage extends StatefulWidget {
  MyHomePage({Key key, this.title}) : super(key: key);

  final String title;

  @override
  _MyHomePageState createState() => new _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {

  CounterBloC _counterBloC = new CounterBloC(initialCount: 0);

  @override
  Widget build(BuildContext context) {
    return new Scaffold(
      appBar: new AppBar(
      ),
      body: new Center(
        child: new Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            new Text('You have pushed the button this many times:'),
            new StreamBuilder(stream: _counterBloC.counterObservable, builder: (context, AsyncSnapshot<int> snapshot){
              return new Text('${snapshot.data}', style: Theme.of(context).textTheme.display1);
            })
          ],
        ),
      ),
      floatingActionButton: new Column(mainAxisAlignment: MainAxisAlignment.end, children: <Widget>[
        new Padding(padding: EdgeInsets.only(bottom: 10), child:
          new FloatingActionButton(
            onPressed: _counterBloC.increment,
            tooltip: 'Increment',
            child: new Icon(Icons.add),
          )
        ),
        new FloatingActionButton(
          onPressed: _counterBloC.decrement,
          tooltip: 'Decrement',
          child: new Icon(Icons.remove),
        ),
      ])
    );
  }

  @override
  void dispose() {
    _counterBloC.dispose();
    super.dispose();
  }
  
}
~~~

UI에서 몇 가지 사항을 변경했습니다.

- 이제 initialCount = 0으로 CounterBloC을 초기화합니다.
- 그런 다음 increment, decrement 메소드을 제거했습니다. 이러한 메소드 구현은 더 이상 UI의 책임이 아닙니다.
- 두 FloatingActionButton을 클릭하면 CounterBloC에서 해당 메소드를 호출합니다.
- 이제 StreamBuilder를 사용하여 화면에 데이터를 표시합니다. CounterBloC 클래스의 counterObservable 를 StreamBuilder의 생성자로 사용했으며 Stream에서 가져온 데이터를 처리하고 적절한 Widget을 리턴하는 builder를 호출합니다.

{% include image.html url="https://miro.medium.com/max/1296/1*ZulQfM1GDwB4p6XTR6tn7A.gif" description="App running
" %}

## 참고 사항 및 결론

BloC, Redux, ScopedModel 등과 같은 상태 관리에 도움이 되는 패턴이 아주 많습니다. 저는 BloC를 가장 좋아한다고 고백하지만, 당신은 어떠한 패턴을 더 선호하는지 의견을 주시기 바랍니다. 지금까지 기사를 읽어 주셔서 감사하며 의견을 보내주십시오. 좀 더 복잡한 예제로 2 부를 원하면 말해주세요.

제 소셜 네트워크는 [LinkedIn](https://www.linkedin.com/in/wiltonribeiro/), [GitHub](https://github.com/wiltonribeiro), [Twitter](https://twitter.com/Willrcn) 입니다. (자유롭게 연락하십시오.)

## References

- https://www.didierboelens.com/2018/08/reactive-programming---streams---BloC/
- https://pub.dartlang.org/documentation/rxdart/latest/