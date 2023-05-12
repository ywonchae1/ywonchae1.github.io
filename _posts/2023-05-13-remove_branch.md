---
layout: post
title: "[github] 로컬에서 브랜치 생성, 이동, 수정, 삭제하기"
categories: [dev]
---

## 생성

```
git branch [브랜치 이름]
git push -u origin [브랜치 이름]
```

로컬에서 브랜치를 생성하고, 이를 리포지토리에 반영한다.

## 로컬 연결된 브랜치 변경

```
git checkout [연결할 브랜치 이름]
```

## 생성, 변경 동시에

```
git checkout -b [브랜치 이름]
```

## 수정

```
git branch -m [브랜치의 새로운 이름]
```

## 삭제

기능 개발이 끝났고 main에 합쳐서 더이상 사용하지 않는 브랜치를 삭제한다.

```
git branch
```

현재 브랜치를 확인한다.

```
git pull father
```

로컬 코드와 원본 저장소의 코드를 동기화한다.

```
git branch -d [브랜치 이름]
```

브랜치를 삭제한다.

```
error: Cannot delete branch '[브랜치 이름]' checked out at ...
```

연결된 브랜치는 삭제할 수 없다. 위와 같은 오류가 발생한다.

checkout으로 다른 브랜치로 변경해 주어야 한다.

여기까지는 로컬에서 브랜치를 제거해 준 것이다.

리포지토리에서 브랜치를 삭제하려면 다음 명령을 사용한다.

```
git push -u origin --delete [브랜치 이름]
```