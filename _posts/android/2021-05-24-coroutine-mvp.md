---
published: true
title: "[Android] Coroutine을 활용한 안드로이드 디자인 패턴 - MVP"	
excerpt : " "	
layout: single	
classes: wide
author_profile: true	
read_time: false # read_time을 출력할지 여부 1min read 같은것!	
toc: true #Table Of Contents 목차 보여줌	
toc_label: "My Table of Contents" # toc 이름 정의	
toc_icon: "cog" #font Awesome아이콘으로 toc 아이콘 설정	
toc_sticky: true # 스크롤 내릴때 같이 내려가는 목차	
categories :	
 - Android	
tags: 	
  - Android
  - Glide
  - Coroutine
  - Kotlin
comments: true	
header:
  overlay_image: https://user-images.githubusercontent.com/35194820/119770376-18f76c80-bef7-11eb-8b3e-abca9300d1c1.gif
  overlay_filter: 0.4
  teaser: https://www.pngkey.com/png/full/27-278037_banner-freeuse-library-android-transparent-app-android-development.png
sitemap :	
  changefreq : daily	
  priority : 1.0	
---

## 서두

MVC 패턴의 문제점은 Controller 와 Android Framework, View와 강력한 종속성 때문에 테스트 코드를 작성하기가 어렵다는 것이 였습니다.

위의 언급된 문제를 해결한 것이 MVP 패턴입니다. 이 포스트는 기존 MVC 패턴을 어떻게 MVP 패턴으로 변경하는 지에 대해서 알아보도록 하겠습니다.

## 실행 화면

<div align="center">
<img src="https://user-images.githubusercontent.com/35194820/117981811-5e049600-b370-11eb-97d4-52fca92cf356.gif" >
</div></br>

앱에 대해 간단히 설명하자면 서버로 부터 HTML 문서를 다운받고 문서로 부터 이미지-제목을 파싱하여 RecyclerView에 뿌려주는 갤러리 앱입니다. 비동기 처리를 위해 저는 Coroutine을 사용했습니다.

실행화면은 MVC, MVP, MVVM 패턴 모두 동일합니다.

## 샘플 코드

아래 Repository를 참고바랍니다.

[Repository](https://github.com/Origogi/Android-Coroutine-Galley-App)

> git checkout mvp


## MVP

MVC 는 Model-View-Presenter의 약자로써 3개의 집합으로 구분합니다.

<div align="center">
<img src="https://user-images.githubusercontent.com/35194820/119347519-98f2bc00-bcd6-11eb-8a3f-23434a453e37.PNG" >
</div>

MVC와 큰 차이점은 Android Activity는 이제 View에 속하게 됩니다.

### Presenter

View와 Model를 서로 이어주는 역할을 합니다. View와 Presenter는 미리 정의한 Interface를 통해서 서로 통신을 합니다.

기존 Controller와 큰 차이점은 Android Framework 와 종속성이 모두 제거가 되었습니다.

#### MainContract

~~~kotlin
class MainContract {

    interface View {
        fun addItem(imageData: ImageData)
        fun updateCount(count : Int)
    }

    interface Presenter {
        fun launch()
        fun init()
        fun deInit()
    }
}
~~~

#### MainPresenter

~~~kotlin
class MainPresenter(private val view: MainContract.View) : MainContract.Presenter, CoroutineScope {

    lateinit var job: Job
    var count = 0

    override fun init() {
        job = Job()
        count = 0
    }

    override fun deInit() {
        job.cancel()
    }

    override fun launch() {
        count = 0
        launch {
            println("Thread : " + Thread.currentThread().name)
            val channel = ImageDataProvider().get(this)

            channel.consumeEach {
                count++
                withContext(Main) {
                    view.onAddedItem(it)
                    view.onUpdateCounter(count)
                }
            }
        }
    }

    override val coroutineContext: CoroutineContext
        get() = job + newSingleThreadContext("Presenter")
}
~~~

MyPresenter 는 MainContract.Presenter 를 상속받아 구현을 하고 View 는 MainContract.Presenter interface를 통해서 이벤트를 전달합니다.

- init() : 코루틴 스코프를 활성화하고 `counter` 를 초기화합니다.
- deInit() : 코루틴을 종료하고 코루틴 스코프를 정리 합니다. Acitivity가 더 이상 사용하지 않을 때 호출 할 것입니다.
- launch() : 네트워크 연산을 하고 Model를 업데이트하여 그 결과를 callback interface를 통해서 View에 전달합니다.

### View

화면을 구성하는 컴포넌트입니다.

위 샘플코드에서 View는 Layout Xml, RecyclerView Adapter인 ImageDataAdapter 그리고 MainActicity가 새롭게 추가가 됩니다.

#### MainActivity

~~~kotlin
class MainActivity : AppCompatActivity(), CoroutineScope, MainContract.View {
    ...
    private lateinit var presenter: MainContract.Presenter

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        job = Job()

        presenter = MainPresenter(this).apply {
            init()
            launch()
        }

        viewManager = LinearLayoutManager(this)
        viewAdapter = ImageDataAdapter(this)
        recyclerView = this.findViewById<RecyclerView>(R.id.image_title_list).apply {
            layoutManager = viewManager
            adapter = viewAdapter
        }
    }

    override fun onAddedItem(imageData: ImageData) {
        launch {
            viewAdapter.add(imageData)
        }
    }

    override fun onUpdateCounter(count: Int) {
        launch {
            findViewById<TextView>(R.id.counter).text = "Image Count : $count"
        }
    }
    ....
~~~

Activity는 MainContract.View interface를 상속하여 구현을 하고 해당 interface callback 를 Presenter에게 넘겨줍니다.

Presenter로 인해 callback이 호출이 되면 View 를 갱신을 합니다.

추가로 View와 Model은 완벽하게 분리가 되었습니다.

### Model

MVC와 달라진 점은 없습니다.

## MVP 패턴의 장점

- Presenter에서 Anroid Framework 의 종속성이 제거가 되었습니다. 따라서 테스트 코드 작성이 좀 더 편해졌습니다.

## MVC 패턴의 단점

- View와 Presenter 간 1대1로 강력한 종속성이 생겼으며 Presenter는 다른 View와 결합할수 없습니다. 따라서 새로운 View를 생성할 경우 Presenter 또한 생성을 해야합니다.
- 기능이 추가가 될 수록 Presenter 로직 또한 복잡해져서 유지 보수가 어려워 집니다.

## 마무리

MVP의 Presenter는 MVC 의 Controller와 대비 Android Framework와 종속성이 제거가 되어 테스트 코드 작성 및 Android Framework 변경 사항에 영향을 받지 않는 다는 장점이 있지만 View와 1대1로 종속성 때문에 Presenter는 다른 View와 결합할수 없습니다.

위 장점을 살리면서 단점을 극복한 것이 MVVM 입니다. 다음 포스트에서 기존 MVP에서 MVVM 으로 변경하는 과정을 다루도록 하겠습니다.

## 참고

- [[Android] Coroutine을 활용한 안드로이드 디자인 패턴 - MVC](https://origogi.github.io/android/coroutine-mvc/)
- [[Android] Coroutine을 활용한 안드로이드 디자인 패턴 - MVVM-LiveData](https://origogi.github.io/android/coroutine-mvvm-livedata/)
- [[Android] Coroutine을 활용한 안드로이드 디자인 패턴 - MVVM-DataBinding](https://origogi.github.io/android/coroutine-mvvm-data-binding/)
