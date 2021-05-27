---
published: true
title: "[Android] Glide 를 이용하여 RecylerView Image Loading 속도 단축하기"	
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
  - Glide
comments: true	
header:
  overlay_image: https://user-images.githubusercontent.com/35194820/119770376-18f76c80-bef7-11eb-8b3e-abca9300d1c1.gif
  overlay_filter: 0.8
  teaser: https://www.pngkey.com/png/full/27-278037_banner-freeuse-library-android-transparent-app-android-development.png
sitemap :	
  changefreq : daily	
  priority : 1.0	
---

RecylerView 를 활용한 앱은 보통 다음과 같은 화면으로 구성이 될 것이다.

<div align="center">
<img src="https://user-images.githubusercontent.com/35194820/104088168-31e4e580-52a8-11eb-8333-f7aedd046fb2.gif" >
</div>

위 앱은 서버로 부터 imageUrl 과 title 데이터를 list 형태로 받아오고 그 list를 RecyclerView 에 뿌려주는 간단한 기능을 제공한다.

앱을 설치하고 스크롤을 하면 아래와 같이 title이 바로 보이지만 Image 같인 경우는 몇초간 딜레이 후 보이는 현상을 볼수가 있을 것이다.

<div align="center">
<img src="https://user-images.githubusercontent.com/35194820/117423140-594c7600-af5b-11eb-9123-4f6c289da95a.gif" >
</div>

위 현상이 발생하는 이유는 title 같은 경우 바로 TextView 에 set을 하지만 Image 같은 경우는 ImageUrl를 이용하여 서버로 부터 다운받아야 하는 과정이 추가로 들어가기 때문이다.
만약 Image Size가 클 수록 로드 딜레이는 더 길어질 것이다.

해당 포스트의 목적은 RecyclerView 에서 스크롤 시 이미지 로딩 시간을 단축하는 방법에 대해 다루도록 하겠다.

## 스크롤 시 이미지 로딩 딜레이가 왜 발생하는 가?

스크롤 시 이미지 딜레이가 발생하는 원인에 대해서 살펴보기 위해 RecyclerView의 동작 방식에 대해 간략히 살펴보면
RecyclerView는 RecyclerViewAdapter로 부터 item layout를 제공받는다.

보통 RecyclerViewAdpater는 다음과 같이 구현 할 것이다.

~~~kotlin
class ImageDataAdapter(private val context: Context) : RecyclerView.Adapter<ImageDataAdapter.ViewHolder>() {

    private val imageDataList = mutableListOf<ImageData>()

    class ViewHolder(itemView: View) : RecyclerView.ViewHolder(itemView) {
        val image: ImageView = itemView.findViewById(R.id.image)
        val title: TextView = itemView.findViewById(R.id.title)
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
        val convertView =
            LayoutInflater.from(parent.context).inflate(R.layout.list_item, parent, false)
        return ViewHolder(convertView)
    }

    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        val imageData = imageDataList[position]

        holder.apply {
            Glide.with(context).load(imageData.imageUrl)
                .override(150, 150)
                .into(image)
            title.text = imageData.imageTitle
        }
    }

    override fun getItemCount() = imageDataList.size

    fun add(imageData: ImageData) {
        imageDataList.add(imageData)

        GlobalScope.launch(Main) {
            notifyItemInserted(imageDataList.size)
        }
    }
}
~~~

여기서 핵심은 onBindViewHolder() 다. 사용자가 스크롤 할때 새로 보여질 item view에 대해서 onBindViewHolder()가 호출이 된다.

예를 들어 앱이 실행하고 최초 5개의 item view가 보여지고 있다고 가정하자 사용자가 아래로 스크롤하게 되면 6번째 아이템이 보이게 되고 따라서 6번째 item view 에 대한 onBindViewHolder() 가 호출이 될 것이다.

~~~kotlin
override fun onBindViewHolder(holder: ViewHolder, position: Int) {
    val imageData = imageDataList[position]

    holder.apply {
        Glide.with(context).load(imageData.imageUrl)
            .override(150, 150)
            .into(image)
        title.text = imageData.imageTitle
    }
}
~~~

