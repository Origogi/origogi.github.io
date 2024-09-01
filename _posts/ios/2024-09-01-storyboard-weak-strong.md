---
published: false
title: "[iOS][Swift] Storyboard의 Outlet은 weak 가 맞을까 strong 이 맞을까?"	
excerpt : " "	
layout: single	
classes: wide
author_profile: true
read_time: false
toc: true
toc_label: "My Table of Contents"
toc_icon: "cog"
toc_sticky: true
categories :	
 - iOS	
tags: 	
  - iOS
  - Swift

comments: true	
header:
  overlay_image: https://github.com/user-attachments/assets/9b9bbd57-859f-473b-81b2-178a9d7d69e6
  overlay_filter: 0.4
  teaser: https://github.com/user-attachments/assets/9b9bbd57-859f-473b-81b2-178a9d7d69e6
sitemap :	
  changefreq : daily	
  priority : 1.0	
---

## 서론

현재 iOS 개발 공부를 하면서 Storyboard를 이용하여 View를 생성하는 방법을 배우고 있습니다. 그런데 Storyboard를 이용하여 View를 생성할 때 IBOutlet을 선언할 때 weak로 선언하는 것이 맞는지 strong으로 선언하는 것이 맞는지 궁금하여 이에 대해 알아보았습니다.

참고로 weak, 또는 strong reference, ARC 에 대한 개념은 아래 포스트를 참고하시면 됩니다.

- [ARC (Automatic Reference Counting) 정리](https://origogi.github.io/ios/ARC/)


## Reference

[야곰넷-스토리보드와 인터페이스 빌더를 이용한 뷰 생성시 질문 있습니다.](https://yagom.net/forums/topic/%EC%8A%A4%ED%86%A0%EB%A6%AC%EB%B3%B4%EB%93%9C%EC%99%80-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4-%EB%B9%8C%EB%8D%94%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EB%B7%B0-%EC%83%9D%EC%84%B1%EC%8B%9C/)