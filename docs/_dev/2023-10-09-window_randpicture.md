---
layout: post
title: "[윈도우 명화 찾기] 윈도우 바탕화면 사진 찾아내기"
categories: [writing]
image: '../attachment/231009-1/01.png'
---

갑자기 노트북 화면 이미지에 사로잡힐 때..

1. 파일 탐색기를 열어 주소창에 아래 경로를 입력하여 이동

  <img src='/attachment/231009-1/01.png'>

  ```
  %UserProfile%\AppData\Local\Packages\Microsoft.Windows.ContentDeliveryManager_cw5n1h2txyewy\LocalState\Assets
  ```

  <img src='/attachment/231009-1/02.PNG'>

2. 해당 경로의 모든 사진을 복사하여 특정 폴더에 붙여넣기

  <img src='/attachment/231009-1/03.png'>

  <img src='/attachment/231009-1/04.PNG'>

3. powershell을 관리자 권한으로 열고 방금 새로 만든 폴더로 이동한 후 다음 명령을 실행

  ```
  cd '폴더 경로'
  cmd /c "ren *.* *.jpg"
  ```

  <img src='/attachment/231009-1/05.png'>

  <img src='/attachment/231009-1/06.PNG'>

  경로가 안 맞으면 빨간색 오류가 보임
  
  경로에 띄어쓰기가 있는 경우 따옴표로 묶어주기

4. 폴더로 돌아오면 사진이 보임

  <img src='/attachment/231009-1/07.PNG'>

  <img src='/attachment/231009-1/dfee429bddf5c868ec8da69e9c71dbb2cd813bfe9928723f5e386d880db19177.jpg'>

  찾았따 .

  <img src='/attachment/231009-1/윈도우명화.PNG'>