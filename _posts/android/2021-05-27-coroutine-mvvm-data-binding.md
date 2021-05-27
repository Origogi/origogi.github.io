---
published: true
title: "[Android] Coroutine을 활용한 안드로이드 디자인 패턴 - MVVM (Data binding)"	
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

<div align="center">
<img src="https://miro.medium.com/max/2140/1*pgFREsmroqa52ITl4iBPZQ.png" >
</div>


## 서두

저번 포스트에서는 MVP에서 MVVM 으로 패턴을 변경하는 과정을 살펴보았습니다. View Model에서 data 가 변경이 되었을 때 View를 업데이트가 되는 과정은 Data binding 이 아닌 LiveData 에 옵저버를 등록하고 Data 변경을 관찰함으로써 View를 직접 업데이트를 하였습니다. 이번 포스트에서는 Data binding을 적용하여 View를 업데이트를 해보도록 하겠습니다.

## Data binding 이란?

Data binding에 대해서 알기 전에 먼저 선언적 UI 패턴과 프로그래미틱 UI 패턴의 차이 점에 대해 알아 보도록합시다.

프로그래미틱 UI 패턴이란 이벤트가 발생하면 메인 컨트롤러가 Data 를 업데이트를 하고 업데이트 할 View 를 선택하여 Data를 사용하여 View 를 업데이트하는 방식입니다. 가장 기초적이고 고전적인 방식입니다.

선언적 UI 패턴이란 View가 Data 를 관찰하고 있으며 만약 Data가 변경이 되면 View가 Data 값을 읽어서 View 스스로 업데이트가 되는 방식입니다.
여기서 View와 Data 를 서로 이어줘야(Binding) 하는데 View와 Data 를 서로 이어주도록 하는 것이 Data bidning 입니다.

Android에서는 Jetpack에 databinding library로 제공하고 있으며 이를 사용하여 View와 Data간 Data binding을 만들어 줍니다.

## 실행 화면

<div align="center">
<img src="https://user-images.githubusercontent.com/35194820/117981811-5e049600-b370-11eb-97d4-52fca92cf356.gif" >
</div></br>

앱에 대해 간단히 설명하자면 서버로 부터 HTML 문서를 다운받고 문서로 부터 이미지-제목을 파싱하여 RecyclerView에 뿌려주는 갤러리 앱입니다. 비동기 처리를 위해 저는 Coroutine을 사용했습니다.

실행화면은 MVC, MVP, MVVM 패턴 모두 동일합니다.

## 샘플 코드

아래 Repository를 참고바랍니다.

