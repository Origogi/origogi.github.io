---
published: true
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
<img src="https://thdev.tech/images/posts/2020/10/Android-Jetpack-Compose-Basics/image.png" width="70%" >
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

Android Compose 는 화면을 구성할 때 Composable 함수를 이용합니다. Composable 함수는 `@Composable` 어노테이션을 선언함으로써 구성하게 됩니다.

위 코드에서 보이는 `Text( ... )` 는 아래와 같이 미리 정의된 Composable 입니다.

~~~kotlin
@Composable
fun Text(text: AnnotatedString, modifier: Modifier = Modifier, ...) {
    ...
}
~~~

또한 `@Composable` 어노테이션를 사용하여 아래와 같이 Composable을 추가하고 호출이 가능합니다.

~~~kotlin
@Composable
fun NewsStory() {
 ...
}
~~~

특정 Composable 은 자식 Composable를 가질수 있으며 자식 Composable 는 또 자식 Composable를 가짐으로써 Composable tree를 구성하게 됩니다. 이는 Flutter의 Widget tree와 매우 유사합니다.

위 코드를 기준으로 Composable Tree는 아래와 같이 구성이 됩니다.

<div align="center">
<img src="https://user-images.githubusercontent.com/35194820/120459270-ff609400-c3d2-11eb-802a-37e525a63f4a.png
" width="50%">
</div>

## @Preview

Android Compose의 특징 중 하나는 앱을 실행하지 않아도 Preview 화면을 볼 수가 있습니다. Composable 함수에 `@Preview` 어노테이션을 추가함으로써 원하는 Composable 의 Preview 화면를 아래와 같이 볼수가 있게 됩니다.

<div align="center">
<img src="https://user-images.githubusercontent.com/35194820/120452146-f7055a80-c3cc-11eb-9805-dca7690bf261.png" width="70%" >
</div>

이것은 Android XML layout 과 비슷하나 코드를 수정 후 최신 버전을 보기 위해서는 반드시 빌드를 수행해야하는 번거로움이 있습니다.

## setContent

액티비티의 setContentsView() 의 역할을 한다고 보면 됩니다. 실제 내부적으로 setContentsView() 를 호출을 하고 있으며 Composable 을 액티비티에 보여주는 역할을 합니다.

## ComposeTestAppTheme

Project 명에 따라 자동으로 생성되는 Composable 입니다.

~~~koltin
@Composable
fun ComposeTestAppTheme(darkTheme: Boolean = isSystemInDarkTheme(), content: @Composable() () -> Unit) {
    val colors = if (darkTheme) {
        DarkColorPalette
    } else {
        LightColorPalette
    }

    MaterialTheme(
            colors = colors,
            typography = Typography,
            shapes = Shapes,
            content = content
    )
}
~~~

여기서 중요한 것은 `MaterialTheme(...)` 로써 기본적인 Material 스타일의 화면을 구성을 도와주기 위한 Composable 함수입니다. Flutter의 `Scaffold` 와 비슷한 역할을합니다. 

`MaterialTheme` 는 자식 Composable을 가질수 있습니다.

## Surface

빈 껍대기의 Composable입니다. `Surface` 는 백그라운드 색이나 테두리 색을 지정할수 있습니다. Flutter의 `Container` 와 유사합니다.

`Surface` 는 자식 Composable을 가질수 있습니다.

## Column

`Column` 는 여러 개의 자식 Composable 를 세로 방향으로 정렬하는 Composable 입니다. 세로 방향의 Linear layout과 유사한 역할을 합니다.

## Image

`Image` 는 로컬에 저장된 이미지 파일을 보여주기 위한 Composable 입니다. 이미지의 scale type 및 사이즈, 라운드 테두리 처리를 할 수 있습니다.

## Space

빈 껍대기의 Composable입니다. Composable 사이의 공간을 띄우기 위해 사용이 됩니다. 보통 `Column` 안에서 사용이 됩니다.

## Text

텍스트를 표시하기 위한 Composable입니다. 폰트 스타일, 색, 사이즈를 지정할 수 있습니다.

## 정리

Android Compose 는 선언적 UI 패턴으로 화면을 구성하게 됩니다. 화면을 구성할 때 layout xml를 사용하지 않고 오직 kotlin으로만 사용합니다.
또한 Composable 단위로 화면의 일부분을 각각 구성하며 여러개의 Composable 를 조합하여 최종적으로 하나의 화면을 구성하게 됩니다.

## Reference

[TUTORIAL Jetpack Compose Basics](https://developer.android.com/jetpack/compose/tutorial?continue=https%3A%2F%2Fdeveloper.android.com%2Fcourses%2Fpathways%2Fcompose%23article-https%3A%2F%2Fdeveloper.android.com%2Fjetpack%2Fcompose%2Ftutorial)
