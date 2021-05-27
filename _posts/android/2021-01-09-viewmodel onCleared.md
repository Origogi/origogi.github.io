---
published: true
title: "[Android] ViewModel의 onCleared() 활용법"	
excerpt : " "	
layout: single	
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
  - MVVM
comments: true	
header:
  overlay_image: https://user-images.githubusercontent.com/35194820/119770376-18f76c80-bef7-11eb-8b3e-abca9300d1c1.gif
  overlay_filter: 0.4
  teaser: https://www.pngkey.com/png/full/27-278037_banner-freeuse-library-android-transparent-app-android-development.png
sitemap :	
  changefreq : daily	
  priority : 1.0	
---

ViewModel 의 onCleared()를 사용하여 리소르를 어떻게 해제를 할 것인가에 대해 다루는 포스팅입니다.

## 1. Sample App 소개

아래 실행되는 앱은 간단한 갤러리 앱으로

최초 앱이 실행이 되면 로딩 화면이 보여지고 로딩이 완료되면 Image와 title이 RecyclerView로 보여지게 됩니다.

<div align="center">
<img src="https://user-images.githubusercontent.com/35194820/104088168-31e4e580-52a8-11eb-8333-f7aedd046fb2.gif" >
</div>

실행 과정을 간략히 살펴보면

1. 앱을 실행
2. Main Activity 생성
3. View Model를 생성하고 Main Activity와 바인딩
4. View Model의 생성자에서 `fetchData()` 를 호출하여 `ImageData` 를 다운로드
5. Image Data 를 모두 다운로드하였으면 `LiveData` 에 업데이트
6. Main Activity는 `ImageData` 를 이용하여 `RecyclerView` 를 업데이트

ViewModel의 코드는 아래와 같습니다.

~~~kotlin

class ImageDataViewModel : ViewModel() {

    private val imageDataList: MutableLiveData<List<ImageData>> = MutableLiveData()
    private val viewType: MutableLiveData<ViewType> = MutableLiveData()
    private val appState: MutableLiveData<AppState> = MutableLiveData()

    init {
        Log.d("TEST","ViewModel init()")
        fetchData()
    }

    fun fetchData() {
        appState.postValue(AppState.LOADING)

        ImageDataProvider.fetch()
            .subscribeOn(Schedulers.io())
            .subscribe({ list ->
                imageDataList.postValue(list)
                appState.postValue(AppState.LOADED)
            }, {
                appState.postValue(AppState.ERROR)
            })
    }

    ....
}

~~~

## 2. Sample App 의 문제점

만약 앱이 로딩 중, 즉 `fetchData()` 호출 중에 만약 사용자가 백 버튼을 누르게 되면 Main Activity와 View Model이 정리가 되는데 이 때 `fetchData()` 의 `subscribe()` 의 lambda 문에 `LiveData` 들을 를 참조하고 있기 때문에 View Model 이 JVM Heap 에서 정리되지 않아 메모리 누수의 원인이 될 수 있습니다.
  
## 3. Sample App 개선 방향

Activity로 예를 들면 Activity의 메모리 누수를 막기 위해 `onCreate()` 에서 생성된 리소스는 `onDestroy()` 에서 리소스를 정리를 합니다.
  
View Model 에는 Activity와 의 `onDestory()` 와 유사한 callback 인 `onCleared()` 가 존재합니다.

`onCleared()` 가 호출되는 시점은 아래와 같습니다.

<div align="center">
<img src="https://2.bp.blogspot.com/-yDA6lQPeUM0/WjMEoM8_qsI/AAAAAAAABrU/aSrk1ePRyugp6Mna8mSPlq5K-4Moz9EcACLcBGAs/s1600/image1.pn" >
</div>

따라서 onCleared() 메소드를 override 하고 리소르를 정리하는 코드를 추가해야 합니다.

## 4. Sample App 수정

1. 먼저 View Model에 맴버 변수로 `disposable` 를 추가합니다.
2. `subscribe()` 의 리턴 값을 위에 추가한 `disposable` 에 저장합니다.
3. `onCleared()` 를 `override` 를 합니다.
4. `onCleared()` 메소드안에 `disposable.dispose()` 코드를 추가합니다.

~~~ kotlin

class ImageDataViewModel : ViewModel() {

    private val imageDataList: MutableLiveData<List<ImageData>> = MutableLiveData()
    private val viewType: MutableLiveData<ViewType> = MutableLiveData()
    private val appState: MutableLiveData<AppState> = MutableLiveData()
    private var disposable: Disposable? = null // 1. Disposable 추가


    fun fetchData() {
        appState.postValue(AppState.LOADING)

        disposable = ImageDataProvider.fetch() // 2. subsribe() 의 반환 값을 맴버 변수에 저장
            .subscribeOn(Schedulers.io())
            .subscribe({ list ->
                imageDataList.postValue(list)
                appState.postValue(AppState.LOADED)
            }, {
                appState.postValue(AppState.ERROR)
            })
    }

    override fun onCleared() {    // 3. onCleared() override 
        super.onCleared()
        if (disposable?.isDisposed == false) { // 4. onCleared() 될 때 dispose() 호출
            disposable?.dispose()
        }
    }
}
~~~

## 5. 총평

위 예제는 rxAndroid 의 observable 를 리소스의 예시로 들었습니다. 이 외에 리소스로는 thread나 data 를 저장하는 class 가 있을 수 있습니다. 잘 판단하여 꼭 리소스를 해제를 하여 불필요한 메모리 누수를 막읍시다!