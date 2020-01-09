---
published: true
title: "[Flutter][번역] Flutter: Push, Pop, Push"
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
  overlay_image: assets/images/flutter_dart.png
  overlay_filter: 0.5
  teaser: https://venturebeat.com/wp-content/uploads/2019/09/google-dart-flutter.png?w=578&strip=all
sitemap :
  changefreq : daily
  priority : 1.0
---
> Original Source : <https://medium.com/flutter-community/flutter-push-pop-push-1bb718b13c31>

# Flutter: Push, Pop, Push

![](https://miro.medium.com/max/1024/1*7banZjCh-rGamKT-9jqADA.jpeg)

Framework가 제공하는 모든 위젯을 통해 Flutter에서 UI를 구축하는 것은 매우 간단합니다. 마지막 기사에서 다루었습니다.
그러나 우리는 아무 기능도 하지 않는다면 아름다운 응용 프로그램을 가질 수 없습니다. 애플리케이션 화면을 이동하거나 화면간에 데이터를 주고 받아야합니다. Flutter에서는 [Navigator](https://docs.flutter.io/flutter/widgets/Navigator-class.html)를 이용하여 한 화면에서 다른 화면으로 이동할 수 있습니다.

그리고 Navigator를 `Route`를 쌓아두는 간단한 Widget입니다.

새 화면으로 push하거나 현재 화면에서 팝업하는 방법을 알려주는 포스트는 이미 많이 있지만 이 포스트는 그 이상으로 대부분의 네비게이터 방법에 중점을두고 각 방법의 사용 사례를 설명합니다.

## Before we begin…

앞에서 `Route`에 대해서 언급하였는데 그것은 정확하게 무엇일까요?

`Route`는 app의 화면이나 페이지에 대한 추상화입니다.
예를 들어 '/home'은 HomeScreen으로, '/login'은 LoginScreen으로 안내한다. '/'가 가장 첫 번째 route일 것이다. 이것은 REST API 의 Routing과 매우 유사합니다. 그래서 '/'는 Root처럼 동작합니다.

아래 예제는 Flutter 응용 프로그램에서 route를 선언하는 방법입니다.

~~~dart
new MaterialApp(
  home: new Screen1(),
  routes: <String, WidgetBuilder> {
    '/screen1': (BuildContext context) => new Screen1(),
    '/screen2' : (BuildContext context) => new Screen2(),
    '/screen3' : (BuildContext context) => new Screen3(),
    '/screen4' : (BuildContext context) => new Screen4()
  },
)
~~~

Screen1 (), Screen2 () 등은 각 화면의 클래스 이름입니다.

## Push, push, push.

데이터 구조에 대한 지식이 있으면 Stack에 대해 이미 알고 있을 것 입니다. 그리고 Stack에 대한 기본 지식이 있다면 Push 및 Pop에 대해 알고 있을 것입니다.

만약 위 사전 지식을 모를 경우 간략히 설명 드리면

- Push는 Stack 맨 위에 요소를 추가
- Pop은 Stack 맨 위에 요소를 제거

Flutter의 경우 다른 화면으로 이동할 때 push method를 사용히여 Navigator 위젯의 Stack 상단에 새 화면을 추가합니다. 당연히 pop method는 해당 스크린을 Stack에서 제거합니다.

이제 [샘플 프로젝트의 코드](https://github.com/PoojaB26/NavigatorsDemo-Flutter)로 이동하여 화면 1에서 화면 2로 이동할 수 있는 방법을 살펴보십시오. 샘플 app을 실행하면 그 방법을 실험할 수 있습니다.

~~~dart
new RaisedButton(
   onPressed:(){
   Navigator.of(context).pushNamed('/screen2');
},
   child: new Text("Push to Screen 2"),
),
~~~


`pushNamed` method를 사용하여 main.dart에 route가 정의 된 화면으로 이동할 수 있습니다. 우리는 이 방법을 `namedRoute`라고 부릅니다. 이 방법의 사용 사례는 매우 간단합니다.

![image](https://miro.medium.com/max/94/1*RKtC1MKJbjSfMjUlR-2K7g.png)

## Pop it

이제 마지막 방문한 화면 (이 경우 Screen2)을 제거하려면 `pop` method를 사용하여 네비게이터의 Stack에서 Route를 Pop해야합니다.

~~~dart
Navigator.of(context).pop();
~~~

위 코드는 `onPressed` method 안에 들어갑니다.

![image](https://miro.medium.com/max/93/1*hq7qfAer0wCCSyIBKr7sfg.png)

Scaffold를 사용할 때는 일반적으로 명시적으로 Route를 pop할 필요가 없습니다. 왜냐하면 Scaffold는 자동으로 ApApar에 `back` 버튼을 추가하기 때문입니다. `back` 버튼을 누르면 Navigator.pop ()을 호출합니다. 
Android에서도 기기의 back 버튼을 누르면 같은 방식으로 작동합니다.
그럼에도 불구하고 사용자가 취소 버튼을 클릭 할 때 AlertDialog를 팝업하는 것과 같은 유스 케이스에는 이 방법이 필요할 수 있습니다.

### Why pop instead of pushing back to the previous screen?

원하는 위치에 호텔을 나열하는 호텔 예약 app이 있다고 가정합니다. 
목록에서 하나의 항목을 클릭하면 호텔에 대한 자세한 정보가 있는 화면으로 이동합니다. 선택한 호텔이 마음에 들지 않다 다시 목록으로 돌아가고 싶습니다.
Stack에 HotelListScreen 을 push하면 DetailsScreen은 Stack에 계속 유지가 됩니다. 따라서 back 버튼을 누르면 다시 DetailsScreen 로 돌아가게 됩니다. 이것은 매우 혼란스럽습니다.

샘플 app을 실행하고 Screen1의 appBar를 확인하십시오. 이것은 초기 Route 이기 때문에 back 버튼이 없습니다. 
이제 back 버튼 대신 `Push to Screen 2` 버튼을 누룹니다. 그 다음 `Push to Screen1 instead of Pop` 버튼을 누른 후 이제 Screen1의 appBar를 확인 하십시오. 새로 표시된 back 버튼을 누르면 Screen2로 돌아갈 수 있습니다.

![](https://miro.medium.com/max/92/1*Xsyo5c8s1JwO6f2OQ1nNEg.png)


### maybePop

만약 초기 Route 인 상태에서 실수로 Screen을 pop을 하게 되면 어떻게 될까요? Stack에 있는 유일한 screen을 pop하게 되면 app은 종료가 됩니다. 그 이유는 표시할 Route가 없기 때문입니다. 사용자가 이러한 경험을 원하지 않을수도 있습니다.

이를 방지하기 위해 maybePop()이 등장하게 됩니다. 
Screen1 에서 `maybePop` 버튼을 클릭하게 되면 아무 것도 하지 않습니다. 왜냐하면 pop를 할 것이 없기 때문입니다. 하지만 Screen3에서 같은 버튼을 누르게 되면 pop이 될 것입니다. 왜냐하면 그것을 할 수 있는 상태이기 때문입니다.

### canPop

위 동작을 수행 할 수 있다는 것은 놀라운 일이지만 이것이 초기 Route 인지 어떻게 알 수 있습니까? 그러한 경우 사용자에게 경고를 표시 할 수 있다면 좋을 것입니다.

좋은 질문입니다.  `canPop ()` method를 호출하면 Stack에 Route를 pop 할수 있으면 true를 반환하고 가능하지 않으면 false를 반환합니다.

두가지 방법 모두 Sreen 1과 Screen 3의 `canPop` 및 `maybePop`을 사용해 보고 차이점을 확인하십시오. canPop의 return 값이 IDE의 콘솔 탭에 표시됩니다.

## Push a little harder

이제 더 많은 Push 방법에 대해 좀 더 깊이 알아보겠습니다. 이제 route를 새 route로 교체하는 것에 대해 이야기하겠습니다.

이를 수행하기 위해 두가지 방법이 있습니다.

- `pushReplacementNamed`
- `popAndPushNamed`

~~~dart
Navigator.of(context).pushReplacementNamed('/screen4');
                       //and
Navigator.popAndPushNamed(context, '/screen4');
~~~

![](https://miro.medium.com/max/242/1*cr77kgOgz7KRjwvMAVXoAg.png)

샘플 app의 Screen3에서 두 가지를 모두 실험 해보십시오. 그리고 각각의 경우 enter, exit 애니메이션을 확인하십시오

pushReplacementNamed는 enter 애니메이션을 실행하고 popAndPushNamed는 exit 애니메이션을 실행합니다.
다음과 같은 Use case에서 사용할 수 있습니다.


### Use-case : pushReplacementNamed

사용자가 성공적으로 로그인하여 현재 DashboardScreen에있는 경우 어떤 경우에도 사용자가 LoginScreen으로 돌아 가기를 원하지 않습니다. 따라서  Login Route는 Dashboard Rroute 로 완전히 대체되어야합니다.

또 다른 예는 Splash Screen에서 Home Screen으로 이동하는 것입니다. 한 번만 표시되고 사용자는 HomeScreen에서 다시 돌아갈 수 없어야합니다. 이러한 경우 완전히 새로운 화면으로 이동하기 때문에 이 method 를 enter animation property에 사용할 수 있습니다.

### Use-case: popAndPushNamed

ProductsListScreen에 제품 목록을 표시하는 쇼핑 app을 작성하고 있고 사용자가 FiltersScreen에 필터를 적용 할 수 있다고 가정합니다. 사용자가 Apply Changes (변경 사항 적용) 버튼을 클릭하면 FiltersScreen (필터 화면)이 팝업되고 새 필터 값이있는 ProductsListScreen (제품 목록 화면)으로 되돌아갑니다. 여기서 popAndPushNamed의 exit animation property가 더 적합 할 것입니다.

## Until the end…

이제 이 포스팅의 마무리 단계입니다.

이 섹션에서는 다음 세 가지 method `pushNamedAndRemoveUntil` 및 `popUntil`을 다룰 것입니다.

### Use-case: pushNamedAndRemoveUntil


기본적으로 Facebook / Instagram과 같은 응용 프로그램에서 사용자가 로그인하고 피드를 스크롤하고 다른 프로필을 통해 추적하고 완료한 후 app에서 로그 아웃하려고 합니다. 로그 아웃 한 후에는 단순히 홈 스크린 (또는 로그 아웃 한 후 표시 해야하는 화면)을 누르기 만하면 됩니다.


사용자가 로그 아웃 한 후 이전 route로 돌아갈 수 없도록 Stack의 모든 Route를 제거하려고합니다.

~~~dart
Navigator.of(context).pushNamedAndRemoveUntil('/screen4', (Route<dynamic> route) => false);
~~~

여기서 `(Route <dynamic> route) => false`는 push 된 route 이전의 모든 route가 제거되도록 합니다.

![https://miro.medium.com/max/241/1*xiajfLip3Fu7tR3for_COQ.png]


이제 push 된 route 이전의 모든 route를 제거하는 대신 특정 수의 route 만 제거 할 수 있습니다.

다른 쇼핑 app을 예로 들어 보겠습니다.

쇼핑 app에서 사용자가 결제 거래를 완료하면 모든 거래 또는 장바구나 관련 화면이 Stack에서 제거되고 사용자는 PaymentConfirmationScreen으로 이동해야합니다.

![](https://miro.medium.com/max/251/1*aaZxoLUbKdFPgiIkBAmw7w.png)

여기서 back button을 클릭하면 ProductsListScreen 또는 HomeScreen으로 만 돌아갑니다.

~~~dart
Navigator.of(context).pushNamedAndRemoveUntil('/screen4', ModalRoute.withName('/screen1'));
~~~


위 코드에 따르면 Screen4를 push하고 Screen1까지 모든 route를 제거하므로 Stack은 다음과 같습니다.

![](https://miro.medium.com/max/213/1*D81iZF-BikxXJHak7_NkhA.png)

### Use-case: popUntil

Google 양식을 작성하고 구성 할 수있는 응용 프로그램을 사용하고 있다고 가정해 보십시오.
일부 사용자는 긴 3 단계 양식을 작성해야 할 수 있으며, 이는 모바일 애플리케이션에서 3개의 순차적 화면으로 표시 될 수 있습니다.

이제 양식의 세 번째 부분에서 사용자는 양식 작성을 취소하기로 결정합니다. 사용자가 취소를 클릭하면 이전의 모든 양식 관련 화면이 팝업되고 사용자는 HomeScreen 또는 DashboardScreen으로 돌아가서 모든 양식 관련 데이터를 잃게됩니다. 여기서는 새로운 route를 추진하지 않고 이전 route로 돌아갑니다.

![](https://miro.medium.com/max/249/1*qV7mF0Kow2zch-fjksmA_Q.png)

~~~dart
Navigator.popUntil(context, ModalRoute.withName('/screen2'));
~~~

## Where is the data?

위의 대부분의 예에서 데이터를 보내지 않고 새로운 route를 push하고 있지만 실제 시나리오에서는 그러한 시나리오가 거의 없을 것입니다. 데이터를 보내려면 Navigator를 사용하여 데이터가있는 Stack으로 새 MaterialPageRoute를 push합니다 (여기서는 userName).

~~~dart
String userName = "John Doe";
Navigator.push(
    context,
    new MaterialPageRoute(
        builder: (BuildContext context) =>
        new Screen5(userName)));
~~~

Screen5에서 값을 검색하려면 다음과 같이 Screen5에서 생성자를 추가하십시오.

~~~dart
class Screen5 extends StatelessWidget {

  final String userName;
  Screen5(this.userName);
  @override
  Widget build(BuildContext context) {
  print(userName)
  ...
  }
}
~~~

즉, MaterialPageRoute를 Push Method뿐만 아니라 PushReplacement, PushAndPopUntil 등에 사용할 수 있습니다. 기본적으로 우리가 설명한 위의 방법에서 첫 번째 파라미터는 이제 명명된 Route의 문자열 대신 MaterialPageRoute를 사용합니다.

### Give me some data back, man

새 화면에서 데이터를 반환 할 수도 있습니다. 알람 app을 만들고 있고 알람의 새 톤을 설정하려면 오디오 톤 옵션이 포함된 Dialog를 표시합니다. Dialog 가 열리면 분명히 선택한 항목 데이터가 필요할 것입니다.. 다음과 같이 위 목표를 달성할 수 있습니다.

~~~dart
new RaisedButton(onPressed: ()async{
  String value = await Navigator.push(context, new MaterialPageRoute<String>(
      builder: (BuildContext context) {
        return new Center(
          child: new GestureDetector(
              child: new Text('OK'),
              onTap: () { Navigator.pop(context, "Audio1"); }
          ),
        );
      }
  )
  );
  print(value);

},
  child: new Text("Return"),)
~~~

Screen4 에서 사용해 보고 콘솔에서 print 값을 확인하십시오.

참고: route를 사용하여 값을 반환하는 경우, route의 매개변수 타입은 pop의 return type과 일치해야 합니다. 

## Wow, that was a lot of information

실제로 method와 해당 구현을 간단히 설명 할 수 있었지만 Navigator method가 너무 많기 때문에 실제 응용 프로그램에서 가져온 시나리오를 사용하여 설명하고 싶었습니다. 나는 이것이 여러분 중 몇몇이 Navigator의 지식을 넓히는 데 도움이 되었기를 바랍니다.
