---
published: true
title: "[Flutter] Flutter app architecture 101: Vanilla, Scoped Model, BLoC -번역
excerpt : " "
layout: single
# classes: wide
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


# Flutter app architecture 101: Vanilla, Scoped Model, BLoC - 번역


> 원문 : <https://medium.com/flutter-community/flutter-app-architecture-101-vanilla-scoped-model-bloc-7eff7b2baf7e>


![](https://miro.medium.com/max/2000/1*FX3yx5KS0fAJNDtGaSEdhw.png)

Flutter는 Modern react-style framework , 풍부한 Wiget Collection 및 tooling을 제공하지만 Android와 비슷한 앱 아키텍처를 제공하지 않습니다.

실제로 모든 요구 사항을 충족시키는 궁극적인 아키텍처는 없습니다. 그리고 우리가 작업 중인 대부분의 모바일 앱에는 최소한 다음과 같은 기능이 있다는 사실을 직시해 봅시다.

1. 네트워크에 Data를 요청 / 업로드합니다.
2. Data를 매핑, 변환, 준비하고 사용자에게 제공합니다.
3. Database에 Data를 넣거나 가져옵니다.

이를 고려하여 저는 동일한 문제를 세 가지 서로 다른 아키텍처로 해결하는 Sample app을 만들어 봤습니다.

사용자에게 화면 중앙에 `Load user data` 버튼이 표시된다. 사용자가 버튼을 클릭하면 비동기 Data Loading이 Tree거되고 버튼이 `Loading progress` 로 교체됩니다. Data가 로드된 후 `Loading progress`가 Data로 교체됩니다.



<center><img src="https://miro.medium.com/max/300/1*yErdVupP--4NKV6b7YFffw.gif"></center>

## Data

간단한 예제로 비동기 네트워크 호출을 가정하기 위해 `Future <User>` 객체를 반환하는 `getUser()` 메소드가 포함 된 `Repository ` 클래스를 만들었습니다.

Dart에서 `Future` 와 비동기 프로그래밍에 익숙하지 않은 경우 이 [튜토리얼](https://www.dartlang.org/tutorials/language/futures)을 따라하거나, [문서](https://api.dartlang.org/stable/2.1.0/dart-async/Future-class.html)를 읽으면 자세한 내용을 알 수 있습니다.

~~~dart
class Repository {
  Future<User> getUser() async {
    await Future.delayed(Duration(seconds: 2));
    return User(name: 'John', surname: 'Smith');
  }
}
~~~

~~~dart
class User {
  User({
    @required this.name,
    @required this.surname,
  });

  final String name;
  final String surname;
}
~~~

## Vanilla

공식 Flutter 설명서를 읽은 후 대부분의 개발자가하는 방식으로 앱을 build 해 봅시다.

아래 예제는 `Navigator`를 사용하여 `VanillaScreen` 화면으로 이동합니다.

Widget의 수명 동안 Widget의 State가 여러 번 변경될 수 있으므로 StatefulWidget을 상속해야 합니다. State 저장 Widget을 구현하려면 State class 있어야 합니다. `_VanillaScreenState` class의 `bool _isLoading`, `User _user`는 Widget의 State를 나타냅니다. `build(BuildContext context)` 메소드를 호출하기 전에 두 Field를 초기화합니다.

~~~dart
class VanillaScreen extends StatefulWidget {
  VanillaScreen(this._repository);
  final Repository _repository;

  @override
  State<StatefulWidget> createState() => _VanillaScreenState();
}

class _VanillaScreenState extends State<VanillaScreen> {
  bool _isLoading = false;
  User _user;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Vanilla'),
      ),
      body: SafeArea(
        child: _isLoading ? _buildLoading() : _buildBody(),
      ),
    );
  }

  Widget _buildBody() {
    if (_user != null) {
      return _buildContent();
    } else {
      return _buildInit();
    }
  }

  Widget _buildInit() {
    return Center(
      child: RaisedButton(
        child: const Text('Load user data'),
        onPressed: () {
          setState(() {
            _isLoading = true;
          });
          widget._repository.getUser().then((user) {
            setState(() {
              _user = user;
              _isLoading = false;
            });
          });
        },
      ),
    );
  }

  Widget _buildContent() {
    return Center(
      child: Text('Hello ${_user.name} ${_user.surname}'),
    );
  }

  Widget _buildLoading() {
    return const Center(
      child: CircularProgressIndicator(),
    );
  }
}
~~~

Widget State 객체가 생성되면 `build (BuildContext context)` 메소드가 호출되어 UI를 build합니다. 현재 State를 나타 내기 위해 build되는 Widget에 대한 모든 결정은 UI declaration 코드를 통해 이루어집니다.

~~~dart
body: SafeArea(
  child: _isLoading ? _buildLoading() : _buildBody(),
)
~~~

사용자가 `Load user details` 버튼을 클릭할 때 `Loading Progress`를 표시하기 위해 다음 작업을 수행합니다.

~~~dart
setState(() {
  _isLoading = true;
});
~~~

> `setState()`를 호출하면 이 객체의 내부 State가 변경되었음을 `Framework`에 알립니다. 그러면 Framework가 이 State 객체의 build를 예약합니다.

