---
layout: default
title: Promise로 HTTP Request를 사용하기
last_modified_date: 2024-04-09
---

# Promise로 HTTP Request를 사용하기
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

마이크로서비스 아키텍처를 구성하면서 각 마이크로서비스가 다른 마이크로서비스에게 요청을 보내는 일이 빈번해졌다.

서버가 서버에게 요청을 보낼 때 사용할 수 있는 게 http, fetch, axios가 있는데, 강의에서 배웠던 http 모듈로 구현을 하고 있었다.

## 하고 싶은 것

A 서버가 B 서버에게 두 번의 요청을 보낸다.

1. username을 가지고 resident 테이블에서 데이터를 조회하도록 요청한다.
2. 만약 데이터를 조회하지 못했다면 username을 가지고 agent 테이블에서 데이터를 조회하도록 요청한다.
3. 결과를 가져와서 쿠키를 생성하고 응답을 반환한다.

![](/attachment/2024/04/24/request01.jpeg)

이렇게나 많은 단계를 거쳐 통신을 하게 된다.. 이게 정말 효율적인가? 의심이 들지만 아무튼 해결해야 할 문제는 이 부분이다.

![](/attachment/2024/04/24/request02.jpeg)

http request의 결과로 response가 돌아온 뒤에 그 결과에 따라 agent 테이블을 조회하는 요청을 할지 말지가 결정된다.

## 하지만 코드가 너무 길어

기존에 사용하던 코드는 이 정도가 된다.

stop_bang_resident_mypage 컨테이너에 `/myReview` 경로로 GET 요청 하나를 보내는 코드이다.

너무너무 길다.

response가 도착했을 때 도착 결과를 기준으로 요청을 생성할지 말지를 정해야 한다.

그러면 `forwardResponse.on('end'` 뒤에 콜백 함수에 또 다시 이만큼의 request 코드가 적히게 된다.

```js
/* msa */
const getOptions = {
  host: 'stop_bang_resident_mypage',
  port: process.env.MS_PORT,
  path: '/myReview',
  method: 'GET',
  headers: {
    ...
    req.headers,
    auth: res.locals.auth
  }
}
const forwardRequest = http.request(
  getOptions,
  forwardResponse => {
    let data = '';
    forwardResponse.on('data', chunk => {
      data += chunk;
    });
    forwardResponse.on('end', () => {
      // 이부분에 콜백 지옥!
      return res.render("resident/myReview", JSON.parse(data));
    });
  }
);
forwardRequest.on('close', () => {
  console.log('Sent [myReview] message to resident_mypage microservice.');
});
forwardRequest.on('error', (err) => {
  console.log('Failed to send [myReview] message');
  console.log(err && err.stack || err);
});
req.pipe(forwardRequest);
```

## 콜백 지옥을 해결하려면?

Promise를 사용하자.

Promise로 변경하는 김에, request 코드도 너무 중복이 되니 utils로 빼서 함수로 간편하게 사용하면 좋을 것 같다.

검색해 보니 나와 동일한 고민을 하는 사람이 스택오버플로우에 이미 질문을 해 두었다!