이 때 6번 째 아이템에 대한 타이틀과 이미지를 업데이트하게 되는 데 타이틀 같은 경우는 data 객체에 저장되어 있기 때문에 TextView에 바로 업데이트가 된다.  
하지만 ImageView 같은 경우는 imageUrl 을 이용하여 network operation이 발생하고 이미지 데이터를 다운받아 비트맵으로 변환 후 ImageView에 set을 하기 때문에 당연히 이미지가 바로 보여질수가 없다.
따라서 스크롤을 할 때 새로 보여지는 item view에 대해서 이미지 로딩 딜레이가 발생 할 수 밖에 없다.

## 이미지 로딩 딜레이를 줄여보기

현실적으로 이미지 같은 경우에는 서버로 부터 다운받은 시간 자체를 줄이기는 어렵다. 가장 이상적인 방법으로는 썸네일용 저용량 이미지 파일을 서버에서 제공하는 것도 있지만 서버에서 제공하지 않는다면 결국 client, 즉 APP에서 문제를 해결해야 한다.

따라서 이미지를 다운받는 속도를 줄일수 없다면 그 다음 방법으로는 보여질 이미지를 미리 다운 받아서 app에 저장하는 것이다.
즉 cache를 이용하는 것이다. cache 자체는 직접 구현할수도 있지만 더 좋은 것은 라이브러리를 사용하는 것이다.
그 라이브러리는 바로 Glide다.

Glide 같은 경우 자체에 cache 동작을 제공한다. glide 의 cache 정책은 아래 링크를 참고 바란다.

[Glide Library](https://origogi.github.io/android/glide/)

## Glide Preloader 추가

Glide 자체에서 Preload api를 제공한다. preload api는 다음과 같다.

~~~kotlin
fun preload(context: Context,  url : String) {
    Glide.with(context).load(url)
        .preload(150, 150)
}
~~~

다음 onBindViewHolder 에 다음과 같은 코드를 추가한다.

~~~kotlin
override fun onBindViewHolder(holder: ViewHolder, position: Int) {
    val imageData = imageDataList[position]

    holder.apply {
        Glide.with(context).load(imageData.imageUrl)
            .override(150, 150)
            .into(image)
        title.text = imageData.imageTitle
    }

    //Preload
    if (position <= imageDataList.size) {
        val endPosition = if (position + 6 > imageDataList.size) {
            imageDataList.size
        } else {
            position + 6
        }
        imageDataList.subList(position, endPosition ).map { it.imageUrl }.forEach {
            preload(context, it)
        }
    }
}
~~~

추가한 로직에 대해 간략이 설명하자면 현재 보여지고 있는 item에 대해서 뒤에 6개의 이미지에 대해서 Preload를 실행하게 된다. 그림으로 표현하면 다음과 같다.

<div align="center">
  <img src="https://user-images.githubusercontent.com/35194820/117423182-65d0ce80-af5b-11eb-8d70-a576485ff938.png">
</div>
<br>

위 로직을 적용 하기 전과 적용 후 비교 결과는 다음과 같다.

|Preload 적용 전|Preload 적용 후|
|------|---|
|![default](https://user-images.githubusercontent.com/35194820/117423140-594c7600-af5b-11eb-9123-4f6c289da95a.gif)|![custom](https://user-images.githubusercontent.com/35194820/117423126-52bdfe80-af5b-11eb-8216-597f357a2cca.gif)|
  
  
참고로 Glide 자체에 디스크 캐쉬 때문에 앱을 지우고 새로 설치하여 확인을 하였다.
몇몇 아이템에 대해서 딜레이는 있지만 대부분 스크롤 할 때 바로 바로 표시되는 것을 볼 수가 있다.

## 정리

1. Recycler View에서 고용량 이미지를 다운받아서 표시할 경우 스크롤 할 때 마다 이미지 로드 딜레이가 발생할 수 있다.
2. 이미지 로드 딜레이를 줄이기 위해 나중에 보여질 이미지를 미리 다운을 받아 캐시에 저장을 한다.
3. 캐시에 저장하기 위해 Glide의 preload() 함수를 사용한다.
