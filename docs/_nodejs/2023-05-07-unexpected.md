---
layout: post
title: "[Node.js] heap; syntaxerror: unexpected token ; 해결"
categories: [dev]
---

## 문제

```
node main.js
```

서버를 가동 시켰더니 에러가 발생했다.

```
heap ;
     ^
Syntaxerror: unexpected token ;
...
```

node_modules를 지워보고 package.js, packages-lock.json까지 다 지워봤지만 소용이 없었다.

## 해결

```
node -v
```

내 버전은 10.얼마얼마였다.

업데이트를 하고 터미널을 껏다가 다시 켜니까 잘 작동하였다.

```
npm cache clean -f
npm install -g n
n lts
```

<img src='/attachment/230507/Screenshot_nodev.png'>

<img src='/attachment/230507/Screenshot_nodemain.png'>