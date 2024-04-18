---
layout: post
title: MySQL 컨테이너 올릴 때 테이블도 같이 만들어 초기화하는 방법
last_modified_date: 2024-04-18
---

# Docker MySQL 컨테이너 올릴 때 테이블도 같이 만들어 초기화하는 방법
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## [아주 굉장한 설명](https://stackoverflow.com/questions/49573004/create-tables-on-docker-start)

을 보면 바로 이해가 되지만 한글로 다시 정리해보겠어요

## 목표

mysql 컨테이너를 올릴 때 `--MYSQL_DATABASE=mydb` 이런 식으로 데이터베이스를 만들고 시작하는 건 아는데, 그 안에 테이블도 만들어서 시작하는 방법을 알고 싶다.

## docker compose 파일 수정

```
version: '3'
services:
  auth_db:
    image: mysql
    restart: always
    container_name: auth_db
    env_file: ./.env_auth_db
    volumes:
      - ./auth_db_init:/docker-entrypoint-initdb.d
    restart: 'no'
```

스택오버플로우처럼 env를 직접 docker compose 파일에서 하나하나 적어 주어도 되지만 나는 파일로 분리했다.

볼륨을 연결해 주는 것을 보니 ./auth_db_init 이라는 폴더가 필요하겠다.

## SQL 파일 작성

auth_db_init 폴더 안에는 auth_db_init.sql 파일이 있는데 그 내용은 다음과 같이 만들고자하는 테이블 SQL문을 적어 주면 된다.

```sql
-- -----------------------------------------------------
-- Table `mydb`.`resident`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `mydb`.`resident` (
  `r_id` INT NOT NULL AUTO_INCREMENT,
  `r_username` VARCHAR(45) NOT NULL,
  `r_password` VARCHAR(255) NOT NULL,
  `r_phone` VARCHAR(45) NOT NULL,
  `r_realname` VARCHAR(15) NOT NULL,
  `r_email` VARCHAR(45) NOT NULL,
  `r_point` INT NOT NULL DEFAULT 0,
  `r_birth` DATE NULL,
  `created_time` TIMESTAMP NULL DEFAULT CURRENT_TIMESTAMP,
  `updated_time` TIMESTAMP NULL DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (`r_id`),
  UNIQUE INDEX `r_phone_UNIQUE` (`r_phone` ASC) VISIBLE,
  UNIQUE INDEX `r_username_UNIQUE` (`r_username` ASC) VISIBLE,
  UNIQUE INDEX `r_id_UNIQUE` (`r_id` ASC) VISIBLE)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `mydb`.`agentList`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `mydb`.`agentList` (
  `ra_regno` VARCHAR(30) NOT NULL,
  `rdealer_nm` VARCHAR(45) NOT NULL,
  `cmp_nm` VARCHAR(45) NOT NULL,
  `telno` VARCHAR(140) NOT NULL,
  `address` VARCHAR(70) NOT NULL,
  `sgg_nm` VARCHAR(45) NOT NULL,
  `bjdong_nm` VARCHAR(45) NOT NULL,
  PRIMARY KEY (`ra_regno`),
  UNIQUE INDEX `ra_regno_UNIQUE` (`ra_regno` ASC) VISIBLE)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `mydb`.`agent`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `mydb`.`agent` (
  `a_id` INT NOT NULL AUTO_INCREMENT,
  `agentList_ra_regno` VARCHAR(30) NOT NULL,
  `a_username` VARCHAR(45) NOT NULL,
  `a_password` VARCHAR(255) NOT NULL,
  `a_realname` VARCHAR(45) NOT NULL,
  `a_email` VARCHAR(45) NOT NULL,
  `a_auth` TINYINT NOT NULL DEFAULT 0,
  `a_auth_image` VARCHAR(200) NULL,
  `a_profile_image` VARCHAR(200) NULL,
  `a_introduction` VARCHAR(45) NULL,
  `a_office_hours` VARCHAR(45) NULL,
  `a_image1` VARCHAR(200) NULL,
  `a_image2` VARCHAR(200) NULL,
  `a_image3` VARCHAR(200) NULL,
  `created_time` TIMESTAMP NULL DEFAULT CURRENT_TIMESTAMP,
  `updated_time` TIMESTAMP NULL DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (`a_id`, `agentList_ra_regno`),
  UNIQUE INDEX `idrealtor_UNIQUE` (`a_id` ASC) VISIBLE,
  UNIQUE INDEX `a_email_UNIQUE` (`a_email` ASC) VISIBLE,
  INDEX `fk_agent_agentList1_idx` (`agentList_ra_regno` ASC) VISIBLE,
  CONSTRAINT `fk_agent_agentList1`
    FOREIGN KEY (`agentList_ra_regno`)
    REFERENCES `mydb`.`agentList` (`ra_regno`)
    ON DELETE CASCADE
    ON UPDATE CASCADE)
ENGINE = InnoDB;

-- -----------------------------------------------------
-- Table `mydb`.`agent_contact`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `mydb`.`agent_contact` (
  `contact_number` VARCHAR(20) NOT NULL,
  `agent_agentList_ra_regno` VARCHAR(30) NOT NULL,
  PRIMARY KEY (`contact_number`, `agent_agentList_ra_regno`),
  UNIQUE INDEX `contact_number_UNIQUE` (`contact_number` ASC) VISIBLE,
  INDEX `fk_agent_contact_agentList1_idx` (`agent_agentList_ra_regno` ASC) VISIBLE,
  CONSTRAINT `fk_agent_contact_agentList1`
    FOREIGN KEY (`agent_agentList_ra_regno`)
    REFERENCES `mydb`.`agentList` (`ra_regno`)
    ON DELETE CASCADE
    ON UPDATE CASCADE)
ENGINE = InnoDB;

CREATE TABLE IF NOT EXISTS `mydb`.`certification` (
    `code` INT(11) NOT NULL,
    `email` VARCHAR(255) NOT NULL,
    CONSTRAINT PK_Certification PRIMARY KEY (code, email)
);

SET SQL_MODE=@OLD_SQL_MODE;
SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS;
SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS;
```

## DB 생성 확인

`docker compose up --build`로 컨테이너를 올리고 auth_db에 접속하여 테이블이 잘 생성되었는지 확인

![](/attachment/2024/04/18/mysqltable.png)

## 마이크로서비스로 활용하는 방법

비즈니스로직을 담당하는 다른 서비스에서는 auth_db를 host name으로 사용하고 포트 번호는 3306으로, db이름은 mydb로하여 연결을 진행하고 SQL문은 동일하게 날려 주면 된다.

기존에는 다른 서버에 연결되어 있던 것을 분리한 데이터베이스 컨테이너로 연결해준 것이라고 생각하면 되겠다.