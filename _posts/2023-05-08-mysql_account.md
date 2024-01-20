---
layout: post
title: "[MySQL] 계정관리 / 비밀번호 확인 및 변경 방법"
categories: [dev]
page: DEV
---

## root 계정으로 mysql 실행

```
$ mysql -u root -p
Enter password:
```

## mysql 데이터베이스의 user 테이블

계정 정보를 찾을 수 있다.

```
mysql> use mysql;
mysql> SELECT host, user, authentication_string FROM user;
```

mysql 버전이 높아지면서 authentication_string에 비밀번호가 암호화되어 들어가 있다.

## 비밀번호 변경

```
mysql> ALTER user 'root'@'localhost' identified with mysql_navite_password by 'password';
mysql> ALTER user 'root'@'%' identified with mysql_navite_password by 'password';
```

원격 접속이 가능하게 localhost가 아니라 %로 했다면 얘도 마찬가지로 '[계정이름]'@'%'로 적어 주어야 한다.

## 변경사항 적용

```
mysql> flush privileges;
mysql> quit
```

사실 이걸 안 해주고 그냥 quit을 해도 되긴 함..

안전하게 flush를 해준다.