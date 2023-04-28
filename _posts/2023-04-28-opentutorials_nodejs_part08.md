---
layout: post
title: "[OpenTutorials] Node.js 공부 Part08 - 🐘 post 방식으로 서버가 클라이언트로부터 받은 데이터를 처리하기! / 여기 정말 재밌다!"
categories: [serverSystem]
---

## 🐘

> 가장 좋아하는 부분이니 코끼리 이모티콘을 채택한다!

## 배운 내용

🐘 클라이언트가 서버에 데이터를 비밀스럽게 전송할 수 있다.

🐘 객체나 딕셔너리에 대한 이해가 있으면 도움이 된다!

### 🐘 목표

- 클라이언트로부터 post로 받은 데이터를 가공하여 파일을 생성한다.

### 🐘 방법

```js
if(mypathname === '/'){
    ...
} else if(mypathname === '/create') { //여기!
    myfs.readdir('./data', function(error, filelist) {
        title = 'WEB - create'; //뭐든 상관 없음
        var list = templateList(filelist); //글 목록 생성
        var createPage = `
            <form action='/create_process' method='post'>...
            `                               //위의 html코드와 동일
        var template = templateHTML(title, list, createPage, '');

        response.writeHead(200); //정상처리됐다는 신호
        response.end(template); //template을 화면에 뿌려줌
    });
} else {
    ...
}
```

지난번 main.js 코드

이제 제출 버튼을 누르게 되면 form의 action이 수행된다고 생각하면 된다.

그러면 원래 /create라는 pathname에서 /create_process로 pathname 값이 변경이 되고 서버는 계속 주시하고 있다가 /create_process에 해당하는 if문을 찾으러 간다.

지금 없으니까 else로 분류되어 Not found가 나올 것이다.

💡 create와 마찬가지로 else if를 또 추가해 주면 된다.

```js
if(mypathname === '/'){
    ...
} else if(mypathname === '/create') {
    ...
} else if(mypathname === '/create_process') { //여기!

} else {
    ...
}
```

추가를 했다면 안쪽에 어떤 걸 할지 적어주면 된다.

코드를 좀 위로 올려 app을 확인해 본다.

```js
var app = http.createServer(function(request, response) {
    var _url = request.url;
    var queryData = myurl.parse(_url, true).query;
    var mypathname = myurl.parse(_url, true).pathname;
    var title = queryData.id;

    if(mypathname === '/'){
        ...
    } else if(mypathname === '/create') {
        ...
    } else if(mypathname === '/create_process') { //여기!

    } else {
        ...
    }
});
```

제출 버튼을 누르면 post 방식으로 다시 되돌아온다.

*되돌아 온 여러 정보들은 request 변수 안에 다 들어있다.*

(response는 서버가 클라이언트한테 보내 줄 여러 정보들을 다 가지고 있다.)

그치만 데이터의 크기는 알 수가 없어 서버는 데이터를 툭툭 끊어서 일정 단위로 가져오게 된다.

이것을 합쳐주는 작업을 거친 뒤, 받은 데이터를 가공하여 사용하면 된다.

제출 버튼을 눌렀을 때 request 안의 post 데이터에는

{title : "MongoDB", description : "MongoDB is..."}

이런 식으로 들어있다고 생각하면 좋다.

request.title 이렇게 접근하게 되면 MongoDB를 가져오게 되는 것.

💡 우선 최상단에 querystring이라는 모듈을 불러오고서 시작하자!

```js
var qs = require('querystring');
```

💡 else if문 코드를 만들어보자

```js
if(mypathname === '/'){
    ...
} else if(mypathname === '/create') {
    ...
} else if(mypathname === '/create_process') { //여기!
    var body = ''; //데이터들을 합쳐서 저장해 둘 공간. 공백으로 초기화
    request.on('data', function(data) {
        body += data;
    });  //끊긴 데이터를 끝까지 받아 body에 이어서 저장
    request.on('end', function() {
        var post = qs.parse(body); //post로 받은 정보를 저장
        var title = post.title; //받은 title 저장
        var description = post.description; //받은 description 저장
        ...
```

qs(querystring 모듈과 같다. 코드 맨 윗줄에 아까 추가하였다.)를 사용해야하는 이유는, post로 전송받은 데이터는 url로 전송되는 것이 아니기 때문이다.

**비밀스럽게** 안 보이는 형태로 전송이 된다.

그것을 구분해서 받아 올 모듈이 querystring이라는 모듈이다.

그냥 myurl.parse()를 하면 받을 게 없다. (이걸 사용할 때는 get 방식일 때라서 url을 통해 정보를 받을 수 있으니 사용했었다.)

```js
if(mypathname === '/'){
    ...
} else if(mypathname === '/create') {
    ...
} else if(mypathname === '/create_process') { //여기!
    var body = ''; //데이터들을 합쳐서 저장해 둘 공간. 공백으로 초기화
    request.on('data', function(data) {
        body += data;
    });  //끊긴 데이터를 끝까지 받아 body에 이어서 저장
    request.on('end', function() {
        var post = qs.parse(body); //post로 받은 정보를 저장
        var title = post.title; //받은 title 저장
        var description = post.description; //받은 description 저장

        myfs.writeFile(`data/${title}`, description, 'utf8', function(err) {
            response.writeHead(302, {Location: `/?id=${title}`});
            response.end('success');
        });
    });
} else {
    ...
}
```

받은 데이터를 토대로 파일을 만드는 과정이다.

302는 페이지를 리다이렉션, 다른 페이지로 옮겨주는 역할을 한다.

create_process라는 페이지는 있을 필요가 없고, 그냥 데이터를 서버에 파일로 저장하는 역할만 하면 되니까 완성된 파일이 화면에 보여지도록 하는 게 더 직관적이기 때문이다.

Location 뒤에 이동할 경로를 적어 주면 된다.

이러면 mypathname이 /로 변경이 되고(? 뒤는 쿼리스트링이다. pathname은 그 앞에까지!) 서버는 계속 주시하다가 코드 상 첫 번째 if문에 걸려 안쪽 내용을 실행하니 원하는 대로 동작한다.

[전체소스코드와 수업영상](https://opentutorials.org/course/3332/21137)

## 감상과 질문

🐘 request 안에 post 내용과 url 정보 등등등이 들어 있다는 것이 맞는가?

맞는 것 같다. 왜냐면 _url 만들 때도 request를 사용했고, url 모듈인 myurl이 _url을 사용했기 때문이다.