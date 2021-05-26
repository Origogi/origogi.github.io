---
published: false
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
  overlay_filter: 0.5	
sitemap :	
  changefreq : daily	
  priority : 1.0	
---

## 서두

MVP 패턴을 사용할 때 가장 큰 문제점은 View와 Presenter 간의 강한 종속성으로 인해 1대1 관계가 생기고 Presenter는 다른 View와 결합을 할수 없었습니다. 이에 대한 단점을 극복한 것이 MVVM 패턴입니다.

## 실행 화면

<div align="center">
<img src="https://user-images.githubusercontent.com/35194820/117981811-5e049600-b370-11eb-97d4-52fca92cf356.gif" >
</div></br>

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



## 마무리

MVP의 Presenter는 MVC 의 Controller와 대비 Android Framework와 종속성이 제거가 되어 테스트 코드 작성 및 Android Framework 변경 사항에 영향을 받지 않는 다는 장점이 있지만 View와 1대1로 종속성 때문에 Presenter는 다른 View와 결합할수 없습니다.

위 장점을 살리면서 단점을 극복한 것이 MVVM 입니다. 다음 포스트에서 기존 MVP에서 MVVM 으로 변경하는 과정을 다루도록 하겠습니다.

## 참고

[[Android] Coroutine을 활용한 안드로이드 디자인 패턴 - MVC](./2021-05-07-coroutine-mvc)