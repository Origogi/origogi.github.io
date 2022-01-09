---
published: true
title: "[React] Debounce, Throttle"
excerpt : " "
layout: single
author_profile: true
read_time: false # read_time을 출력할지 여부 1min read 같은것!
toc: true #Table Of Contents 목차 보여줌
toc_label: "My Table of Contents" # toc 이름 정의
toc_icon: "cog" #font Awesome아이콘으로 toc 아이콘 설정
toc_sticky: true # 스크롤 내릴때 같이 내려가는 목차
categories :
 - React
tags: 
  - React
comments: true
header:
  overlay_image: assets/images/flutter_dart.png
  overlay_filter: 0.5
  teaser: https://venturebeat.com/wp-content/uploads/2019/09/google-dart-flutter.png?w=578&strip=all
sitemap :
  changefreq : daily
  priority : 1.0
---

## 디바운스와 쓰로틀

디바운스와 쓰로틀은 `지연 처리` 를 효율적으로 처리하기 위한 알고리즘 혹은 기법입니다. 두 개념은 서버의 데이터를 요청하거나 UI 이벤트를 처리하는 과정에서 작업에서 생기는 부하를 크게 줄여주기에 꼭 알아두는 것이 좋습니다.

### 디바운스

디바운스(Debounce) 는 어떤 내용을 입력하다가 특정 시간 동안 대기하고 있으면 마지막에 입력된 내용을 바탕으로 서버 요청을 하는 벙법입니다. 즉 쉽게 말해 짧은 시간(특정 시간) 동안 이벤트가 계속 발생하면 그중 마지막 이벤트를 처리하는 것입니다. 예를 들어 네이버나 구글 검색창에 내용을 입력할 때는 검색창 하단에 아무 내용도 나오지 않다가 입력을 멈추면 검색창 하단에 연관 검색어 목록이 나타납니다. 이것이 디바운스를 적용한 것입니다.

<div align="center">
   <img src="https://t1.daumcdn.net/cfile/tistory/99C4B6335B33302822"/>
</div>

#### Code

~~~javascript
function debounce(func, delay) {
    let inDebounce
    return function(...args) {
        if (inDebounce) {
            clearTimeout(inDebounce)
        }
        inDebounce = setTimeout(() => func(...args), delay)
    }
}


const run = debounce(val => console.log(val), 2000)

run('a')
run('b')
run(2) // 2초 이후 2 를 출력
~~~

### 쓰로틀

쓰로틀(Throttle) 은 디바운스 개념과 비슷하지만 `입력되는 동안에도 바로 이전에 요청한 작업을 주기적으로 실행한다는 점` 이 다릅니다. 쉽게 말해 이벤트를 일정 주기마다 한 번씩만 발생하도록 해줍니다. 이벤트가 한 번 실행이 되면 설정한 시간이 자니고 나서야 다음 이벤트를 실행하며 특정 시간 동안 입력된 모든 이벤트들은 마지막 이벤트를 제외한 나머지는 무시하게 됩니다.

 쓰로틀이 적용된 예를 들면 페이스북의 타임라인은 스크롤을 내리는 동안 계속해서 다음 내용이 출력되는 일명 `무한 스크롤` 기능이 구현되어 있습니다.

<div align="center">
   <img src="https://blog.kakaocdn.net/dn/cAAR95/btqB1dhwq7n/YDGBzhgPsxnrKWdKYbMX2K/img.gif"/>
</div>

만약 사용자가 지연 시간 동안 스크롤을 계속 움직이면 여러번의 서버 요청이 발생합니다. 쓰로틀은 첫 번째 요청이 지연 실행되는 동안에는 중복된 요청을 무시하게 됩니다.

#### Code

~~~javascript
function throttle(func, delay) {
  let lastFunc;
  let lastRan;

  return function (...args) {
    const context = this;
    if (!lastRan) {
      func.call(context, ...args);
      lastRan = Date.now();
    } else {
      if (lastFunc) {
        clearTimeout(lastFunc);
      }
      lastFunc = setTimeout(function() {

        if (Date.now() - lastRan >= delay) {
            func.call(context, ...args);
            lastRan = Date.now();
        }

      }, delay - (Date.now() - lastRan));
    }
  };
}


const run = throttle(val => console.log(val), 2000)

run('a')  // a를 바로 출력
run('b')   // b를 무시
run(2) // 2초 이후 2 를 출력
~~~