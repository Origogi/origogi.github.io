---
published: true
title: "[Flutter][번역] Flutter internals (2)"
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
comments: true
header:
  overlay_image: assets/images/flutter_dart.png
  overlay_filter: 0.5
  teaser: https://venturebeat.com/wp-content/uploads/2019/09/google-dart-flutter.png?w=578&strip=all
sitemap :
  changefreq : daily
  priority : 1.0
---
> Original Source : <https://www.didierboelens.com/2019/09/flutter-internals>
> <br>Author : boeledi

## Part 2: from Widgets to pixels

이제 내부 동작에 대한 기본을 소개 했으므로 이제 Widget에 대해 이야기 할 차례입니다.

모든 Flutter 문서에서 모든 것이 Widget으로 통합니다.

그것은 거의 정확하지만 좀 더 정확하기 위해서는 오히려 다음과 같이 말하고 싶습니다.

> 개발자의 관점에서 레이아웃 및 유저와 상호작용 관련된 모든 것은 Widget을 통해 수행됩니다.

Widget을 사용하면 개발자가 화면의 일부를 dimensions, content, layout 및 상호 작용 측면에서 정의 할 수 있기 때문에 훨씬 더 많은 기능이 있습니다. <br>
실제로 Widget이란 정확히 무엇일까요?

### Immutable Configuration

Flutter 소스 코드를 읽으면 Widget 클래스의 다음과 같이 정의되어 있습니다.

~~~dart
@immutable
abstract class Widget extends DiagnosticableTree {
  const Widget({ this.key });

  final Key key;

  ...
}
~~~

`@immutable` annotation 은 매우 중요하며 Widget 클래스의 모든 변수는 FINAL이어야합니다. 따라서 일단 인스턴스화되면 Widget은 더 이상 내부 변수를 조정할 수 없습니다.

Flutter로 개발할 때 아래와 같이 Widget을 사용하여 화면의 구조를 정의합니다…

~~~dart
Widget build(`BuildContext` context){
    return SafeArea(
        child: Scaffold(
            appBar: AppBar(
                title: Text('My title'),
            ),
            body: Container(
                child: Center(
                    child: Text('Centered Text'),
                ),
            ),
        ),
    );
}
~~~

이 sample은 7 개의 Widget을 사용하며 계층 구조를 구성합니다. 위 코드를 다음 그림과 같이 단순화 시킬수 있습니다.

