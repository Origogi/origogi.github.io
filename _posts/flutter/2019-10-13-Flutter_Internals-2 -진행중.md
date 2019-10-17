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

Now that we have introduced the basics of the internal mechanics, it is time to talk about Widgets.

In all Flutter documentation, you will read that everything is Widgets.

Well, it is almost correct but in order to be a bit more precise, I would rather say:

> From a Developer perspective, everything related to the User Interface in terms of layout and interaction, is done via Widgets.

Why this precision? Because a Widget allows a developer to define a part of the screen in terms of dimensions, content, layout and interaction BUT there is so much more. So what is a Widget, actually?

### Immutable Configuration

When you read the Flutter source code, you will notice the following definition of the Widget class.

~~~dart
@immutable
abstract class Widget extends DiagnosticableTree {
  const Widget({ this.key });

  final Key key;

  ...
}
~~~

What does this mean?

The annotation “@immutable” is very important and tells us that any variable in a Widget class has to be FINAL, in other words: “is defined and assigned ONCE FOR ALL”. So, once instantiated, the Widget will no longer be able to adapt its inner variables.

> A Widget is a kind of constant configuration since it is IMMUTABLE

The Widgets hierarchical structure
When you develop with Flutter, you define the structure of your screen(s), using Widgets… Something like:

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

This sample uses 7 Widgets, which together form a hierarchical structure. The very simplified structure, based on the code, is the following:

![](https://www.didierboelens.com/images/internals_widgets_tree.png)

As you can see, this looks like a tree, where the SafeArea is the root of the tree.

### The forest behind the tree

As a you already know, a Widget may itself be an aggregation of other Widgets. As an example, I could have written the previous code the following way:

~~~dart
Widget build(BuildContext context){
    return MyOwnWidget();
}
~~~

This assumes that the widget `MyOwnWidget` would itself render the SafeArea, Scaffold… but the most important with this example is that

> a Widget maybe a leaf, a node in a tree, even a tree itself or why not a forest of trees…

### The notion of Element in the tree

Why did I mention this?

As we will see later how, in order to be able to generate the pixels that compose the image to be rendered on the device, Flutter needs to know in details all the little parts that compose the screen and, to determine all the parts, it will request to inflate all the Widgets.

In order to illustrate this, consider the russian dolls principle: closed you only see 1 doll but the latter contains another one which in turn contains another one and so on…

![](https://www.didierboelens.com/images/internals_russian_dolls.png)

When Flutter will have inflated all the widgets, part of the screen, it will be similar to obtaining all the different russian dolls, part of the whole.

The following diagram shows a part of the final Widget hierarchical structure that corresponds to the previous code. In yellow, I have highlighted the Widgets that were mentioned in the code so that you can spot them in the resulting partial widgets tree.

![](https://www.didierboelens.com/images/internals_inflated_widgets.png)

> Important clarification
>
> The wording “Widget tree” only exists for sake of making it easier to understand since programmers are using Widgets but, in Flutter there is NO Widget tree!<br><br>
>In fact, to be correct, we should rather say: “tree of Elements“

It is now time to introduce the notion of Element…

> To each widget corresponds one element. Elements are linked to each other and form a tree. Therefore an element is a reference of something in the tree.

At first, think of an element as a node which has a parent and potentially a child. Linked together via the parent relationship, we obtain a tree structure.

![](https://www.didierboelens.com/images/internals_element.png)

As you can see in the picture above, the Element points to one Widget and may also point to a RenderObject.

> Even better… the Element points to the Widget which created the element !

Let me recap…
- there is no Widgets tree but a tree of Elements
- Elements are created by the Widgets
- an Element references the Widget that created it
- Elements are linked together with the parent relationship
Elements could have a child or children
- Elements could also point to a RenderObject

> Elements define how parts of the visuals are linked to each other

In order to better visualize where the notion of element fits, let’s consider the following visual representation:

![](https://www.didierboelens.com/images/internals_3_trees.png)

As you can see the elements tree is the actual link between the Widgets and the RenderObjects.

But, why does the Widget create the Element?

### 3 main categories of Widgets

In Flutter, Widgets are split into 3 main categories, I personally call these categories:
(but this is only my way of categorizing them)

- the proxies


  The main role of these Widgets is to hold some piece of information which needs to be made available to the Widgets, part of the tree structure, rooted by the proxies. A typical example of such Widgets is the InheritedWidget or LayoutId.

  These Widgets do not directly take part of the User Interface but are used by others to fetch the information they can provide.

- the renderers

  These Widgets have a direct involvement with the layout of the screen as they define (or are used to infer) either:

  - the dimensions;
  - the position;
  - the layout, rendering.

  Typical examples are: Row, Column, Stack but also Padding, Align, Opacity, RawImage…

- the components.

  These are the other Widgets which are not directly providing the final information related to dimensions, positions, look but rather data (or hint) which will be used to obtain the final information. These Widgets are commonly named components.

  Examples are: RaisedButton, Scaffold, Text, GestureDetector, Container…
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


