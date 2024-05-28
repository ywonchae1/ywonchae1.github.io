---
layout: post
title: "[Docker] Dockerfile 작성하고 이미지 빌드하기"
tags: Server
---

## Dockerfile 작성 문법

<img src='/attachment/2024/01/21/12dockerfile_example.png'>
<img src='/attachment/2024/01/21/20dockerfile_example.png'>
<img src='/attachment/2024/01/21/21dockerfile_example.png'>
예시 세가지! 배포할 떄 사용하는 명령어를 dockerfile 문법에 맞게 작성하면 된다.

1. 이미지로 빌드하고 싶은 프로젝트 디렉토리의 최상단에 `dockerfile` 또는 `Dockerfile`이라는 이름으로 파일을 생성
2. 파일 내용을 문법에 맞게 구성

파일 이름을 꼭 dockerfile로 할 필요는 없지만, build 시 기본적으로 해당 이름으로 파일을 찾기 때문에 다른 이름으로 만들었을 경우 옵션으로 따로 지정해 선택해 주어야 함.

<img src='/attachment/2024/01/21/14dockerfile_cheatsheet.jpeg'>

여기서 주의할 점은, EXPOSE를 한다고 해서 호스트와 바인딩이 자동으로 되는 것은 아니므로 run 할 때 바인딩이 필요하다.

## Docker image build

```
docker image build -t webapp:8.0 .
docker image build -t webapp:8.0 -f ./dockerfiles/Dockerfile .
```

위와 같은 형식으로 이미지를 빌드할 수 있다.

dockerhub에 이미지를 업로드 할 것이라면, 자신의 레포지토리를 이미지명 앞에 붙여주어야 한다.

<img src='/attachment/2024/01/21/15dockerimagebuild.png'>
<img src='/attachment/2024/01/21/16dockerimagebuild.png'>

이렇게 만들어진 이미지를 가지고 컨테이너를 생성할 수 있다.

<img src='/attachment/2024/01/21/17dockerrun.png'>

`-d` 옵션 : 백그라운드에서 컨테이너를 실행

`-p` 옵션 : host 포트:container 포트를 연결

`--name` 옵션 : 컨테이너의 이름 지정

<img src='/attachment/2024/01/21/18localhost.png'>

로컬에서 늘상 실행하던 대로 접속이 가능하다.

동일한 방식으로 클라우드 서버에서 배포가 진행되므로, 동일하게 로컬에서 할 수 있다는 것은

로컬에서 컨테이너를 띄워 배포 환경에서의 배포를 테스트할 수 있다는 뜻과 같다

> **Docker를 사용하면 개발 환경과 운영 환경의 차이를 줄일 수 있다!**
>
불변 실행 환경