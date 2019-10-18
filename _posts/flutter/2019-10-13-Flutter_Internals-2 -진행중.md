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
  teaser: assets/images/flutter-header.png
---
> Original Source : <https://www.didierboelens.com/2019/09/flutter-internals>
> <br>Author : boeledi

## Part 2: from Widgets to pixels

이제 내부 동작에 대한 기본을 소개 했으므로 이제 위젯에 대해 이야기 할 차례입니다.

모든 Flutter 문서에서 모든 것이 Widget으로 통합니다. .

글쎄, 그것은 거의 정확하지만 좀 더 정확하기 위해서는 오히려 다음과 같이 말하고 싶습니다.

> 개발자의 관점에서 레이아웃 및 상호 작용과 관련하여 사용자 인터페이스와 관련된 모든 것은 위젯을 통해 수행됩니다.

위젯을 사용하면 개발자가 화면의 일부를 치수, 내용, 레이아웃 및 상호 작용 측면에서 정의 할 수 있기 때문에 훨씬 더 많은 기능이 있습니다. 실제로 위젯이란 무엇입니까?

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

"@immutable"주석은 매우 중요하며 Widget 클래스의 모든 변수는 FINAL이어야합니다. 따라서 일단 인스턴스화되면 위젯은 더 이상 내부 변수를 조정할 수 없습니다.

Flutter로 개발할 때 아래와 같이 위젯을 사용하여 화면의 구조를 정의합니다…

