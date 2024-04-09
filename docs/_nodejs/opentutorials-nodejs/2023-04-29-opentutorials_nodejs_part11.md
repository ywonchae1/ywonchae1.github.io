---
layout: post
title: "Part11 - 함수가 값이 될 수 있는 JavaScript, 객체"
parent: OpenTutorials - Node.js
categories: [serverSystem, dev]
---

# [OpenTutorials] Node.js 공부 Part11 - 함수가 값이 될 수 있는 JavaScript, 객체

## 배운 내용

🚜 JavaScript 심화 개념

### 🚜 개념

- JavaScript에서 함수는 값이다.

- 지저분한 코드는 객체를 활용하여 관련있는 정보끼리 묶어 정리한다.

### 🚜 이해

💡 변수에 대입할 수 있다면 그것은 값이다.

"함수가 값이다"라는 개념을 이해하는 데 정말 중요한 문장인 것 같다.

어떻게 이렇게 설명을 잘 하시지....!

```js
function foo() {
    console.log(3);
}

var f = function() {
    console.log(3);
}

var arr[f];

foo(); //3
f(); //3
arr[0](); //3
console.log(arr[0]); //[Function: f]
console.log(f); //[Function: f]
```

JavaScript에서 함수는 변수에 담을 수 있다. 그러니 값이다.

💡 관련 있는 정보들은 객체에 모아 두자.

```js
var v1 = 'v1';
//....
var v2 = 'v2';

function() {
    console.log(v1);
}
```

서로 연관이 있는 경우 이렇게 따로 따로 떨어져 있으면 중간 중간 긴 코드가 되었을 때 헷갈리고 난처해질 수 있다.

```js
var o = {
    v1:'v1',
    v2:'v2',
    f1:function() {
        console.log(o.v1);
    }
    f2:function() {
        console.log(o.v2);
    }
}

o.f1();
```

💡 o라는 객체의 이름이 바뀌면? 객체 안쪽에도 수정해야 할 부분이 생기게 된다.

```js
var o = {
    v1:'v1',
    v2:'v2',
    f1:function() {
        console.log(this.v1);
    }
    f2:function() {
        console.log(this.v2);
    }
}

o.f1();
```

this 키워드를 사용한다.

[전체소스코드와 수업영상](https://opentutorials.org/module/3549/21146)

## 감상과 질문

🚜 객체 안에서 arrow function은 this 키워드가 원하는 대로 동작하지 않는다.

🚜 arrow function은 this가 global object를 가리켜서, 자기 자신의 값을 볼 수 없다는데... 잘 이해가 안 된다.