---
layout: post
title: "[OpenTutorials] Node.js 공부 Part12 - 파일을 나누자! 모듈 / 보안 / API"
categories: [serverSystem]
---

## 배운 내용

🐯 코드가 너무 길어지지 않게 모듈로 나누어서 사용할 수 있다.

🐯 나누어진 모듈을 불러올 수 있다.

🐯 exports에 대해 이해할 수 있다.

🐯 보안상 이슈를 이해할 수 있다.

🐯 API가 뜻하는 바를 파악할 수 있다.

### 🐯 개념

- module.exports =...

- http = require('http');

- 쿼리스트링으로 서버의 파일 시스템을 탐색하는 위험한 상황

- API?

### 🐯 이해

💡 exports 개념을 수업 들을 땐 이해를 못했는데 이제 이해가 완전 잘 된다.

```js
//mport.js
module.exports = {
    v1:'v1',
    v2:'v2',
    f1:function() {
        console.log(o.v1);
    }
    f2:function() {
        console.log(o.v2);
    }
}
```

mport.js라는 파일이 있다고 하자.

```js
//muse.js
m = require(mport);

console.log(m.v1);
m.f1();
```

muse.js라는 파일이 있다면 이렇게 mport.js 파일을 사용할 수 있다.

C++와 파이썬의 import와 동일하다.

💡 쿼리스트링의 위험성

    /id="../../user01/password.txt"

이렇게 쿼리스트링을 줘 버리면 상위 디렉토리를 여기 저기 파고 들어갈 수 있다. 그게 바로 화면에 뿌려진다면...?

    var path = require('path');

path라는 모듈을 사용한다.

    path.parse(queryData.id).base

이러면 앞의 ../../이 무시된다.

💡 API

우리가 만들었던 app, 즉 createServer(...) 이 부분에 대한 이야기이다.

인터페이스를 사용한다는 것이고 약속을 지키는 것이라고 ...

일단 뭐였는지도 몰랐던 API였지만 대충 감은 잡힌 것 같다.

[전체소스코드와 수업영상](https://opentutorials.org/module/3549/21153)

## 감상과 질문

🐯 화이팅