---
layout: post
title: "[OpenTutorials] Django 시작! 8000번 포트 접근 안 될 때"
categories: [pirogramming]
page: DEV
---

## 🚜 Django 애플리케이션 만들기

1. 설치 `sudo python3 -m pip install django`

2. 폴더 생성

3. django-admin 활용함. 이 명령어로 메뉴얼 확인 가능

4. `django-admin startproject myproject .` myproject라는 이름으로 프로젝트 폴더를 현재 디렉토리에 생성

    <img src="../attachment/230621/Capture5.PNG">

5. `python3 manage.py runserver` 서버 실행

    `python3 manage.py runserver 8080` 포트번호 지정 가능

6. localhost:8000에서 귀여운 로켓 확인

## 🚜 ERROR LOG

<img src="../attachment/230621/Capture.PNG">

```
AttributeError: module 'myapp.views' has no attribute 'index'
```

참고

<https://stackoverflow.com/questions/17880146/django-module-object-has-no-attribute-index>

<img src="../attachment/230621/Capture4.PNG">

<img src="../attachment/230621/Capture2.PNG">

<img src="../attachment/230621/Capture3.PNG">

myapp/views.py에서 `from . import urls` 추가하고 잘 돌아가는 것을 확인

## 하지만 모든 url을 잘 만들어 두면 문제가 안 생긴다..

<img src="../attachment/230621/Capture6.PNG">

## 🚜 Django의 간편함

Node.js와 달리 create와 create_process에 대한 url을 따로 두지 않고 create 함수 안에서 get과 post를 나누어 처리할 수 있다.

- 지금은 views에 함수들을 때려박아 두었는데 template engine을 사용하여 html 코드를 파이썬 코드와 분리할 수 있다.

- 데이터베이스와 함께 사용할 수 있다. 현재 코드는 창을 끄고 재접속하면 생성했던 데이터가 모두 날아가는 구조이다.

<img src="../attachment/230621/Capture7.PNG">