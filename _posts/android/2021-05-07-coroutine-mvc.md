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
  overlay_filter: 0.5	
sitemap :	
  changefreq : daily	
  priority : 1.0	
---

## 서두

Coroutine을 이용하여 비동기적으로 서버로 부터 데이터를 다운받고 그 결과를 화면에 뿌려주는 기능을 구현한다고 가정했을 때 가장 대표적인 디자인 패턴인 MVC, MVP, MVVM 을 어떻게 구현하는 지를 간단한 샘플코드를 이용하여 나타 내고자 합니다. 먼저 디자인 패턴 중 MVC 부터 살펴보도록 하겠습니다.

## 실행 화면

<div align="center">
<img src="https://user-images.githubusercontent.com/35194820/117981811-5e049600-b370-11eb-97d4-52fca92cf356.gif" >
</div>

앱에 대해 간단히 설명하자면 서버로 부터 HTML 문서를 다운받고 문서로 부터 이미지-제목을 파싱하여 RecyclerView에 뿌려주는 갤러리 앱입니다. 비동기 처리를 위해 저는 Coroutine을 사용했습니다.

## 샘플 코드

아래 Repository를 참고바랍니다.

[Repository](https://github.com/Origogi/Android-Coroutine-Galley-App)

## MVC

MVC 는 Model-View-Controller의 약자로써 3개의 집합으로 구분합니다.

<div align="center">
<img src="https://user-images.githubusercontent.com/35194820/119347519-98f2bc00-bcd6-11eb-8a3f-23434a453e37.PNG" >
</div>

### View

화면을 구성하는 컴포넌트입니다.

위 샘플코드에서 View는 Layout Xml, RecyclerView Adapter인 ImageDataAdapter가 있습니다.

~~~kotlin
class ImageDataAdapter(private val context: Context) :
    RecyclerView.Adapter<ImageDataAdapter.ViewHolder>() {

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
        notifyItemInserted(imageDataList.size)
    }
}
~~~

ImageDataAdapter 는 add() 를 통해 새로운 item을 받아서 Recycler view에 계속 추가를 하는 역할을 합니다.

### Model

데이터를 구성하는 컴포넌트입니다.

Image Url과 Image Title를 저장하는 data class와 data class 를 생성하는 data provider 가 있습니다.

#### ImageData

~~~kotlin
data class ImageData(val imageUrl : String, val imageTitle : String)
~~~

#### ImageDataProvider

~~~kotlin
class ImageDataProvider {
    private val baseUrl = "https://www.gettyimagesgallery.com/collection/sasha/"

    fun get(coroutineScope: CoroutineScope): ReceiveChannel<ImageData> {
        return coroutineScope.produce(IO) {
            try {
                val url = URL(baseUrl)
                val conn: HttpURLConnection = url.openConnection() as HttpURLConnection

                if (conn != null) {
                    conn.connectTimeout = 2000;
                    conn.useCaches = false;
                    if (conn.responseCode
                        == HttpURLConnection.HTTP_OK
                    ) {
                        //    데이터 읽기
                        val br = BufferedReader(InputStreamReader(conn.inputStream, "euc-kr"))
                        br.lineSequence()
                            .filter { it.contains("img class=") }
                            .forEach {

                                val pattern = Pattern.compile("\"(.*?)\"")

                                val matcher = pattern.matcher(it)

                                var index = 0

                                var imageUrl = ""
                                var title = ""

                                while (matcher.find()) {
                                    val token = (matcher.group(1))
                                    if (index == 1) {
                                        imageUrl = token
                                    } else if (index == 2) {
                                        title = token
                                        val imageData =
                                            ImageData(imageUrl = imageUrl, imageTitle = title)
                                        send(imageData)
                                    }
                                    index += 1
                                }
                            }
                        br.close(); // 스트림 해제
                    }
                    println("=========end=========")
                    conn.disconnect()
                }
            } catch (e: Exception) {
                e.printStackTrace()
            }
        }
    }
}
~~~

HTTP 통신 후 HTML 문서를 파싱하여 ImageData 들을 `Coroutine` 의 `producer` 를 이용하여 하나씩 생산을 하여 Controller에게 전달을 합니다.
네트워크 통신, 문서 파싱하는 동작은 시간이 오래 걸리는 작업이기 때문에 Main Thread 가 아닌 Working Thread를 통해서 수행이 되어야 합니다.

### Controller

View나 Anroid Framework 부터 이벤트를 받고 Model을 업데이트하고 업데이트 된 Model을 이용하여 View를 업데이트하는 모듈입니다.

Andoid MVC 에서 Controller는 Activity가 담당을 합니다.

#### MainActivity

~~~kotlin

class MainActivity : AppCompatActivity(), CoroutineScope {
    lateinit var job: Job

    private lateinit var recyclerView: RecyclerView
    private lateinit var viewAdapter: ImageDataAdapter
    private lateinit var viewManager: RecyclerView.LayoutManager
    private var dataCount = 0

    override val coroutineContext: CoroutineContext
        get() = Main + job

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        job = Job()

        viewManager = LinearLayoutManager(this)
        viewAdapter = ImageDataAdapter(this)
        recyclerView = this.findViewById<RecyclerView>(R.id.image_title_list).apply {
            layoutManager = viewManager
            adapter = viewAdapter
        }

        launch {
            dataCount = 0
            findViewById<TextView>(R.id.counter).text = "Image Count : $dataCount"
            val channel = ImageDataProvider().get(this)

            println("channel status ${channel.isClosedForReceive}")
            channel.consumeEach {
                withContext(Main) {
                    dataCount++
                    findViewById<TextView>(R.id.counter).text = "Image Count : $dataCount"
                    viewAdapter.add(it)
                }
            }

            println("channel status ${channel.isClosedForReceive}")
        }
    }

    override fun onDestroy() {
        super.onDestroy()
        job.cancel()
    }

}
~~~

여기서 Controller가 받은 이벤트는 Activity가 생성이 완료가 되었다는 `onCreate()` 입니다.
`onCreate()` 에서 `ImageDataProvider` 를 생성하고 Corouine의 Received Channel 를 통해 `ImageData` 를 공급을 받고 RecyclerView를 업데이트 합니다.

중요한 점은 RecyclerView를 업데이트 할 때에는 Working Thread 에서 Main Thread 로 스위칭을 해야 정상적으로 업데이트가 가능합니다.

## MVC 패턴의 장점

- 구조가 단순하고 구현이 쉽습니다. 따라서 개발 기간이 비교적 짧습니다. 구조가 단순하고 기능이 비교적 적은 case에 적합합니다.

## MVC 패턴의 단점

- Contoller가 Android Framework 또는 View에 강력하게 종속이 되어 있습니다. 그렇기에 Controller를 다른 View로 교체가 불가능하고 Android Framework가 변경이 되면 Controller에도 영향이 발생할 수도 있습니다.
- 위에서 언급한 종속성 때문에 Controller 로직에 대해서 테스트 코드를 작성하기가 힘듭니다.
- 기능이 추가가 될수록 Controller가 복잡해지고 코드 사이즈가 커지므로 유지 보수를 어렵게 합니다.

## 마무리

MVC는 구조가 단순하고 구현하기가 쉽다는 장점이 있지만 기능이 추가가 될 수록 유지보수의 어려움이 발생을 합니다.
따라서 위 단점을 극복한 것이 MVP 패턴입니다.

다음 포스트에서 MVP에 대해 알아 보도록 하겠습니다.

## 참고

[[Android] Coroutine을 활용한 안드로이드 디자인 패턴 - MVP](./2021-05-24-coroutine-mvp)