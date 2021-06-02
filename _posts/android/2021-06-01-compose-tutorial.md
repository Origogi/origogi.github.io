---
published: false
title: "[Android][Compose] Tutorial Sample Source 분석"	
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
  - Compose
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
<img src="https://thdev.tech/images/posts/2020/10/Android-Jetpack-Compose-Basics/image.png" >
</div>

## 서론

해당 포스트는 Android Compose Tutorial Sample Source 에 대해 분석한 내용을 정리하였습니다.

## 실행화면
  
<div align="center">
<img src="https://user-images.githubusercontent.com/35194820/120441160-42b10780-c3bf-11eb-98a2-93e1c6f67959.png" width="30%" >
</div>

[Repository](https://github.com/Origogi/Andoid-Compose-Test-App)

## Full Source

~~~kotlin
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            ComposeTestAppTheme {
                Surface(color = MaterialTheme.colors.background) {
                    NewsStory()
                }
            }
        }
    }
}

@Composable
fun NewsStory() {
    Column(modifier = Modifier.padding(16.dp)) {
        Image(
            painter = painterResource(R.drawable.header),
            contentDescription = null,
            modifier = Modifier
                .height(180.dp)
                .fillMaxWidth()
                .clip(shape = RoundedCornerShape(4.dp)),
            contentScale = ContentScale.Crop
        )
        Spacer(Modifier.height(16.dp))

        Text(
            "A day wandering through the sandhills in Shark Fin Cove, and a few of the sights I saw",
            style = typography.h6
        )
        Text("Davenport, California", style = typography.body2)
        Text("December 2018", style = typography.body2)
    }

}

@Preview(showBackground = true)
@Composable
fun DefaultPreview() {
    ComposeTestAppTheme {
        NewsStory()
    }
}
~~~

위 소스를 기반으로 하나 씩 분석하도록 하겠습니다.

## @Composable

Android Compose 같은 경우 화면을 구성할 때 Composable 함수를 이용합니다. Composable 함수는 `@Composable` 어노테이션을 구성하며 Composable 함수 내부에서 0개 이상의 Composable 함수를 호출할 수 있습니다. 이렇게 Composable 함수를 재귀 호출함으로써 Composable tree 를 구성하여 화면을 구성하게 됩니다.

위 코드에서 보이는 `Text( ... )` 는 아래와 같이 미리 정의된 Composable 함수 입니다.

~~~kotlin
@Composable
fun Text(text: AnnotatedString, modifier: Modifier = Modifier, ...) {
    ...
}
~~~

또한 `@Composable` 어노테이션를 사용하여 아래와 같이 Composable 함수를 추가하고 호출이 가능합니다.

~~~kotlin
@Composable
fun NewsStory() {
 ...
}
~~~

## @Preview

