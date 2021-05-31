---
published: true
title: "[Android][Jetpack] Data Binding"	
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
  - DataBinding
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

![databinding](https://user-images.githubusercontent.com/35194820/119796020-f758ae00-bf13-11eb-975f-f09bfd895ef1.PNG)

## 서론

해당 포스트는 Android Jetpack 중 하나인 Data Binding 에 대해서 정리한 것입니다.

## Databinding

DataBinding 은 선언적 UI 방식을 지원하기 위한 라이브러리로 Data와 View를 결합을 시킵니다.

DataBinding 의 장점은 아래와 같습니다.

- data 가 바뀌면 자동으로 View 를 변경하게 할 수 있다.
- xml 리소스만 보고도 View 에 어떤 데이터가 들어가는지 파악이 가능하다.
- 코드 가독성이 좋아지고, 상대적으로 코드량이 줄어든다.

Data binding는 주로 Android MVVM 패턴을 구현하기 위해 사용이 됩니다. 참고로 MVVM 패턴을 구현할 때에는 Data binding외에 아래 라이브러리과 같이 사용하게 됩니다.

- [View Model](https://origogi.github.io/android/viewmodel/)
- [Live Data](https://origogi.github.io/android/livedata/)

Data를 읽어서 View 를 업데이트하는 고전적인 방식은 아래와 같이 findViewByID() api를 사용하여 View를 접근한 다음 set api를 사용하여 View에 data를 set를 하게됩니다.

~~~java
  TextView textView = findViewById(R.id.sample_text);
    textView.setText(viewModel.getUserName());
~~~

하지만 Data binding 을 사용함으로써 자바/코틀린 코드 없이 xml 파일에 직접 data 를 할당함으로써 view 를 업데이트하게 됩니다. 

~~~xml
<TextView
        android:text="@{viewmodel.userName}" />
~~~

참고로 xml 에 할당식을 작성할 때에는 `@{}`를 사용합니다.

## Data Binding 사용법

### 1. Layout 파일 수정

Data와 바인딩할 기존 layout 파일을 아래와 같이 수정해야 합니다.

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<layout>
    <data>
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
            android:text="@{`Image Count :  ` + viewmodel.counter}"
            app:layout_constraintBottom_toTopOf="@id/image_title_list"
            app:layout_constraintLeft_toLeftOf="parent"
            app:layout_constraintRight_toRightOf="parent"
            app:layout_constraintTop_toTopOf="parent"
            tools:text="count : 5" />

        <androidx.recyclerview.widget.RecyclerView
            android:id="@+id/image_title_list"
            android:layout_width="match_parent"
            android:layout_height="0dp"
            bindItem="@{viewmodel.imageDataList}"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintTop_toBottomOf="@id/counter" />
    </androidx.constraintlayout.widget.ConstraintLayout>
</layout>

~~~

1. 먼저 root를 `<layout>` 으로 감쌉니다.
2. Binding 할 data를 <data> 을 사용하여 선언합니다.
3. 할당식 `@{}`를 사용해서 View와 Data 를 연결합니다.

### 2. Data 객체 선언

위의 XML에 해당하는 Data 클래스를 선언합니다.

~~~kotlin
class MyViewModel : ViewModel() {
    private val imageDataList: MutableLiveData<List<ImageData>> =
        MutableLiveData<List<ImageData>>()

    private val counter: MutableLiveData<Int> = MutableLiveData<Int>()

    fun getImageDataList() = imageDataList as LiveData<List<ImageData>>
    fun getCounter() = counter as LiveData<Int>
}
~~~

위 예제에서는 Data를 LiveData 를 사용했습니다.

### 3. View와 Data 를 연결

액티비티나 프래그먼트에 아래와 같이 데이터 바인딩을 시도합니다.

~~~kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    val binding: ActivityMainBinding = ActivityMainBinding.inflate(layoutInflater)

    val viewModel = ViewModelProvider(this)[MyViewModel::class.java]

    binding.viewmodel = viewModel
    binding.lifecycleOwner = this
}
~~~

여기서 `ActivityMainBinding` 은 컴파일러를 통해 생성된 클래스입니다. 이름은 xml 파일명 기준으로 생성이 되며 여기서 사용된 xml이름은 `activity_main.xml` 입니다.

`binding.viewmodel` 에서 `viewmodel` 은 `activity_main.xml` 안에 `<data>` 안에 `name` 기준으로 생성이 됩니다. `binding.lifecycleOwner` 같은 경우 Data가 LiveData 인 경우 추가적으로 등록해야 합니다.

### 4. BindingAdapter 설정

BindingAdapter 는 Data가 변경될 경우 안드로이드 프래임워크에서 자동으로 실행하는 API입니다.

BindingAdapter 은 위 예제에서 아래와 같이 `android:text` 와 `bindItem` 이 있습니다.

~~~xml
<TextView
  ...
    android:text="@{`Image Count :  ` + viewmodel.counter}"
    />

<androidx.recyclerview.widget.RecyclerView
    ...
    bindItem="@{viewmodel.imageDataList}"
/>
~~~

여기서 `android:text` 는 Pre-define 된 BindingAdapter이고 `bindItem` 은 커스텀으로 추가한 BindingAdapter 입니다.

`android:text` 은 아래와 같이 `TextViewBindingAdapter` 에 정의가 되어 있습니다.

~~~java
public class TextViewBindingAdapter {
    ...
    @BindingAdapter("android:text")
    public static void setText(TextView view, CharSequence text) {
        final CharSequence oldText = view.getText();
        if (text == oldText || (text == null && oldText.length() == 0)) {
            return;
        }
        if (text instanceof Spanned) {
            if (text.equals(oldText)) {
                return; // No change in the spans, so don't set anything.
            }
        } else if (!haveContentsChanged(text, oldText)) {
            return; // No content changes, so don't set anything.
        }
        view.setText(text);
    }
}
~~~

`bindItem` 은 안드로이드에서 기본적으로 제공하지 않는 BindingAdapter 로 아래와 같이 추가할수 있으며 @BindingAdapter 어노테이션을 이용하여 이름을 일치시킵니다.

~~~kotlin
@BindingAdapter("bindItem")
fun bindItem(recyclerView: RecyclerView, items: LiveData<List<ImageData>>) {
    val adapter = recyclerView.adapter
    if (adapter is ImageDataAdapter) {
        items.value?.let {
            println("bind item ${it.size}")
            adapter.update(it)
        }
    }
}
~~~

### Appendix. Data object 교체

일반적으로 View와 Data Object를 binding을 하고 Data Object 안의 맴버 변수를 업데이트 함으로써 View를 업데이트하지만 Data Object 자체를 변경해야 하는 case가 있을 수 있습니다.

예를 들어 RecyclerView의 ViewHolder를 업데이트할 때입니다.

~~~kotlin
class ImageDataAdapter(private val context: Context) :
  RecyclerView.Adapter<ImageDataAdapter.ViewHolder>() {

  private val imageDataList = mutableListOf<ImageData>()

  class ViewHolder(private val binding: ListItemBinding) : RecyclerView.ViewHolder(binding.root) {
      fun bind(itemData : ImageData) {
          binding.item = itemData
          binding.executePendingBindings()
      }
  }

  override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
      val binding = ListItemBinding.inflate(LayoutInflater.from(parent.context), parent, false)
      return ViewHolder(binding)
  }

  override fun onBindViewHolder(holder: ViewHolder, position: Int) {
      val imageData = imageDataList[position]
      holder.bind(imageData)
  }
  ...
}
~~~

위의 Data Binding 동작 순서는 다음과 같습니다.

1. onCreateViewHolder() 에 ListItemBinding.inflate() 를 이용하여 binding 을 생성합니다. ListItemBinding 은 컴파일러가 생성한 클래스로써 이름은 xml 파일명 기준으로 생성이 되며 여기서 사용된 xml이름은 `list_item.xml` 입니다.
2. binding 객체를 ViewHolder가 가지도록 합니다.
3. onBindViewHolder() 가 호출 될 때 ViewHolder의 bind() 를 호출합니다.
4. binding의 item에 새로운 data를 set을 함으로써 Data binding의 data 자체를 교체합니다.
5. 마지막으로 binding.executePendingBindings() 를 실행함으로써 안드로이드 프레임워크에 Data 가 변경되었음을 알립니다.

## 정리

Data  binding 같은 경우 여러가지 장점도 있지만 동작이 매우 복잡하고 러닝 커브가 존재하여 기존의 구조에 적용하기 어려울수도 있으며 앱 빌드시간이 늘어난다는 단점이 있습니다. 따라서 Data binding을 적용할 때 특장점을 잘 파악하고 적용하는 것이 중요합니다.

## 참조

[데이터 결합 라이브러리](https://developer.android.com/topic/libraries/data-binding?hl=ko)