![](https://www.didierboelens.com/images/internals_widgets_tree.png)

위 그림과 같이 SafeArea가 Tree의 root 인 것 처럼 보입니다.

### The forest behind the tree

아시다시피 Widget 자체는 다른 Widget의 집합 일 수 있습니다. 예를 들어 이전 코드를 다음과 같이 작성할 수 있습니다.

~~~dart
Widget build(`BuildContext` context){
    return MyOwnWidget();
}
~~~

위 코드는 `MyOwnWidget`이라는 Widget 자체가 SafeArea, Scaffold를 Rendering한다고 가정합니다.

### Tree에서 Element의 개념

제가 왜 이것을 언급했는지 생각해 보았습니끼?

나중에 알게 되겠지만, Device에 Rendering 할 이미지를 구성하는 픽셀을 생성하기 위해서는 화면을 구성하는 모든 작은 부분을 자세히 알아야 하고, 모든 부분을 결정하려면 모든 Widget을 전개(inflate)할 것을 요청해야 합니다.

이것을 설명하기 위해 러시아 인형의 원리를 생각해보면, 처음에는 닫힌 인형 만 보이지만 뚜껑을 열어보면 다른 인형에는 다른 인형이 포함 되어 있는 것을 볼수 있습니다.


![](https://www.didierboelens.com/images/internals_russian_dolls.png)

Flutter가 일부에서 전체로 모든 Widget을 전개했을 때, 그것은 전체에서 일부인 다른 러시아 인형을 얻는 것과 유사할 것입니다.

다음 diagram은 이전 코드에 해당하는 Full Widget 계층 구조의 일부를 보여 줍니다. 노란색으로 코드에 언급된 Widget을 강조하여 Partial Widget Tree에서 찾을 수 있도록 하였습니다.

![](https://www.didierboelens.com/images/internals_inflated_widgets.png)

> **중요 설명**
>
> `Widget Tree`라는 말은 프로그래머들이 Widget을 사용하기 때문에 이해하기 쉽게 하기 위해서 사용되지만 실제 Flutter에는 Widget Tree가 없습니다!<br><br>
>그 대신 `Element Tree`라고 말해야 합니다.

이제 Element의 개념에 대해 알아볼 시간입니다.

> 각 Widget마다 하나의 Element에 해당합니다. Element는 서로 연결되어 Tree를 형성합니다. 따라서 Element는 Tree에서 무언가를 참조합니다.

Element를 parent와 잠재적으로 child가 있는 Node로 생각하십시오. parent 관계를 통해 서로 연결되어 Tree 구조를 얻습니다.

![](https://www.didierboelens.com/images/internals_element.png)

위 그림에서 볼 수 있드시 Element는 하나의 Widget을 가리키고 RenderObject를 가리킬 수도 있습니다.

> 참고로 Element는 Element를 생성한 Widget를 참조합니다.

정리를 하자면
- Widget Tree는 없지만 Element Tree가 있습니다.
- Widget에 의해 Element가 생성됩니다.
- Element는 그것을 만든 Widget을 참조합니다.
- Element는 parent와 연결이 되어 있습니다. 그리고 Element에는 child가 있을 수 있습니다
- Element는 RenderObject를 참조 할 수도 있습니다.

![](https://www.didierboelens.com/images/internals_3_trees.png)

보시다시피 Element Tree는 Widget과 RenderObject 사이를 연결해 줍니다.

그렇다면 Widget이 Element를 생성하는 이유는 무엇일까요?

### 3 main categories of Widgets

Flutter에서 Widget은 3 가지 main Category로 나뉩니다. 개인적으로 Category 를 다음과 같이 부릅니다.

- The Proxies

  이 Widget의 ​​주요 역할은 Widget에 제공해야하는 정보를 보유하는 것입니다.
  proxy widget가 Tree의 특정 part에서 root가 되는 형식을 가집니다.
  일반적인 예로 InheritedWidget 또는 LayoutId가 있습니다.

  이 Widget 은 사용자 인터페이스에 직접 통신하지 않지만 다른 Widget들에게 정보를 추가적인 정보를 제공하기 위해 사용이 됩니다.

- The Renderers

    이 Widget들은 화면의 레이아웃과 직접 관련이 있습니다.

  - Dimensions
  - Position
  - Layout, rendering

  일반적인 예는 다음과 같습니다. 
    - Row, Column, Stack but also Padding, Align, Opacity, RawImage…

- Components.

  Components 는 dimensions, positions과 관련된 최종 정보를 직접 제공하는 것이 아니라 최종 정보를 얻기 위해 사용될 데이터(또는 힌트)를 제공하는 Widget입니다.

  일반적인 예는 다음과 같습니다. 

   - RaisedButton, Scaffold, Text, GestureDetector, Container…

![](https://www.didierboelens.com/images/internals_widgets_categories.png)

다음 [PDF](http://www.example.com/)에는 Category 별로 로 재구성한 Widget들이 나열 되어 있습니다.

Category 별로 Widget을 나누는 것이 중요한 이유는 Element의 Type과 연관이 되어 있기 때문입니다.

### Element types

Element는 아래 그림과 같이 나눌수 있습니다.

![](https://www.didierboelens.com/images/internals_element_types.png)

Elements는 크게 두 가지 타입으로 나눌수 있습니다.

- ComponentElement
    이 Element는 시각적 Rendering부분과 직접적으로 일치하지 않습니다.

- RenderObjectElement
    이 Element는 Rendering 된 화면의 일부에 해당합니다.

### Widget가 Element는 어떻게 연동이 되는 지

> Flutter 전체 Mechanism 은 Element 또는 RenderObject를 Invalidate 하는 데 의존합니다.

Element를 Invalidate 하는 방법은 아래와 같습니다.

- `setState` 를 사용하여 `StatefulElement`을 Invalidate를 합니다. (StatefulWidget이라고 말하지 않은 것에 대해 주의하십시오)
- 다른 proxyElement에 의해 전달되는 알림을 통해 (예를 들어 InheritedWidget), 해당 proxyElement에 의존하는 Element를 Invalidate합니다.

Invalidate의 결과는 해당 Element가 dirty Element 리스트에서 참조됩니다.

RenderObject를 Invalidate하면 Element 구조에 변경 사항이 적용되지 않지만 RenderObject 수준에서 변경이 발생합니다.

- dimensions, position, geometry 변경
- 다시 그려야 하는 경우 예를 들어 배경색을 변경하거나 폰트 변경

이러한 Invalidate의 결과는 해당 RenderObject가 다시 작성되거나 다시 그려 져야하는 RenderObject List에서 참조가 됩니다.

Invalidate 유형에 관계없이 SchedulerBinding은 Flutter Engine에 새 프레임을 예약하도록 요청합니다.

Flutter Engine이 SchedulerBinding을 깨워 마술 같은 일이 발생합니다.

### onDrawFrame()

이 기사의 앞 부분에서 SchedulerBinding에는 2 가지 주요 역할이 있으며 그 중 하나는 프레임 재 구축과 관련하여 Flutter Engine에서 생성 한 요청을 처리 할 준비가 되어 있음을 알려 주는 것이라고 언급했습니다. 이 단락에서 좀 더 자세히 살펴보겠습니다.

아래의 Sequence Diagram 은 SchedulerBinding이 Flutter Engine으로부터 `onDrawFrame()` 요청을 수신 할 때 발생하는 상황을 보여줍니다.

![](https://www.didierboelens.com/images/internals_ondrawframe.png)

**Step 1: the elements**

WidgetsBinding이 호출 된 후 먼저 Element와 관련된 변경 사항을 체크합니다.

BuildOwner가 Element Tree 처리를 담당하므로 WidgetsBinding은 buildOwner의 buildScope 메소드를 호출합니다.
이 메소드는 Invalidate 된 Element 목록 (= dirty)을 순회하고 rebuild 하도록 요청합니다.

rebuild() 메소드의 주요 역할은 다음과 같습니다.

1. Element를 rebuild()하도록 요청하면 대부분의 경우 해당 Element가 참조하는 Widget의 build() 메소드를 호출합니다. (= method Widget build(`BuildContext` context){…}). build() 메소드는 새로운 Widget를 반환합니다.
2. Element에 child이 없으면 새 Widget이 Inflate 되고 그렇지 않으면
3. 새로운 Widget은 Element의 child이 참조한 Widget과 비교합니다.
    - 만약 Widget 간의 Type과 Key가 동일하다면 Child element는 유지된 채로 업데이트 됩니다.
    - 만약 Widget 간의 Type과 Key가 동일하지 않는다면, Child element는 버려지고 새로운 widget이 inflate 됩니다.
4. Widget의 Inflate 은 새로운 Element를 생성합니다, 그리고 Element의 새 child으로 mount됩니다. (mounted = Element Tree에 삽입)

다음 애니메이션은이 위 과정을 좀 더 시각적으로 표현합니다.

![](https://www.didierboelens.com/images/internals_ondrawframe.gif)

**Widget inflating 참고사항**

Widget이 Inflate된 경우 Widget Category에 의해 정의된 특정 유형의 새로운 Element를 생성하도록 요청된다.

그러므로,

- InheritedWidget 은 InheritedElement를 생성
- StatefulWidget 은 StatefulElement를 생성
- StatelessWidget 은 StatelessElement 생성
- InheritedModel 은 InheritedModelElement 생성
- InheritedNotifier 은 InheritedNotifierElement 생성
- LeafRenderObjectWidget 은 LeafRenderObjectElement 생성
- SingleChildRenderObjectWidget 은 SingleChildRenderObjectElement 생성
- MultiChildRenderObjectWidget 은 MultiChildRenderObjectElement 생성 
- ParentDataWidget 은 ParentDataElement 생성

이러한 각 Element는 category 별로 고유 한 동작을 수행합니다.

예를 들어

- StatefulElement는 초기화시 widget.createState() 메소드를 호출하여 State를 생성하고 Element에 링크합니다.
- RenderObjectElement 타입은 Element가 마운트 될 때 RenderObject를 생성합니다.이 RenderObject는 Render Tree에 추가되고 Element에 링크됩니다.

**Step 2: RenderObjects**

Dirty Element와 관련된 모든 작업이 완료되면 Element Tree는 이제 안정적이며 Rendering 과정을 고려할 때 입니다.

RendererBinding은 Rendering Tree 처리를 담당하므로 WidgetsBinding은 RendererBinding의 drawFrame 메서드를 호출합니다.

아래 Diagram은 drawFrame() 요청 중에 수행되는 일련의 동작을 보여줍니다.

![](https://www.didierboelens.com/images/internals_drawframe_rendering.png)

이 단계 동안 다음 동작이 수행됩니다.

- Dirty로 표시된 각 RenderObject는 레이아웃을 수행하도록 요청됩니다.(dimensions, geometry를 계산하는 것을 의미)
- 다시 그릴 필요가 있다고 표시된 RenderObject는 RenderObject의 레이어를 사용하여 다시 그리게 됩니다.

- 결과 씬(scene)을 생성하여 Flutter 엔진으로 전송하여 Device 화면으로 전송하도록 한다.

- 마지막으로 Semantics도 업데이트되어 Flutter Engine으로 전송된다.

이러한 동작의 flow가 끝나면 Device 화면이 업데이트됩니다.

## Part 3: Gesture 처리

Guesture (= 유리 위의 손가락 관련 이벤트)는 Guesture Binding에 의해 처리됩니다.

Flutter Engine이 Guesture 관련 이벤트와 관련된 정보를 window.onPointerDataPacket API를 통해 보내면 GestureBinding이 이를 가로 채고 버퍼링을 진행하며 다음 과정을 수행합니다.

1. Flutter Engine에서 출력 된 좌표를 Device 픽셀 비율과 일치하도록 변환 한 다음
2. RenderView에 이벤트 좌표를 포함하는 모든 RenderObject 목록을 제공하도록 요청합니다.
3. 그런 다음 해당 RenderObject 목록을 순회하여 관련 이벤트를 각각에 전달합니다.
4. RenderObject가 해당 이벤트를 기다리고 있다면 그것을 처리합니다.

이 설명에서 RenderObjects가 얼마나 중요한지 직접 확인할 수 있습니다.

## Part 4: Animations
이 기사의 마지막 부분은 애니메이션 개념, 특히 `Ticker` 개념에 중점을 둡니다.

애니메이션을 시작할 때 일반적으로 AnimationController 또는 이와 유사한 Widget 또는 구성 Element를 사용합니다.

Flutter에서 애니메이션과 관련된 모든 것은 `Ticker`의 개념을 참고합니다.

Ticker는 활성화 된 경우 단 한 가지만 수행합니다. SchedulerBinding이 콜백을 등록하도록 요청하고 다음에 사용 가능한 경우 Flutter Engine에 다시 호출하도록 요청합니다".
Flutter Engine이 준비되면 `onBeginFrame` 요청을 통해 SchedulerBinding을 호출합니다.

SchedulerBinding은 이 요청을 가로 챈 다음 Ticker callback 목록을 순회하고 각각 item을 호출합니다.

각 tikcer tick 은 이 이벤트에 관심이 있는 모든 컨트롤러에 의해 intercepted 처리됩니다. 애니메이션이 완료되면 Ticker가 "비활성화"되고, 그렇지 않으면 Ticker가 다른 콜백을 예약하도록 SchedulerBinding을 요청합니다.

## Global Picture

이제 Flutter 내부의 작동 방식을 살펴 봤으므로 다음은 전체적인 그림입니다.

![](https://www.didierboelens.com/images/internals_big_picture.png)

## `BuildContext`

다른 Element 유형을 표시하는 Diagram을 기억한다면 대부분 기본 Element의 signature를 알 수 있습니다.

~~~dart
abstract class Element extends DiagnosticableTree implements `BuildContext` {
    ...
}
~~~

다음은 유명한 `BuildContext`입니다.

`BuildContext`란 무엇일까요?

`BuildContext`는 Element에서 구현할 수 있는 일련의 getter와 방법을 조화롭게 정의하는 인터페이스입니다.

특히 `BuildContext`는 `StatelessWidget` 및 `StatefulWidget`의 build() 메서드 또는 `StatefulWidget` State 객체에서 주로 사용됩니다.

> `BuildContext`는 그 자체를 나타내는 Element 자체입니다.
> - 빌드중인 Widget (빌드 또는 빌더 메소드 내)
> - 컨텍스트 변수를 참조하는 State 에 연결된 StatefulWidget

이는 대부분의 개발자가 모르는 상태에서도 지속적으로 Element를 처리하고 있음을 의미합니다.

### How useful the `BuildContext` can be?

`BuildContext`는 Widget과 관련된 Element뿐만 아니라 Tree의 Widget 위치에도 해당하므로 `BuildContext`는 다음과 같은 경우에 매우 유용합니다.

- Widget에 해당하는 RenderObject의 참조를 얻습니다 (또는 Widget이 렌더러가 아닌 경우 하위 Widget).
- RenderObject의 사이즈를 얻을 때
- `.of()` 메소드를 구현하는 모든 Widget에서 실제로 사용됩니다.(예를 들어 MediaQuery.of(context), Theme.of(context)…)

### Just for the fun…

우리는 `BuildContext`가 Element라는 것을 이해 했으므로 재미를 위해 그것을 사용하는 또 다른 방법을 보여주고 싶었습니다.

아래 코드는 `BuildContext`를 사용하여 `StatelessWidget`이 setState()를 마치 사용한 것 처럼 자체적으로 업데이트 할 수 있게 합니다.

~~~dart
void main(){
    runApp(MaterialApp(home: TestPage(),));
}

class TestPage extends StatelessWidget {
    // final because a Widget is immutable (remember?)
    final bag = {"first": true};

    @override
    Widget build(`BuildContext` context){
        return Scaffold(
            appBar: AppBar(title: Text('Stateless ??')),
            body: Container(
                child: Center(
                    child: GestureDetector(
                        child: Container(
                            width: 50.0,
                            height: 50.0,
                            color: bag["first"] ? Colors.red : Colors.blue,
                        ),
                        onTap:(){
                            bag["first"] = !bag["first"];
                            //
                            // This is the trick
                            //
                            (context as Element).markNeedsBuild();
                        }
                    ),
                ),
            ),
        );
    }
}
~~~

setState() 메소드를 호출 할 때  _element.markNeedsBuild()와 같은 일을합니다.

## 결론

저는 Flutter가 어떻게 구성되었는지 아는 것이 흥미로울 수 있다고 생각했습니다. 그리고 모든 것이 효율적이고 확장 가능하도록 설계되었음을 기억하기를 바랍니다.<br>
그리고 Widget, Element, `BuildContext`, RenderObject와 같은 핵심 개념들이 항상 이해하기 쉽지는 않습니다.

이 기사가 도움이 되었기를 바라며 나는 당신에게 행복한 코딩을 하기를 기원합니다.
