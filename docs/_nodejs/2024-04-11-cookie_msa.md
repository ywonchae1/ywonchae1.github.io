---
layout: default
title: MSA에서 HTTP 모듈의 request와 cookie
last_modified_date: 2024-04-11
---

# MSA에서 HTTP 모듈의 request와 cookie

## 프로젝트 MSA(Microservice Architecture) 구조

원래 전체 코드였던 main으로부터 공인중개사 회원의 마이페이지와 관련된 기능을 담당하는 agent_mypage 마이크로서비스가 분리되어

main과 agent_mypage, 두 개의 마이크로서비스로 분리된 상태이다.

1. 웹브라우저는 `/agent/settings/update` POST 요청을 보낸다.
2. main 마이크로서비스는 agent_mypage로 `/settings/update` POST 요청을 보낸다.
3. agent_mypage 마이크로서비스는 로그인한 사용자 정보를 조회하고, 계정 정보를 수정하여 DB에 반영한다.

## 분리하면서 수정이 필요했던 부분

기존 main 코드에서는 다음과 같은 인증 절차를 거쳤다.

1. 로그인 사용자의 JWT 값을 쿠키로부터 받아 온다.
2. JWT 값을 해독한다. ➡️ 여기에서 사용자의 ID(이것은 PK가 아니다. 로그인할 때 사용하는 ID 문자열이다.)를 알아낼 수 있다.
3. Model에서 해당 ID의 계정의 이메일을 변경하여 DB에 반영한다.

마이크로서비스로 분리해야 하기 때문에 main에서 알고 있는 JWT 값 또는 ID를 agent_mypage로 넘겨주어야 했다.

## 해결 방안 1. 쿠키로 넘겨 주자 ❌

쿠키는 헤더에 들어 있으니 헤더를 넘겨 주면 되겠다고 생각했다.

```js
module.exports = {
  updateSettings: (req, res) => {
    /* msa */
    const postOptions = {
      host: 'stop_bang_agent_mypage', // agent_mypage 컨테이너 이름
      port: process.env.MS_PORT,
      path: '/settings/update',
      method: 'POST',
      headers: req.headers
    }
    const forwardRequest = http.request(
      postOptions,
      forwardResponse => {
        let data = '';
        forwardResponse.on('data', chunk => {
          data += chunk;
        });
        forwardResponse.on('end', () => {
          if(forwardResponse.statusCode === 302) { // redirect
            res.writeHeader(forwardResponse.statusCode, forwardResponse.headers);
            forwardResponse.pipe(res);
          } else {
            const result = JSON.parse(data);
            if(result.message !== null)
              return res.render('notFound.ejs', { message: result.message });
          }
        });
      }
    );
    forwardRequest.on('close', () => {
      console.log('Sent [updateSettings] message to agent_mypage microservice.');
    });
    forwardRequest.on('error', (err) => {
      console.log('Failed to send [updateSettings] message');
      console.log(err && err.stack || err);
    });
    forwardRequest.write(JSON.stringify(req.body));
    req.pipe(forwardRequest);
  },
}
```

그런데 Content-Type이 application/json으로 설정되어 있지 않아서 쿠키가 제대로 전달되지 못했다.

그럼 쿠키만 넘겨서 이렇게 해볼까 했지만 형식이 맞지 않아 역시 실패했다.

```js
const postOptions = {
  host: 'stop_bang_agent_mypage', // agent_mypage 컨테이너 이름
  port: process.env.MS_PORT,
  path: '/settings/update',
  method: 'POST',
  headers: {
    'Cookie': headers.cookies, // 형식이 틀렸다
    'Content-Type': 'application/json'
  }
}
```

`headers.cookies`는 객체 형식인데, Cookie는 문자열 형식이기 때문이다.

{: .note }
> headers.cookies는 다음과 같이 구성되어 있다.
```
> {
>   authToken: 'aaaa',
>   userType: '0'
> }
```

형식을 맞춰주려면 이런 식으로 해야 한다.

```js
const postOptions = {
  host: 'stop_bang_agent_mypage', // agent_mypage 컨테이너 이름
  port: process.env.MS_PORT,
  path: '/settings/update',
  method: 'POST',
  headers: {
    'Cookie': `authToken=${req.cookies.authToken}; userType=${req.cookies.userType};`,
    'Content-Type': 'application/json'
  }
}
```

너무 불편해서 다른 방법을 찾아보았다.

## 해결 방안 2. 쿠키는 위험하다. ID를 넘겨 주자. ✅

쿠키를 넘겨줄 수 있는 방식도 있지만 조작될 위험이 있다고 한다.

사실 main에서 모든 request에 대하여 쿠키를 가공하는 작업을 하고 있다.

```js
app.use((req, res, next) => {
  try {
    const decoded = jwt.verify(
      req.cookies.authToken,
      process.env.JWT_SECRET_KEY
    );
    if (decoded.userId == null)
      return res.render('notFound.ejs', {message: "로그인이 필요합니다"});
      res.locals.auth = decoded.userId;
      res.locals.userType = req.cookies.userType;
      res.locals.is_admin = adminControl.getAdmin(decoded.userId);
  } catch (error) {
    res.locals.auth = "";
    res.locals.userType = "";
    res.locals.is_admin = "";
  }
  next();
})
```

`res.locals.auth`에 ID가 저장이 되기 때문에 이 값을 넘겨 주기로 한다.

어차피 POST 방식 요청이라 body가 있을 것이기 때문에 body 객체에 ID 데이터를 추가하여 agent_mypage 마이크로서비스에 request를 보낸다.

```js
module.exports = {
  updateSettings: (req, res) => {
    /* msa */
    req.body.userId = res.locals.auth; // ID를 body에 추가
    const postOptions = {
      host: 'stop_bang_agent_mypage', // agent_mypage 컨테이너 이름
      port: process.env.MS_PORT,
      path: '/settings/update',
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      }
    }
    ...생략
    forwardRequest.write(JSON.stringify(req.body)); // body를 써준다
    req.pipe(forwardRequest); // request를 연결해준다
  },
}
```

기존 코드에서는 `req.cookies.authToken`을 해독하고 ID를 가져왔던 작업을 완전히 제거하고, body에 담긴 ID를 가져와 처리하는 것으로 수정한다.

이렇게 수정된 코드는 main이 아닌 agent_mypage 마이크로서비스 코드에 작성된다.

**기존 코드**

```js
updateSettings: (req, res, next) => {
  if (req.cookies.authToken == undefined) res.render('notFound.ejs', {message: "로그인이 필요합니다"});
  else {
    const decoded = jwt.verify(
      req.cookies.authToken,
      process.env.JWT_SECRET_KEY
    );
    let a_id = decoded.userId;
    const body = req.body;
    if (a_id === null) res.render('notFound.ejs', {message: "로그인이 필요합니다"});
    else {
      agentModel.updateAgent(a_id, body, (result, err) => {
        if (result === null) {
          console.log("error occured: ", err);
        } else {
          res.locals.redirect = "/agent/settings";
          next(); // main에서 view를 호출했었지만 ms로 분리하면서 제거해야 한다.
        }
      });
    }
  }
},
```

**수정된 코드**
```js
updateSettings: (req, res) => {
  const body = req.body;
  agentModel.updateAgent(body.userId, body, (result, err) => {
    if (result === null) {
      console.log("error occured: ", err);
    } else {
      return res.status(302).redirect("/agent/settings");
    }
  });
},
```