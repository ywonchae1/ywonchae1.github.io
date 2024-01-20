---
layout: post
title: "[RaspberryPi] ssh 계정이름@hostname 변경하기"
categories: [dev]
page: DEV
---

## 라즈베리파이 이름 변경

```
ssh 계정이름@호스트이름
```

라즈베리파이에 원격 접속을 할 때 사용하는 명령어다.

여러 라즈베리파이가 하나의 네트워크 안에서 호스트네임이 동일할 때는 오류가 발생하기 쉽다.

이럴 때는 IP 주소로 접속을 하는 방법이 있지만 호스트네임을 바꾸면 더 깔끔하다.

1. HDMI 선과 모니터를 사용하여 라즈베리파이에 모니터를 연결한다.

2. 터미널을 연다.

3. 
    ```
    sudo su -

    raspi-config
    ```

    를 터미널에 입력한다.

4. 그렇게 뜬 창에서 다음 과정을 밟아 이름을 변경할 수 있다.
    ```
    system options

    Hostname
    ```