~~~dart
Widget build(BuildContext context){
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

이 sample은 7 개의 위젯을 사용하며 계층 구조를 구성합니다. 위 코드를 다음 그림과 같이 단순화 시킬수 있습니다.


![](https://www.didierboelens.com/images/internals_widgets_tree.png)

보다시피, SafeArea가 트리의 루트 인 것 처럼 보입니다.

### The forest behind the tree

아시다시피 위젯 자체는 다른 위젯의 집합 일 수 있습니다. 예를 들어 이전 코드를 다음과 같이 작성할 수 있습니다.


~~~dart
Widget build(BuildContext context){
    return MyOwnWidget();
}
~~~

이것은 `MyOwnWidget`이라는 위젯 자체가 SafeArea, Scaffold를 렌더링한다고 가정하지만, 이 예에서 가장 중요한 것은

> Widget은 입사귀나 Tree의 줄기, 또는 Tree가 될 수있지만. Tree가 모인 Forest가 될 수가 없을 까? 

### Tree에서 Element의 개념

내가 왜 이것을 언급했는지 생각해 보았습니끼?

나중에 알게 되겠지만, 장치에 렌더링할 이미지를 구성하는 픽셀을 생성하기 위해서는 화면을 구성하는 모든 작은 부분을 자세히 알아야 하고, 모든 부분을 결정하려면 모든 위젯을 전개(inflate)할 것을 요청해야 한다.

이것을 설명하기 위해 러시아 인형의 원리를 생각해보면, 닫힌 인형 만 보이지만 다른 인형에는 다른 인형이 포함되어 있습니다.


![](https://www.didierboelens.com/images/internals_russian_dolls.png)

플러터가 일부에서 전체로 모든 위젯을 전개했을 때, 그것은 전체에서 일부인 다른 러시아 인형을 얻는 것과 유사할 것입니다.

다음 diagram은 이전 코드에 해당하는 최종 위젯 계층 구조의 일부를 보여준다. 노란색으로 코드에 언급된 위젯을 강조하여 부분 위젯 트리에서 찾을 수 있도록 하였습니다.

![](https://www.didierboelens.com/images/internals_inflated_widgets.png)

> **중요 설명**
>
> "위젯 트리"라는 말은 프로그래머들이 위젯을 사용하기 때문에 이해하기 쉽게 하기 위해서 사용되지만,실제 플러터에는 위젯 트리가 없습니다!<br><br>
>그 대신 "Tree의 Element"라고 말해야 합니다..


이제 Element의 개념에 대해 알아볼 시간입니다.

> 각 위젯마다 하나의 요소에 해당합니다. 요소는 서로 연결되어 트리를 형성합니다. 따라서 요소는 트리에서 무언가를 참조합니다.

요소를 부모와 잠재적으로 자식이있는 노드로 생각하십시오. 부모 관계를 통해 서로 연결되어 트리 구조를 얻습니다.


![](https://www.didierboelens.com/images/internals_element.png)

위 그림에서 볼 수 있듯이 요소는 하나의 위젯을 가리키고 RenderObject를 가리킬 수도 있습니다.

> Even better… the Element points to the Widget which created the element !

정리를 하자면
- 위젯 트리는 없지만 요소 트리가 있습니다.
- 위젯에 의해 요소가 생성됩니다.
- 요소는 그것을 만든 위젯을 참조
- 요소는 부모와 연결이 되어 있습니다. 그리고 요소에는 어린이가있을 수 있습니다
- 요소는 RenderObject를 참조 할 수도 있습니다.

> 요소는 비주얼의 일부가 서로 연결되는 방식을 정의합니다.


![](https://www.didierboelens.com/images/internals_3_trees.png)

보시다시피 요소 트리는 위젯과 RenderObject 사이를 연결해 줍니다..

하지만 위젯이 요소를 왜 생성할 까요?

### 3 main categories of Widgets


Flutter에서 위젯은 3 가지 주요 범주로 나뉩니다. 개인적으로이 범주를 다음과 같이 부릅니다.

- The proxies

  The main role of these Widgets is to hold some piece of information which needs to be made available to the Widgets, part of the tree structure, rooted by the proxies. A typical example of such Widgets is the InheritedWidget or LayoutId.

  These Widgets do not directly take part of the User Interface but are used by others to fetch the information they can provide.

- The renderers

   이 위젯들은 화면 Layout과 직접 관련이 있습니다.

  - the dimensions;
  - the position;
  - the layout, rendering.

  일반적인 예는 다음과 같습니다. 
    - 행, 열, 스택 및 패딩, 정렬, 불투명도, RawImage…

- the components.

  이들은 치수, 위치, 모양과 관련된 최종 정보를 직접 제공하지 않고 최종 정보를 얻는 데 사용될 데이터 (또는 힌트) 위젯입니다. 이 위젯은 일반적으로 컴포넌트라고합니다

  Example: RaisedButton, Scaffold, Text, GestureDetector, Container…
![](https://www.didierboelens.com/images/internals_widgets_categories.png)

The following PDF lists most of the Widgets, regrouped by categories.

Why is that split important? Because depending on the Widget category, a corresponding Element type is associated…

### The Element types
Here are the different element types:

![](https://www.didierboelens.com/images/internals_element_types.png)

As you can see in the picture above, Elements are split into 2 main types:

- ComponentElement

  These elements do not directly correspond to any visual rendering part.

- RenderObjectElement

  These elements correspond to a part of the rendered screen.

Great! Lots of information so far but how is everything linked together and why was it interesting to introduce all this?

### How do Widgets and Elements work together?

> In Flutter, the whole mechanics relies on invalidating either an element or a renderObject.

Invalidating an element can be done in different ways:

- by using setState, which invalidates the whole StatefulElement (notice that I am intentionally not saying StatefulWidget)
- via notifications, handled by other proxyElement (such as InheritedWidget, for example), which invalidates any element that depends on that proxyElement
The outcome of an invalidation is that the corresponding element(s) is/are referenced in a list of dirty elements.

Invalidating a renderObject means that no changes are applied to the structure of the elements but a modification at the level of a renderObject happens, such as

- changes to its dimensions, position, geometry…
- needs to be repainted, for example when you simply change the background color, the font style…

The outcome of such invalidation is that the corresponding renderObject is referenced in a list of renderObjects that need to be rebuilt or repainted.

Whatever the type of invalidation, when this happens, the SchedulerBinding (remember it?) is requested to ask the Flutter Engine to schedule a new frame.

It is when the Flutter Engine wakes up the SchedulerBinding that all the magics happens…

### onDrawFrame()

Earlier in this article, we mentioned that the SchedulerBinding had 2 main responsibilities, one of which was to be ready to handle requests emitted by the Flutter Engine, related to frame rebuild. This is the perfect moment to focus on this now…

The partial sequence diagram below shows what happens when the SchedulerBinding receives a request onDrawFrame() from the Flutter Engine.

![](https://www.didierboelens.com/images/internals_ondrawframe.png)

**Step 1: the elements**

The WidgetsBinding is invoked and the latter first considers the changes related to the elements.

As the BuildOwner is responsible for handling the elements tree, the WidgetsBinding invokes the buildScope method of the buildOwner.

This method iterates the list of invalidated elements (= dirty) and requests them to rebuild.

The main principles of this rebuild() method are:

1. request the element to rebuild() which leads to most of the time, invoking the build() method of the widget referenced by that element (= method Widget build(BuildContext context){…}). This build() method returns a new widget.
2. if the element has no child, the new widget is inflated (see just after), otherwise
3. the new widget is compared to the one referenced by the child of the element.
    - If they could be exchanged (=same widget type and key), the update is made, the child element is kept.
    - If they could not be exchanged, the child element is unmounted (~ discarded) and the new widget is inflated.
4. The inflating of the widget leads to creating a new element, which is mounted as new child of the element. (mounted = inserted into the elements tree)
The following animation tries to make this explanation a bit more visual.

![](https://www.didierboelens.com/images/internals_ondrawframe.gif)

**Note on Widget inflating**

When the widget is inflated, it is requested to create a new element of a certain type, defined by the widget category.

Therefore,

- an InheritedWidget will generate an InheritedElement
- a StatefulWidget will generate a StatefulElement
- a StatelessWidget will generate a StatelessElement
- an InheritedModel will generate an InheritedModelElement
- an InheritedNotifier will generate an InheritedNotifierElement
- a LeafRenderObjectWidget will generate a LeafRenderObjectElement
- a SingleChildRenderObjectWidget will generate a SingleChildRenderObjectElement
- a MultiChildRenderObjectWidget will generate a MultiChildRenderObjectElement
- a ParentDataWidget will generate a ParentDataElement
Each of these element types has a distinct behavior.

For example

- a StatefulElement will invoke the widget.createState() method at initialization, which will create the State and link it to the element
- a RenderObjectElement type will create a RenderObject when the element will be mounted, this renderObject will be added to the render tree and linked to the element.

**Step 2: the renderObjects**

Once all actions related to dirty elements have been completed, the elements tree is now stable and it is time to consider the rendering process.

As the RendererBinding is responsible for handling the rendering tree, the WidgetsBinding invokes the drawFrame method of the RendererBinding.

The partial diagram below shows the sequence of actions performed during a drawFrame() request.

![](https://www.didierboelens.com/images/internals_drawframe_rendering.png)

During this step, the following activities are performed:

- each renderObject marked as dirty is requested to perform its layout (meaning calculating its dimensions and geometry)
- each renderObject marked as needs paint is repainted, using the renderObject’s layer.
- the resulting scene is built and sent to the Flutter Engine so that the latter transmits it to the device screen.
- finally the Semantics is also updated and sent to the Flutter Engine

At the end of this flow of actions, the device screen is updated.

## Part 3: Gesture handling
The gestures (= events related to the finger on the glass) is handled by the GestureBinding.

When the Flutter Engine sends information related to a gesture-related event, through the window.onPointerDataPacket API, the GestureBinding intercepts it, proceeds with some buffering and:

1. converts the coordinates emitted by the Flutter Engine to match the device pixel ratio, then
2. requests the renderView to provide a list of ALL RenderObjects which cover a part of screen containing the coordinates of the event
3. then iterates that list of renderObjects and dispatches the related event to each of them.
4. when a renderObject is waiting for this kind of event, it processes it.

From this explanation, we directly see how important renderObjects are…

## Part 4: Animations
This last part of the article focuses on the notion of Animations and more specifically on the notion of Ticker.

When you initiate an animation, you generally use an AnimationController or any similar Widget or component.

In Flutter, everything which is related to animation refers to the notion of Ticker.

A Ticker does only one thing, when active: “it requests the SchedulerBinding to register a callback and ask the Flutter Engine to wake it up when next available”.

When the Flutter Engine is ready, it then invokes the SchedulerBinding via a request: “onBeginFrame”.

The SchedulerBinding intercepts this request then iterates the list of ticker callbacks and invokes each of them.

Each ticker tick is intercepted by any controller interested in this event to process it. If the animation is complete, the ticker is “disabled”, otherwise, the ticker requests the SchedulerBinding to schedule another callback. And so on…

## Global Picture

Now that we have seen how the Flutter internals work, here is the global picture:

![](https://www.didierboelens.com/images/internals_big_picture.png)

## BuildContext
Final word…

If you recall the diagram that shows the different element types, you most probably noticed the signature of the base Element:

~~~dart
abstract class Element extends DiagnosticableTree implements BuildContext {
    ...
}
~~~

Here is the famous BuildContext.

What is a BuildContext?

The BuildContext is an interface that defines a series of getters and methods that could be implemented by an element, in an harmonized manner.

In particular, the BuildContext is mainly used in the build() method of a StatelessWidget and StatefulWidget or in a StatefulWidget State object.

> The BuildContext is nothing else but the Element itself which corresponds to
> - the Widget being rebuilt (inside the build or builder methods)
> - the StatefulWidget linked to the State where you are referencing the context variable.

This means that most developers are constantly handling elements even without knowing it.

### How useful the BuildContext can be?

As the BuildContext corresponds to the element related to the widget but also to a location of the widget in the tree, this BuildContext is very useful to:

- obtain the reference of the RenderObject that corresponds to the widget (or if the widget is not a renderer, the descendant widget)
- obtain the size of the RenderObject
- visit the tree. This is actually used by all the Widgets which usually implement the of method (e.g. MediaQuery.of(context), Theme.of(context)…)

### Just for the fun…

Now that we have understood that the BuildContext is the element, for the fun I wanted to show you another way of using it…

The following useless code makes possible for a StatelessWidget to update itself (as if it was a StatefulWidget but without using any setState()), by using the BuildContext …

~~~dart
void main(){
    runApp(MaterialApp(home: TestPage(),));
}

class TestPage extends StatelessWidget {
    // final because a Widget is immutable (remember?)
    final bag = {"first": true};

    @override
    Widget build(BuildContext context){
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
                        onTap: (){
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

Between us, when you are invoking the setState() method, the latter ends up doing the very same thing: _element.markNeedsBuild().

## Conclusions
Yet another long article, will you tell me.

I thought it could be interesting to know how Flutter has been architectured and to remind that everything has been designed to be efficient, scalable and open to future extensions.

Also, key concepts such as Widget, Element, BuildContext, RenderObject are not always obvious to apprehend.

I hope that this article might have been useful.

Stay tuned for new articles, soon. Meanwhile, let me wish you a happy coding.


