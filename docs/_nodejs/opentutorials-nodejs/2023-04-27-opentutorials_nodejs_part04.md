---
layout: post
title: "Part04 - 저장된 파일에 따라 동적으로 변경되는 글 목록 출력하기, 중복되는 부분을 함수로 정리하기"
parent: OpenTutorials - Node.js
categories: [serverSystem, dev]
---

# [OpenTutorials] Node.js 공부 Part04 - 저장된 파일에 따라 동적으로 변경되는 글 목록 출력하기, 중복되는 부분을 함수로 정리하기 / 반복문과 함수의 활용

## 배운 내용

💊 data 폴더에 들어있는 파일의 이름이 바뀌는 대로 글의 목록도 바뀌게 만들 수 있다.

💊 반복문을 사용한 코드를 만들 수 있다.

<img src='/attachment/230427/Capture9.PNG'>

<img src='/attachment/230427/Capture8.PNG'>

Nodejs가 추가되자 반영된 것을 확인할 수 있다.

### 💊 목표

- ```html
    <ul>
        <li><a href="/?id=HTML">HTML</a></li>
        <li><a href="/?id=CSS">CSS</a></li>
        <li><a href="/?id=JavaScript">JavaScript</a></li>
    </ul>
    ```
    이 부분을 수정한다.

- 이 상태로라면 파일이 추가되어도 목록은 딱 저 세개 밖에 표시되지 않는다.

- 페이지의 데이터가 들어있는 디렉토리의 파일 리스트를 불러와 해당 부분 HTML 코드가 고정되어 있는 것이 아닌 동적으로 변화할 수 있도록 만들어 준다.

### 💊 방법

💡 while문을 활용한다.

```js
var myhttp = require('http');
var myfs = require('fs');
var myurl = require('url');

var app = myhttp.createServer(function(request,response){
    var _url = request.url;
    var queryData = myurl.parse(_url, true).query;
    var mypathname = myurl.parse(_url, true).pathname;

    if(mypathname === '/'){
        if(queryData.id === undefined){
            var title = 'Welcome';
            var description = 'Hello, Node.js';
            ...${title}...
            ...${description}...
        } else {
            myfs.readFile(`data/${queryData.id}`, 'utf8', function(err, description){
                ...
            });
        }
        ...
    } else {
       response.writeHead(404);
       response.end('Not found');
    }
});
```

지난 글의 마지막 코드이다.

```js
if(mypathname === '/'){
        if(queryData.id === undefined){
            //요기
            var title = 'Welcome';
            var description = 'Hello, Node.js';
            ...${title}...
            ...${description}...
        }
        ...
}
```

코드가 너무 길기 때문에 이 부분 중 주석 부분에 넣을 코드에만 집중한다.

```js
fs.readdir('./data', function(error, filelist){
    var title = 'Welcome';
    var description = 'Hello, Node.js';
    var list = '<ul>';
    var i = 0;
    while(i < filelist.length){
        list = list + `<li><a href="/?id=${filelist[i]}">${filelist[i]}</a></li>`;
        i = i + 1;
    }
    list = list+'</ul>';
    ...
    ...${list}...
});
```

### 💡 readdir 함수가 새롭게 나온다

디렉터리를 읽어, function의 두번째 parameter인 filelist에 '리스트'형태로 저장이 되어 있다.

이 리스트를 순회하며 다음 코드를 만든다고 생각하면 된다.

```html
<ul>
    <li><a href="/?id=HTML">HTML</a></li>
    <li><a href="/?id=CSS">CSS</a></li>
    <li><a href="/?id=JavaScript">JavaScript</a></li>
</ul>
```

[전체소스코드와 수업영상](https://opentutorials.org/module/3549/21123)

### 💊 함수로 정리하기

중복되는 부분을 함수로 적절히 뽑아서 코드를 간략화시키면 된다.

함수를 만드는 방법

```js
function 함수이름(매개변수) {
    함수내용
    (return 반환 값)
}
```

[전체소스코드와 수업영상](https://opentutorials.org/module/3549/21127)

## 감상과 질문

💊 1차 완강이다!

💊 다음은 심화학습~

💊 4시간 공부한 성과가 좋다.