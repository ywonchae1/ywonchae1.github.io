---
layout: post
title: "[MySQL] MySQL 계정 생성, 삭제, 비밀번호 변경, 비밀번호 분실"
categories: [dev]
---

## 으아아악

너무 많이 찾아봐서 정리..

## 계정 생성

중요! MySQL의 root 계정으로 로그인!!

```
mysql> SHOW databases; # 데이터베이스 확인
+--------------------+
| Database           |
+--------------------+
| dcdb               |
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.00 sec)
mysql> USE mysql;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> SHOW tables;  # mysql 데이터베이스의 테이블 확인
+------------------------------------------------------+
| Tables_in_mysql                                      |
+------------------------------------------------------+
| columns_priv                                         |
| component                                            |
| db                                                   |
| default_roles                                        |
...
| time_zone_transition_type                            |
| user                                                 |
+------------------------------------------------------+
37 rows in set (0.00 sec)
```

준비가 되었다. 계정을 생성

test라는 이름의 localhost 계정을 test라는 비밀번호로 생성한다.

외부에서 DB서버 접속이 필요하면 'test'@'%'로 localhost를 %로 변경해 주어야 한다.

```
mysql> CREATE USER 'test'@'localhost' IDENTIFIED BY 'test';
Query OK, 0 rows affected (0.02 sec)
mysql> SELECT * FROM user where User='test'; # 해당 계정이 잘 생겼는지 확인할 수 있다.
```

## 계정 삭제

test 계정을 삭제한다.

```
mysql> DELETE FROM user WHERE User='test';
```

## 계정 비밀번호 변경

test 계정의 비밀번호를 testdiff로 변경한다.

test@%로 계정을 생성했다면 'test'@'localhost'대신 'test'@'%'로 적어 주어야 한다.

```
mysql> ALTER user 'test'@'localhost' IDENTIFIED WITH mysql_native_password BY 'testdiff';
```

## 계정 비밀번호 분실 시

모든 것을 잊어 아예 MySQL 접속이 안 되는 경우

Ubuntu 기준

MySQL 서버가 아닌 Shell에 위치한 상태여야 한다.

```
$ sudo service mysql stop
$ sudo /usr/bin/mysqld_safe --skip-grant-tables &
```

여기서 오류가 발생한다면 권한 설정

```
$ sudo mkdir -p /var/run/mysqld
$ sudo chown -R mysql:mysql /var/run/mysqld
```

권한 설정 후 다시 데몬 실행

```
$ sudo /usr/bin/mysqld_safe --skip-grant-tables &
```

이러면 백그라운드에서 이 프로세스가 돌아간다. 이 상태에서 다음 명령을 실행하면 비밀번호 없이 MySQL 접속이 가능하다.

```
$ mysql -u root mysql
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 36
Server version: 8.0.33-0ubuntu0.20.04.2 (Ubuntu)

Copyright (c) 2000, 2023, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> UPDATE mysql.user SET authentication_string=PASSWORD('새로운 비밀번호') WHERE user='root';

mysql> flush privileges;

mysql> exit

$ sudo service mysql restart
```

MySQL에서 위 명령어들이 오류 없이 잘 진행되었다면 service mysql restart로 재시작해준다.

완료..

```
$ jobs
[3]+ ....
$ kill -9 %3
```

위 명령어로 백그라운드에서 실행 중인 프로세스를 확인하고, 아까 실행한 데몬이 안 죽었다면 죽인다.

## 비밀번호 설정이 안 되는 경우

```
ERROR 1819 (HY000): Your password does not satisfy the current policy requirements
```

새로운 비밀번호를 update하려는데 위와 같은 오류가 발생하는 경우

접속해 있는 MySQL 서버에서 다음 명령을 실행한다.

```
mysql> SHOW VARIABLES LIKE 'validate_password%'
```

표 해석

vaildate_password_length: 비밀번호 길이 관련
vaildate_password_policy: 비밀번호 강력함 정도(?)

```
mysql> SET GLOBAL validate_password_policy=LOW;
```

MIDIUM, HIGH도 가능

설정하고, 다시 계정 생성 또는 비밀번호 변경 시도

## 계정에 DB 권한 주기

```
mysql> GRANT ALL ON [database이름].[table이름] TO [user이름]@[server이름];
```

권한을 주지 않으면 웹 서버가 mysql 패키지로 접근이 불가능해서 이런 오류가 생긴다.

```
Error: Access denied for user 'root'@'localhost'
code: 'ER_ACCESS_DENIED_NO_PASSWORD_ERROR', 
errno: 1698, 
sqlState: '28000', 
sqlMessage: "Access denied for user 'root'@'localhost'", 
sql: undefined
```

## 계정 로그인

```
$ sudo mysql -u test -p
Enter password: test
```