---
layout: post
title: "Part06 - 패키지 매니저와 PM2"
parent: OpenTutorials - Node.js
categories: [serverSystem, dev]
---

# [OpenTutorials] Node.js 공부 Part06 - 패키지 매니저와 PM2

## 배운 내용

🍓 PM2를 설치하여 사용할 수 있다. (지금으로서는 그렇게 유용하지 않은 것 같다..)

### 🍓 목표

- 개념 이해

### 🍓 내용

💡 프로젝트가 성장했을 때

사용하는 패키지(http, fs, ....)는 계속해서 늘어나게 될 것이다.

이 모든 패키지들을 한꺼번에 관리해 주는 '패키지매니저'라는 친구가 있다.

💡 PM2 설치 방법

    npm install pm2 -g

전역적으로 설치된다.

내 리눅스 컴퓨터 어디에서나 이 pm2를 사용할 수 있다.

💡 PM2 사용 방법

현재 자주 쓰는 기능들만 적는다.

1. 
        pm2 start main.js

    main.js를 실행한다. 코드를 수정할 때마다

        node main.js

    이걸 계속 다시 실행할 필요 없이 새로고침만 하면 된다.

2. 
        pm2 stop main.js
    
    main.js 실행했던 걸 멈춘다.

3. 
        pm2 log
    
    지금까지의 진행상황을 볼 수 있다. 이걸 통해서 오류가 발생했는지 등을 확인

[전체소스코드와 수업영상](https://opentutorials.org/course/3332/21133)

## 감상과 질문

🍓 일단 지금은.. 오류가 발생하면 바로 바로 보이는 게 더 편해서 node main.js를 더 많이 사용하게 된다.

🍓 pm2로 돌아가고 있는 상태에서 새로고침했는데 오류 코드도 안 나오고 페이지도 막 이상하지 않고 코드는 길고 이러니깐 눈에 잘 안 들어오는 느낌