즉, `setState()` 메소드를 호출한 후 `build(BuildContext context)` 메소드를 Framework에 의해 다시 호출하고 전체 Widget Tree를 재구성합니다. 이제 _isLoading이 true 메소드로 설정됨에 따라 `_buildBody()` 대신 `_buildLoading()`을 호출하고 화면에 `Loading Progress` 가 표시됩니다.

~~~dart
widget._repository.getUser().then((user) {
  setState(() {
    _user = user;
    _isLoading = false;
  });
});
~~~

### 장점

1. 쉽게 배우고 이해하기 쉽습니다.
2. 3rd party lib를 사용하지 않습니다.

### 단점

1. State가 변경이 될 때마다 모든 Widget tree가 Rebuild가 됩니다.
2. 단일 책임 원칙(SRP)을 어깁니다. Widget은 UI build 뿐만 아니라 Data load, business logic, State 관리를 담당합니다.
3. 현재 State를 어떻게 표현해야 하는지에 대한 결정은 UI declaration 코드로 이루어지며, 만약 State 관리 코드가 좀 더 복잡해지면 가독성이 떨어질 것입니다.

## Scoped Model

`Scoped Model`은 Flutter Framework에 포함되지 않은 3rd party 패키지입니다. Scoped Model의 개발자는 아래와 같이 설명합니다.

> 상위 Widget에서 하위 항목으로 Data model를 쉽게 전달할 수있는 유틸리티 세트입니다. 또한 Data model이 업데이트 될 때 Data model을 사용하는 모든 child 을 Rebuild 합니다. 이 Library 는 Fuchsia 코드에서 파생 되었습니다.


Scoped Model을 사용해서 같은 화면을 만들어 봅시다. 먼저, `pubspec.yaml`에 Scoped model 종속성을 추가하여 Scoped model패키지를 설치해야 합니다.

~~~dart
scoped_model: ^1.0.1
~~~

UserModelScreen Widget을 살펴보고 Scoped model를 사용하지 않은 이전 예와 비교해 봅시다. 모든 Widget의 후손이 모델을 사용할 수 있도록 하려면 ScopedModel로 랩핑하고 Widget과 모델을 제공해야합니다.

~~~dart
class UserModelScreen extends StatefulWidget {
  UserModelScreen(this._repository);
  final Repository _repository;

  @override
  State<StatefulWidget> createState() => _UserModelScreenState();
}

class _UserModelScreenState extends State<UserModelScreen> {
  UserModel _userModel;

  @override
  void initState() {
    _userModel = UserModel(widget._repository);
    super.initState();
  }

  @override
  Widget build(BuildContext context) {
    return ScopedModel(
      model: _userModel,
      child: Scaffold(
        appBar: AppBar(
          title: const Text('Scoped model'),
        ),
        body: SafeArea(
          child: ScopedModelDescendant<UserModel>(
            builder: (context, child, model) {
              if (model.isLoading) {
                return _buildLoading();
              } else {
                if (model.user != null) {
                  return _buildContent(model);
                } else {
                  return _buildInit(model);
                }
              }
            },
          ),
        ),
      ),
    );
  }

  Widget _buildInit(UserModel userModel) {
    return Center(
      child: RaisedButton(
        child: const Text('Load user data'),
        onPressed: () {
          userModel.loadUserData();
        },
      ),
    );
  }

  Widget _buildContent(UserModel userModel) {
    return Center(
      child: Text('Hello ${userModel.user.name} ${userModel.user.surname}'),
    );
  }

  Widget _buildLoading() {
    return const Center(
      child: CircularProgressIndicator(),
    );
  }
}
~~~

이전 예제에서 Widget State가 변경되면 전체 Widget Tree가 다시 작성되었습니다. 그러나 실제로 전체 화면을 Rebuild가 필요가 없을 수도 있습니다. 예를 들어 AppBar는 전혀 바뀌지 않았음으로 Rebuild 할 필요가 없습니다. 이상적 으로는 업데이트 된 Widget 만 Rebuild 되어야 합니다. `Scoped model`은 이를 해결하는 데 도움이 될 수 있습니다.

`ScopedModelDescendant<UserModel>` Widget은 Widget Tree에서 `UserModel`을 찾는 데 사용됩니다. `UserModel`이 변경 사항을 알릴 때마다 자동으로 Rebuild 됩니다.

또 다른 개선 사항은 `UserModelScreen` 가 더 이상 State 관리 및 business logic 을 담당하지 않는다는 것입니다.

아래 `UserModel` 코드를 살펴 봅시다.

~~~dart
class UserModel extends Model {
  UserModel(this._repository);
  final Repository _repository;

  bool _isLoading = false;
  User _user;

  User get user => _user;
  bool get isLoading => _isLoading;

  void loadUserData() {
    _isLoading = true;
    notifyListeners();
    _repository.getUser().then((user) {
      _user = user;
      _isLoading = false;
      notifyListeners();
    });
  }

  static UserModel of(BuildContext context) =>
      ScopedModel.of<UserModel>(context);
}
~~~

