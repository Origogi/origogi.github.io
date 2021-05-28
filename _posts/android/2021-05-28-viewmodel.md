---
published: false
title: "[Android][Jetpack] View Model"	
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
  - Kotlin
  - ViewModel
  - JetPack
comments: true	
header:
  overlay_image: https://user-images.githubusercontent.com/35194820/119770376-18f76c80-bef7-11eb-8b3e-abca9300d1c1.gif
  overlay_filter: 0.4
  teaser: https://www.pngkey.com/png/full/27-278037_banner-freeuse-library-android-transparent-app-android-development.png
sitemap :	
  changefreq : daily	
  priority : 1.0	
---

<div align="center">
<img src="https://user-images.githubusercontent.com/35194820/119796009-f58eea80-bf13-11eb-9d8b-cfbae470312b.PNG" >
</div>

## 서두

해당 포스트는 Android Jetpack 중 하나인 View Model 에 대해서 정리한 것입니다.

## ViewModel

ViewModel 는 Android MVVM 디자인 패턴을 구현하기 위해서 사용되는 컴포넌트입니다.

만약 Android MVVM 디자인 패턴을 구현 할 때에는 보통 View Model과 아래의 컴포넌트와 같이 set로 묶어서 사용합니다.

- [LiveData](./2021-05-27-livedata)
- DataBinding

주된 목적인 액티비티나 프래그먼트에서 사용되는 데이터들을 저장/관리하는 모듈입니다. 액티비티나 플래그먼트와 독립적인 생명 주기를 가지고 있어서 액티비티나 플래그먼트가 종료되어도 계속 생존이 가능하기에 UI 변경에 필요한 데이터들을 저장/관리가 용의합니다.

여기서 데이터는 `LiveData` 형태로 저장이 되며 액티비티/프래그먼트와 View Model간 서로 이어주는 것이 `Data biding` 입니다. `LiveData` 나 `Data biding` 은 바로 위 링크를 참고 바랍니다.

## View Model 구현

새로운 View Model를 구현 할 때에는 ViewModel 클래스를 상속받아서 사용합니다.

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
}
~~~

만약 ViewModel에 Context가 필요하면 AndroidViewModel를 상속합니다.

~~~kotlin
class MyViewModel(application: Application) : AndroidViewModel(application) {
    
    val context : Context = application
}
~~~



## View Model 생명주기

<div align="center">
<img src="https://developer.android.com/images/topic/libraries/architecture/viewmodel-lifecycle.png?hl=ko" >
</div>