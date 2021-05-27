---
published: true
title: "[Android] Coroutine을 활용한 안드로이드 디자인 패턴 - MVVM"	
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

MVP 패턴을 사용할 때 가장 큰 문제점은 View와 Presenter 간의 강한 종속성으로 인해 1대1 관계가 생기고 Presenter는 다른 View와 결합을 할수 없었습니다. 이에 대한 단점을 극복한 것이 MVVM 패턴입니다.

## 실행 화면

<div align="center">
<img src="https://user-images.githubusercontent.com/35194820/117981811-5e049600-b370-11eb-97d4-52fca92cf356.gif" >
</div>

앱에 대해 간단히 설명하자면 서버로 부터 HTML 문서를 다운받고 문서로 부터 이미지-제목을 파싱하여 RecyclerView에 뿌려주는 갤러리 앱입니다. 비동기 처리를 위해 저는 Coroutine을 사용했습니다.

실행화면은 MVC, MVP, MVVM 패턴 모두 동일합니다.

## 샘플 코드

아래 Repository를 참고바랍니다.

[Repository](https://github.com/Origogi/Android-Coroutine-Galley-App)

> git checkout mvvm-livedata

## MVVM

MVC 는 Model-View-ViewModel 약자로써 3개의 집합으로 구분합니다.

<div align="center">
<img src="https://user-images.githubusercontent.com/35194820/119606170-06186580-be2d-11eb-8734-265d6d526e93.png" >
</div>

### View Model

MVP 에서 Presenter의 역할을 이제 View Model 이 담당하게 됩니다.

View 와 Presenter간 interface 는 이제 제거가 되었으며 View는 View Model의 관찰가능한 객체 즉 `LiveData` 를 관찰하고 변경이 되면 그 즉시 View를 업데이트를 하게 됩니다.

#### MyViewModel

~~~kotlin

class MyViewModel : ViewModel() {

    private val imageDataList: MutableLiveData<List<ImageData>> =
        MutableLiveData<List<ImageData>>().apply {
            viewModelScope.launch(Main) {
                value = mutableListOf()
            }
        }

    private val counter: MutableLiveData<Int> = MutableLiveData<Int>().apply {
        viewModelScope.launch(Main) {
            value = 0
        }
    }

    init {
        load()
    }

    private fun load() {

        viewModelScope.launch(Dispatchers.IO) {
            val channel = ImageDataProvider().get(viewModelScope)

            withContext(Main) {
                counter.value = 0
            }

            channel.consumeEach {
                withContext(Main) {
                    counter.value = counter.value!! + 1
                    imageDataList.value = imageDataList.value!! + it
                }
            }
        }
    }

    fun getImageDataList() = imageDataList as LiveData<List<ImageData>>
    fun getCounter() = counter as LiveData<Int>
}
~~~

View 가 UI 를 구성할 때 필요한 데이터 또는 상태는 두 가지 입니다.

- Image Data의 갯수
- Image Data List

View Model에서 LiveData 를 생성할 때에는 변경가능한 객체인 `MutableLiveData` 로 생성하고 View 에 제공할 때에는 변경이 불가능한 `LiveData` 타입으로 제공합니다. 그 이유는 LiveData 는 오직 View Model에서만 변경이 가능해야 하기 때문입니다.

View Model에서 코루틴을 사용할 때에 직접 CoroutineScope 를 생성하고 생명주기를 관리할 수도 있지만 플러그인 형태로 View Model 용 CoroutineScope 인 `viewModelScope` 를 제공합니다.

`viewModelScope` 를 사용하기 위해서 아래와 같이 build.gradle에 종속성을 추가합니다.

~~~kotlin
dependencies {
    ....
    implementation 'androidx.lifecycle:lifecycle-viewmodel-ktx:2.2.0'
}
~~~

View Model 이 생성이 되면 Model을 통해서 공급 받은 ImageData 를 바로 View에게 념겨주는 것이 아니라 LiveData에 List 형태로 저장을 합니다.

단순히 LiveData 를 업데이트 하는 것만으로도 그 변경 사실이 View에게 전달이 된다는 장점이 있습니다.

### View

Presenter 같은 경우는 View에서 직접 생성해서 가지고 있었지만 View Model 같은 경우는 ViewModelProvider를 통해 가져옵니다.

~~~kotlin

class MainActivity : AppCompatActivity(), CoroutineScope {
    ...
    override fun onCreate(savedInstanceState: Bundle?) {
        ...
        val viewModel = ViewModelProvider(this)[MyViewModel::class.java]

        val counterTextView = findViewById<TextView>(R.id.counter)

        viewModel.getCounter()
            .observe(this, { count ->
                counterTextView.text = "count : $count"
            })

        viewModel.getImageDataList().observe(this, { newList ->
            viewAdapter.update(newList)
        })

    }
    ...
}
~~~

ViewModelProvider 를 통해 ViewModel 을 가져오는 이유는 View Model 을 단 한 개만 생성하기 위함입니다.

View Model은 한개 이상의 View와 관계를 가질수 있습니다. View Model은 LiveData 형태로 상태를 저장하고 있으며 만약 동일한 Class 로 여러개 의 View Model 인스턴스를 생성한다면 View Model간 데이터/상태 의 불일치가 발생할 것입니다.

ViewModelProvider 은 View Model 객체를 관리하는 Map을 가지고 있으며 View의 요청에 따라 View Model를 새로 생성하거나 기존 존재하는 View Model를 리턴 할 것입니다.

~~~java
public class ViewModelStore {

    private final HashMap<String, ViewModel> mMap = new HashMap<>();
    ...
}
~~~

View 는 LiveData에 observe() 를 통해서 LiveData 를 관찰하고 변경이 있을 시 View를 업데이트 하게 됩니다.

~~~kotlin
viewModel.getImageDataList().observe(this, { newList ->
    viewAdapter.update(newList)
})
~~~

추가로 View 의 RecyclerView에 업데이트 할 ImageData List를 이제 View Model이 관리하기 때문에 RecyclerView의 Adpater 는 이제 List 통째로 replace 를 해야 하기에 아래와 같이 update() 라는 함수를 추가했습니다.

~~~kotlin

class ImageDataAdapter(private val context: Context) :
    RecyclerView.Adapter<ImageDataAdapter.ViewHolder>() {

    ...
    fun update(list: List<ImageData>) {
        val diffCallback = Diff(imageDataList, list)
        val diffResult = DiffUtil.calculateDiff(diffCallback)

        imageDataList.clear()
        imageDataList.addAll(list)
        diffResult.dispatchUpdatesTo(this)
    }
    ...
}
~~~

### Model

Model은 변경되는 점이 없습니다.

## 마무리

MVVM 은 View Model를 사용함으로써 View와 Presenter 간 1대1 관계라는 단점을 극복하고 View와 View Model 간 N대1 이라는 관계를 가질수 있게 됩니다.

View와 View Model 간 N대1 이라는 관계는 View 는 결국 하나의 data를 공유함으로써 Activity-Activity, Activity-Fragment, Fragment-Fragment 간 통신이 가능하게 됩니다.
마치 여러 개의 쓰레드가 공유 data를 통해 통신하는 것처럼요!!

하지만 위 코드는 View를 업데이트를 할 때 data binding이 아닌 observer 패턴을 사용하였습니다.
다음 포스트에서는 data binding을 어떻게 적용하는 방법에 대해 살펴보겠습니다.

## 참고

- [[Android] Coroutine을 활용한 안드로이드 디자인 패턴 - MVC](./2021-05-07-coroutine-mvc)
- [[Android] Coroutine을 활용한 안드로이드 디자인 패턴 - MVP](./2021-05-24-coroutine-mvp)