[Repository](https://github.com/Origogi/Android-Coroutine-Galley-App)

> git checkout mvvm-databiding

## Data binding 적용

### 1. gradle 설정

~~~gradle
plugins { 
    ...
    id 'kotlin-kapt' //1. plug in kapt 추가
}

android {
    ...

    dataBinding {   // 2. data bing enable
        enabled = true
    }
    ...
}

dependencies {
    ...
    kapt "com.android.databinding:compiler:3.0.1" // 3. databinding 종속성 추가
}

~~~

### 2. Activity Layout 수정

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<layout>  // 1. Root 를 layout으로 변경한다.
    <data>  //2. Binding 을 할 View Model를 설정한다.
        <variable
            name="viewmodel"
            type="com.origogi.gallery.vm.MyViewModel" />
    </data>

    <androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context=".MainActivity">

        <TextView
            android:id="@+id/counter"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="10dp"
            android:layout_marginBottom="15dp"
            android:text="@{`Image Count :  ` + viewmodel.counter}"  //3. TextView와 VM의 counter와 바인딩을 한다.
            app:layout_constraintBottom_toTopOf="@id/image_title_list"
            app:layout_constraintLeft_toLeftOf="parent"
            app:layout_constraintRight_toRightOf="parent"
            app:layout_constraintTop_toTopOf="parent"
            tools:text="count : 5" />

        <androidx.recyclerview.widget.RecyclerView
            android:id="@+id/image_title_list"
            android:layout_width="match_parent"
            android:layout_height="0dp"
            bindItem="@{viewmodel.imageDataList}"  //4. RecyclerView VM의 imageDataList와 바인딩을 한다.
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintTop_toBottomOf="@id/counter" />
    </androidx.constraintlayout.widget.ConstraintLayout>
</layout>
~~~

여기서 주목해야 할 것은 4번 항목의 bindItem은 기존에 존재한 attr 값이 아닌 개발자가 새로 추가한 값입니다.
따라서 이 값에 대해 설정을 해야 합니다.

### 3. BindingUtil.kt 추가

파일 명/패키지 위치/ 함수명은 중요하지 않습니다. 하지만 @BindingAdapter 의 값이 중요합니다. 

위에서 새로 정의한 시그니쳐와 반드시 일치시켜야 합니다.

~~~kotin
@BindingAdapter("bindItem")
fun bindItem(recyclerView: RecyclerView, items: LiveData<List<ImageData>>) {
    val adapter = recyclerView.adapter
    if (adapter is ImageDataAdapter) {
        items.value?.let {
            adapter.update(it)
        }
    }
}
~~~

### 4. Main Activity 수정

~~~kotlin
class MainActivity : AppCompatActivity(), CoroutineScope {
    ...

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        val binding: ActivityMainBinding = DataBindingUtil.setContentView( // 1. DatadingUtil 을 사용하도록 수정한다.
            this, R.layout.activity_main
        )

        ...

        val viewModel = ViewModelProvider(this)[MyViewModel::class.java]

        binding.viewmodel = viewModel  //2. 기존의 liveData를 observe 하는 코드를 제거한다.
        binding.lifecycleOwner = this  //3. ViewModel과 lifeCycle Owner 값을 set한다.
    }
~~~

1번 부터 4번까지 작업을 수행했으면 기본적인 Data binding은 동작할 것입니다.
앱을 빌드하고 실행해봐서 잘 동작하는 지 확인해 봅시다.

혹시 잘 안되시나요?

<div align="center">
<img src="https://user-images.githubusercontent.com/35194820/119776774-50b6e200-bf00-11eb-893d-75cbd8412ce8.jpeg" >
</div>

괜찮습니다. 저도 처음엔 잘 이해도 안되고 동작도 안하더라고요. Data binding 이라는 게 원래 어려운 내용입니다.

처음으로 돌아가서 빠진 것이 없는 지 다시 살펴 봅시다!!

잘 동작하면 이제 다음 단계에서는 RecyclerView 의 item view와 item data 를 binding 할수 있도록 수정해봅시다.

### 5. Item view 수정

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<layout> // 1. Root 를 layout 으로 감싸줍니다.
    <data> 
        <variable  // 2. 바인딩할 item data를 지정합니다.
            name="item"
            type="com.origogi.gallery.model.ImageData" />
    </data>

    <androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <ImageView
            loadImage="@{item.imageUrl}" //3. 이미지 뷰에 뿌려줄 이미지 url과 바인딩합니다.
            android:scaleType="centerCrop"
            android:id="@+id/image"
            android:layout_width="150dp"
            android:layout_height="150dp"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintLeft_toLeftOf="parent"
            app:layout_constraintTop_toTopOf="parent" />

        <TextView
            android:id="@+id/title"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginLeft="15dp"
            android:layout_marginRight="15dp"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintLeft_toRightOf="@id/image"
            app:layout_constraintRight_toRightOf="parent"
            app:layout_constraintTop_toTopOf="parent"
            android:text="@{item.imageTitle}"  //4. 텍스트 뷰와 이미지 title을 바인딩합니다.
            tools:text="Title" />

    </androidx.constraintlayout.widget.ConstraintLayout>

</layout>
~~~

여기서 주목해야 할 것은 3번 항목의 loadImage 은 기존에 존재한 attr 값이 아닌 개발자가 새로 추가한 값입니다.
따라서 위와 마찬가지로 이 값에 대해 설정을 해야 합니다.

### 6. BindingUtil.kt 에 loadImage 추가

~~~kotlin
... 
@BindingAdapter("loadImage")
fun loadImage(imageView: ImageView, imageUrl: String) {
    Glide.with(imageView.context)
        .load(imageUrl)
        .into(imageView)
}
~~~

위에 bindItem 를 추가 한 것처럼 loadImage 를 추가합니다.

### 7. RecyclerView Adapter 수정

~~~kotlin

class ImageDataAdapter(private val context: Context) :
    RecyclerView.Adapter<ImageDataAdapter.ViewHolder>() {

    private val imageDataList = mutableListOf<ImageData>()


    // 1. ViewHolder를 다음과 같이 수정합니다.
    class ViewHolder(private val binding: ListItemBinding) : RecyclerView.ViewHolder(binding.root) {
        fun bind(data: ImageData) {
            binding.item = data
            binding.executePendingBindings()
        }
    }

    // 2. item view xml을 inflate하는 코드를 DataBindingUtil을 사용하도록 수정합니다.
    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
        val binding = DataBindingUtil.inflate<ListItemBinding>(LayoutInflater.from(parent.context),
            R.layout.list_item, parent, false)
        return ViewHolder(binding)
    }

    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        val imageData = imageDataList[position]

        // 3. view holder에 직접 view를 업데이트 하는 코드를 제거합니다.
        // 4. holder.bind() 를 호출하는 코드를 추가합니다.
       holder.bind(imageData)
    }
    ...
}
~~~

참고로 ListItemBinding class는 빌드 시 Data Binding 컴파일러를 통해 자동으로 생성되는 class 입니다.

class 명은 아래와 같이 xml 파일명 기준으로 생성이 됩니다.

<div align="center">
<img src="https://user-images.githubusercontent.com/35194820/119778746-d9368200-bf02-11eb-8491-5b7d47a6f06a.png" >
</div>

저 같은 경우 list_item 이라는 파일명을 사용했기 때문에 ListItemBinding 으로 생성이 됬습니다.

만약 에러가 발생하면 파일명에 맞는 class 이름으로 변경하시길 바랍니다.

### 8. 최종 확인

이제 빌드를 하고 정상적으로 동작하는 지 확인 해 봅시다. 만약 빌드 에러가 발생하거나 실행 시 crash가 발생한 경우 어느 스텝을 빼먹거나 아니면 잘못 수정했거나 하는 문제일 것입니다.

만약 잘 안되거나 하는 데 어려움이 있는 경우 1~4번 까지 먼저 확인 후 잘되시면 5~7번 까지 나눠서 진행하는 것을 추천드립니다.

### 마무리

Databiding 을 이용한 MVVM 패턴 같은 경우 View와 View Model간 약한 종속성을 가진다는 장점이 있지만 그만큼 러닝 커브가 높고 기존 프로젝트에 적용하기 어려울수 있으며 MVC, MVP 보다 코드를 이해하기 어렵다는 단점이 있습니다.

따라서 무조건 MVVM 패턴을 적용하기 보다는 현재 상황에서 제일 적합한 것이 어떤 것인 지 잘 판단하고 적용하기를 바랍니다.

## 참고

- [[Android] Coroutine을 활용한 안드로이드 디자인 패턴 - MVC](./2021-05-07-coroutine-mvc)
- [[Android] Coroutine을 활용한 안드로이드 디자인 패턴 - MVP](./2021-05-24-coroutine-mvp)
- [[Android] Coroutine을 활용한 안드로이드 디자인 패턴 - MVVM-LiveData](./2021-05-26-coroutine-mvvm-livedata)