[https://stackoverflow.com/questions/38533580/nodejs-how-to-promisify-http-request-reject-got-called-two-times](https://stackoverflow.com/questions/38533580/nodejs-how-to-promisify-http-request-reject-got-called-two-times){:target="_blank"}

그리고 엄청난 공감을 받은 답변을 발견

![](/attachment/2024/04/24/http01.png)

게다가 좀 더 아래로 읽어보면 코드를 근현대화시키신 분도 있다.

## 코드

`utils/httpRequest.js` 파일을 만들어 준다.

```js
const http = require('http');

module.exports = {
  httpRequest: (params, body) => {
    return new Promise(function (resolve, reject) {
      const clientRequest = http.request(params, incomingMessage => {
        // Response object.
        let response = {
          statusCode: incomingMessage.statusCode,
          headers: incomingMessage.headers,
          body: []
        };

        // Collect response body data.
        incomingMessage.on('data', chunk => {
          response.body.push(chunk);
        });

        // Resolve on end.
        incomingMessage.on('end', () => {
          if (response.body.length) {

            response.body = response.body.join();

            try {
              response.body = JSON.parse(response.body);
            } catch (error) {
              // Silently fail if response is not JSON.
            }
          }

          resolve(response);
        });
      });
      
      // Reject on request error.
      clientRequest.on('error', error => {
        reject(error);
      });

      clientRequest.on('close', () => {
        console.log('Sent message to auth-db microservice.');
      });

      // Write request body if present.
      if (body) {
        clientRequest.write(JSON.stringify(body)); // 형식을 맞추기 위해 stringify를 해 주어야 한다. 가장 하단 참고자료 링크 참고!!
      }

      // Close HTTP connection.
      clientRequest.end();
    });
  }
}
```

사용할 때 코드가 훨씬 간결해졌다.

이 함수는 stop_bang_auth_DB (아까 그림에서는 B 서버에 해당한다.) 서버에 `/db/resident/create` 경로로 요청을 보내고 있다.

```js
const {httpRequest} = require('../utils/httpRequest');

...

registerResident: (req, res) => {
  ...

  /* msa */
  const postOptions = {
    host: 'stop_bang_auth_DB',
    port: process.env.MS_PORT,
    path: `/db/resident/create`,
    method: 'PUT',
    headers: {
      'Content-Type': 'application/json',
    }
  };

  const requestBody = req.body;
  httpRequest(postOptions, requestBody); // 여기서 활용한다.
  
  res.redirect('/');
}
```

## 문제의 콜백 지옥은 어떻게 해결했느냐 하면

그래도 코드가 많이 길긴 하지만, 어쨌든 이해하긴 훨씬 쉬워졌다.

`httpRequest` 함수 호출 다음에 나오는 `.then`의 `res`로 `httpRequest`의 response가 넘어가게 된다.

`res` 변수에서 resident 회원 정보가 조회 되었는지 확인해 보고, 없다면 다시 `httpRequest`를 실행한다. 마찬가지로 그 결과가 다음 `.then`의 `response` 변수로 넘어가게 된다.

찾았다면 `res`가 그대로 `response` 변수로 넘어가게 된다.

두 번째 `.then`에서는 받은 결과에 대해 쿠키를 생성한 뒤 redirect 경로를 지정하고 결과 response를 넘겨주는 (그림에서는 Main 서버로) 작업을 한다.

회원의 종류가 두 가지라 if 조건식이 조금 달라서 코드가 길어졌다.

```js
login: async (req, res) => {
  /* msa */
  const postOptionsResident = {
    host: 'stop_bang_auth_DB',
    port: process.env.PORT,
    path: `/db/resident/findById`,
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    }
  };
  const postOptionsAgent = {
    host: 'stop_bang_auth_DB',
    port: process.env.PORT,
    path: `/db/agent/findById`,
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    }
  };
  const requestBody = req.body;
  httpRequest(postOptionsResident, requestBody) // resident 회원 정보 먼저 조회
    .then((res) => {
      const body = res.body;
      if(body === null) {
        return httpRequest(postOptionsAgent, requestBody) // resident 회원 정보가 없으면 agent 회원 정보 조회
      } else {
        return res; // 있다면 다음 콜백으로
      }
    })
    .then((response) => {
      const body = response.body[0];
      console.log(body);
      if(body.r_username) { // 정보가 있다면 쿠키 생성
        const token = jwt.sign({userId: body.r_username}, process.env.JWT_SECRET_KEY);
        res.cookie("authToken", token, {
          maxAge: 86400_000,
          httpOnly: true,
        });
        res.cookie("userType", 1, { // 입주민
          maxAge: 86400_000,
          httpOnly: true,
        });
        return res.redirect('/');
      } else if(body.a_username) {
        const token = jwt.sign({userId: body.a_username}, process.env.JWT_SECRET_KEY);
        res.cookie("authToken", token, {
          maxAge: 86400_000,
          httpOnly: true,
        });
        res.cookie("userType", 0, { // 공인중개사
          maxAge: 86400_000,
          httpOnly: true,
        });
        return res.redirect('/');
      } else {
        res.cookie('authToken', null, {
          maxAge: 86400_000,
          httpOnly: true,
        });
        return res.redirect('/auth/login'); // 정보가 없다면 리다이렉트
      }
    });
},
```

## [소스코드 참고하기](https://github.com/STOPBANG/stop_bang_login_logout/tree/617c4b09da5141134921cac7f3e216c0b392a120){:target="_blank"}

위의 로그인 관련 코드는 [이 파일](https://github.com/STOPBANG/stop_bang_login_logout/blob/816796643632e9f04f3f898ff9952f4d21911ba8/controllers/authController.js){:target="_blank"}에 있다.

## 📚 참고자료

[https://stackoverflow.com/questions/17811827/get-a-json-via-http-request-in-nodejs](https://stackoverflow.com/questions/17811827/get-a-json-via-http-request-in-nodejs){:target="_blank"}