이제 `UserModel`이 State를 유지하고 관리합니다. 변경 사항이 발생했음을 `Listener` 에게 알릴려면 (그리고 descendants을 Rebuild 하려면) `notifyListeners()` 메소드를 호출해야합니다.

### 장점

1. Business logic, State 관리 및 UI 코드 분리가 됩니다.
2. 배우기가 쉽습니다.

### 단점

1. 3rd party library
2. 모델이 점점 복잡 해짐에 따라 notifyListeners ()를 언제 호출해야 할지 결정하기가 어렵습니다.

## BLoC

`BLoC (Business Logic Components)` 는 Google 개발자가 권장하는 패턴입니다. State 변경을 관리하고 전파하기 위해 `Stream` 을 활용합니다.

당신이 만약 Android 개발자라면 `Bloc` 객체를 `ViewModel` 로, `StreamController` 를 `LiveData` 로 생각할 수 있습니다. 이렇게 이해를 한다면 다음 코드를 매우 간단하게 만들 수 있습니다.

~~~dart
class UserBloc {
  UserBloc(this._repository);

  final Repository _repository;

  final _userStreamController = StreamController<UserState>();

  Stream<UserState> get user => _userStreamController.stream;

  void loadUserData() {
    _userStreamController.sink.add(UserState._userLoading());
    _repository.getUser().then((user) {
      _userStreamController.sink.add(UserState._userData(user));
    });
  }

  void dispose() {
    _userStreamController.close();
  }
}

class UserState {
  UserState();
  factory UserState._userData(User user) = UserDataState;
  factory UserState._userLoading() = UserLoadingState;
}

class UserInitState extends UserState {}

class UserLoadingState extends UserState {}

class UserDataState extends UserState {
  UserDataState(this.user);
  final User user;
}
~~~

State 가 변경 될 때 Subscriber 에게 알리기 위해 추가 메소드 호출이 필요하지 않습니다.

화면의 가능한 State를 나타 내기 위해 3 개의 클래스를 만들었습니다.

1. UserInitState : 사용자가 중앙에 버튼이 있는 화면을 열 때의 State
2. UserLoadingState : Data가 로드되는 동안 `Loading progress` 가 표시 될 때의 State
3. UserDataState : Data가 로드되어 화면에 표시 될 때의 State

이런 방식으로 State 변경을 전파하면 UI 선언 코드의 모든 logic 를 제거 할 수 있습니다. `Scoped Model`의 예에서는 UI 선언 코드에서 `_isLoading`이 true 인지 확인하여 렌더링 할 Widget을 결정 했습니다. `BLoC`의 경우 화면 State 를 전파하고 있으며 `UserBlocScreen` Widget의 유일한 책임은 현재 State에 대한 UI를 렌더링하는 것입니다.

~~~dart
class UserBlocScreen extends StatefulWidget {
  UserBlocScreen(this._repository);
  final Repository _repository;

  @override
  State<StatefulWidget> createState() => _UserBlocScreenState();
}

class _UserBlocScreenState extends State<UserBlocScreen> {
  UserBloc _userBloc;

  @override
  void initState() {
    _userBloc = UserBloc(widget._repository);
    super.initState();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Bloc'),
      ),
      body: SafeArea(
        child: `StreamBuilder`<UserState>(
          stream: _userBloc.user,
          initialData: UserInitState(),
          builder: (context, snapshot) {
            if (snapshot.data is UserInitState) {
              return _buildInit();
            }
            if (snapshot.data is UserDataState) {
              UserDataState State = snapshot.data;
              return _buildContent(State.user);
            }
            if (snapshot.data is UserLoadingState) {
              return _buildLoading();
            }
          },
        ),
      ),
    );
  }

  Widget _buildInit() {
    return Center(
      child: RaisedButton(
        child: const Text('Load user data'),
        onPressed: () {
          _userBloc.loadUserData();
        },
      ),
    );
  }

  Widget _buildContent(User user) {
    return Center(
      child: Text('Hello ${user.name} ${user.surname}'),
    );
  }

  Widget _buildLoading() {
    return const Center(
      child: CircularProgressIndicator(),
    );
  }

  @override
  void dispose() {
    _userBloc.dispose();
    super.dispose();
  }
}
~~~

`UserBlocScreen` 코드는 이전 예제에 비해 훨씬 단순해졌습니다. State 변화를 듣기 위해 `StreamBuilder`를 사용하고 있습니다. `StreamBuilder`는 `Stream`과의 최신 `Snapshot` 을 기반으로 자체 build 되는 StatefulWidget입니다.

### 장점

1. 3rd party library가 필요 없습니다.
2. Business logic, State 관리 그리고 UI logic이 분리가 되어 있습니다.
3. `Scoped Model` 의 `notifyListeners()` 의 경우처럼 추가 호출이 필요하지 않습니다.

### 단점

1. `Stream` 또는 `RxDart`의 개념을 알고 있어야 합니다.

## Source Code

위의 예제의 소스 코드를 이 [Gitub repo](https://github.com/savjolovs/flutter_arch_examples)에서 확인할 수 있습니다.
