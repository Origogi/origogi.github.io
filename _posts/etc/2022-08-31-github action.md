---
published: false
title: "Github Actions 정리"
excerpt : " "
layout: single
author_profile: true
read_time: false
toc: true
toc_label: "My Table of Contents"
toc_icon: "cog"
toc_sticky: true
categories :
 - etc

comments: true
sitemap :
  changefreq : daily
  priority : 1.0
---

<div align="center">
<img src="https://image.toast.com/aaaadh/real/2021/techblog/1%2818%29.png" >
</div>

# Github Actions

깃허브에서 제공하는 CI/CD 툴입니다. 이전에는 Jenkins나 CircleCI 등 외부 툴을 이용해야 했지만 2018년에 Github Actions 가 출시되면서 외부 툴 없이 Github 레포지토리에서 자체적으로 CI/CD 를 적용할수 있게 되었습니다.

## Github Actions 정리

아래 5가지 키워드가 핵심입니다.

### 1. Events

CI/CD 가 실행되는 Trigger Point 입니다.

예를 들어서

- 메인 브랜치로 머지
- 커밋을 푸쉬
- 이슈을 누군가 열었을 때

### 2. Workflows

위 이벤트가 발생되었을 때 실행 되는 작업의 집합입니다.<br>
여기서 작업은 아래 언급된 `Job` 입니다.

### 3. Job

`Workflow` 에서 수행되는 최소 실행 수행 단위입니다.<br>
하나의 `Workflow` 에 선언된 `Job` 들은 기본적으로 병렬로 수행이 되며 순차적으로 수행도 가능합니다.

#### Step

`Job` 에서 커맨드를 실행하는 단위, 쉽게 말해 터미널 명령어 한줄 입니다.

### 4. Action

재사용 가능 한 workflow로 직접 만들어 사용할수도 있고 누군가 이미 만들어 진 것을 가져와 사용할 수도 있습니다.

### 5. Runners

`Job` 을 실행시키는 Virtual Machine 입니다. 각각 Job 들은 독립적인 Runner 를 통해 실행이 됩니다.

## Reference

[드림코딩 / 제발 깃허브 액션🔥 모르는 개발자 없게해 주세요 🙏](https://www.youtube.com/watch?v=iLqGzEkusIw)