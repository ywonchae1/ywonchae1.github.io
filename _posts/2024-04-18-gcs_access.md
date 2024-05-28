---
layout: post
title: Node.js GCS 이미지 접근 권한 오류 해결
tags: Node.js
excerpt_separator: <!--more-->
---

Memory Multer를 사용하면서, Multer에서 받아온 이미지를 GCS(Google Cloud Storage)에 업로드하는 과정 중 발견한 오류를 정리해 보았다.
<!--more-->

1. TOC
{:toc}

## Node.js GCS에 이미지 업로드하기

Memory Multer를 사용하고, Multer에서 받아온 이미지를 GCS(Google Cloud Storage)에 업로드하면 된다.

```js
// agentRouter.js
router.post(
  "/:id/update_process",
  agentController.upload.single("myImage"),
  agentController.updatingEnteredInfo,
);
```

`/:id/update_process` 경로 POST 요청이 들어오면 agentController의 upload 객체(Multer)에서 single메서드를 호출한다.

```js
// agentController.js
const multer = require("multer");

// Check File Type
function checkFileType(file, cb) {
  // Allowed ext
  const filetypes = /jpeg|jpg|png/;
  // Check ext
  const extname = filetypes.test(path.extname(file.originalname).toLowerCase());
  // Check mime
  const mimetype = filetypes.test(file.mimetype);

  if (mimetype && extname) {
    return cb(null, true);
  } else {
    cb("Error: Images Only!");
  }
}

module.exports = {
  upload: multer({
    storage: multer.memoryStorage(),
    limits: { fileSize: 10 * 1024 * 1024 },
    fileFilter: function (req, file, cb) {
      checkFileType(file, cb);
    },
  }),
}
```

그 다음에는 agentController의 updatingEnteredInfo 메서드를 호출하게 된다.

updatingEnteredInfo에서는 request로 넘어온 file에 대하여 GCS에 저장하는 로직이 필요하다.

우선 GCS 버킷 객체를 생성한다.

```js
// agentController.js
// .env 파일에 설정되어 있는 값을 가져온다.
const GCP_PROJECT_ID = process.env.GCP_PROJECT_ID; // my-project-1234
const GCP_KEYFILE_PATH = process.env.GCP_KEYFILE_PATH; // config/my-project-1234-abcd.json
const GCP_BUCKET_NAME = process.env.GCP_BUCKET_NAME; // new_bucket

const {Storage} = require('@google-cloud/storage');
const storage = new Storage({
  projectId: GCP_PROJECT_ID,
  keyFilename: GCP_KEYFILE_PATH
});
const bucket = storage.bucket(GCP_BUCKET_NAME);
```

updatingEnteredInfo의 로직은 다음과 같다.

```js
module.exports = {
  updatingEnteredInfo: (req, res, next) => {
    try {
      let filename = '';
      /* gcs */
      if(req.file) {
        // 이름이 중복되지 않게 시간 정보를 추가
        const date = new Date();
        const fileTime = date.getTime();
        filename = `${fileTime}-${req.file.originalname}`; // DB에 저장될 파일 이름
        
        const gcsFileDir = `agent/${filename}`; // gcs에 agent 폴더 밑에 파일이 저장
        
        // GCS로 이미지 업로드
        const blob = bucket.file(gcsFileDir);
        const blobStream = blob.createWriteStream();

        blobStream.on('finish', () => {
          console.log('gcs upload successed');
        });

        blobStream.on('error', (err) => {
          console.log(err);
        });

        blobStream.end(req.file.buffer);
      }
      // req.file.filename을 agentModel.updateEnterdAgentInfo에서 사용하고 있음
      // filename이 DB에 저장됨
      req.file.filename = filename;
      agentModel.updateEnterdAgentInfo(req.params.id, req.file, req.body, () => {
        res.redirect(`/agent/${req.params.id}`);
      });
    } catch(err) {
      console.log('updating info err : ', err);
    }
  },
}
```

### 📕 참고

[버킷 객체 만들기](https://googleapis.dev/nodejs/storage/latest/Bucket.html)

[버킷에 파일 객체 만들기](https://googleapis.dev/nodejs/storage/latest/Bucket.html#file)

[버킷에 파일 객체 올리기](https://googleapis.dev/nodejs/storage/latest/File.html#createWriteStream)

## Node.js GCS 이미지 읽어오기

```
https://storage.googleapis.com/my_bucket/agent/1713391935864-1-intro-photo-final.jpg
```

이 경로만 있으면 읽어올 수 있을 줄 알았지만 다음과 같은 오류가 발생했다.

## anonymous caller does not have storage.objects.get access to the google cloud storage object.

웹 브라우저가 읽어올 수 있는 권한이 없다는 것 같다.

[버킷의 모든 객체를 읽을 수 있도록 공개하는 방법에 대한 공식문서를 참고해보자.](https://cloud.google.com/storage/docs/access-control/making-data-public?hl=ko#buckets)

![](/attachment/2024/04/18/01allUsers.png)

![](/attachment/2024/04/18/02allUsers.png)

![](/attachment/2024/04/18/03allUsers.png)

![](/attachment/2024/04/18/04allUsers.png)

![](/attachment/2024/04/18/05allUsers.png)

## 여기서 문제가 발생

[또 다시 공식문서에서 권한을 제거하는 방법을 찾아보자.](https://cloud.google.com/storage/docs/troubleshooting?_gl=1*as6lcz*_ga*MTE5NzYxNDAyMC4xNzEwMTEzNDY0*_ga_WH2QY8WWF5*MTcxMzM4ODg5NC4zOC4xLjE3MTMzOTI0MzYuMC4wLjA.&_ga=2.59347821.-1197614020.1710113464&hl=ko#public-access-prevention)

![](/attachment/2024/04/18/06allUsers.png)

![](/attachment/2024/04/18/07allUsers.png)

![](/attachment/2024/04/18/08allUsers.png)

다시 시도하면 잘 된다.

# Node.js GCS에서 이미지 경로 가져오기

```js
// agentRouter.js
router.get(
  "/:id",
  agentController.agentProfile,
  agentController.agentProfileView
);
```

`/:id` 경로에 대한 get 요청이 들어왔을 때 다음과 같이 이미지를 띄워 주어야 한다.

![](/attachment/2024/04/18/01readgcs.png)

GCS 콘솔에서 확인해보니 publicUrl을 사용하면 될 것 같다.

[관련된 공식 문서를 참고해보자.](https://googleapis.dev/nodejs/storage/latest/File.html#publicUrl)

![](/attachment/2024/04/18/02readgcs.png)

```js
bucket.file('path/to/gcs').publicUrl();
```

```js
module.exports = {
  agentProfile: async (req, res, next) => {
    try {
      let agent = await agentModel.getAgentProfile(req.params.id);
      res.locals.agent = agent[0];

      /* gcs */
      // agent가 null일 수도 있다. 로그인 여부를 확인하는 공통관심사는 따로 분리되어 있다.
      const profileImage = res.locals.agent.a_profile_image;
      if(profileImage !== null) {
        res.locals.agent.a_profile_image = bucket.file(`agent/${profileImage}`).publicUrl(); // html에서는 locals에서 agent를 꺼내어 a_profile_image 경로를 form에 집어넣게 된다.
      }
    } catch (err) {
      console.error(err.stack);
    }
    next();
  },
}
```

ejs는 다음과 같다.

```html
<div class="photo-left">
  <% if(agent.a_profile_image == null) { %>
      <img class = "photo" src="/images/none-image-status.jpg">
  <% } else { %>
      <img class = "photo" src="<%= agent.a_profile_image %>" />
      <% } %>
</div>
```