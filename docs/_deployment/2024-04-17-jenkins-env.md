---
layout: default
title: Jenkins에서 env 파일 사용하기
parent: 배포
last_modified_date: 2024-04-17
---

# Jenkins에서 env 파일 사용하기
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## .env 파일이란..

CICD pipeline을 구축할 때 꽤나 골치가 아프다.

환경변수를 컨테이너에 주입해야 하나? 환경변수가 너무 많아 파일로밖에 관리가 안 되면 어쩌지?

### 1. jenkins 홈페이지에서 env를 하나 하나 추가해줄까?

![](/attachment/2024/04/17/env-setting.png)

- 시간이 너무 오래 걸린다
- 빠뜨릴 가능성이 농후하다

### 2. jenkinsfile

jenkinsfile에서 컨테이너를 올릴 떄 컨테이너에 환경변수를 주입시켜야 하나?

![](/attachment/2024/04/17/jenkinsfile-docker.png)

- 하나의 명령줄이 너무 길어진다.
- jenkinsfile 가독성이 떨어진다.

### 3. clone 받은 디렉토리에 .env 파일을 수작업으로 만들어 주자.

➡️ 기존 구현되어 있던 방식

`vi .env`를 서버 안에서 직접 실행하여 수작업이 필요했다.

{: .important }
이게 문제가 되었다

![](/attachment/2024/04/17/github-issue.png)

[📍참고자료](https://community.jenkins.io/t/project-files-missing-out-from-jenkins-workspace/13213)

## [해결] Jenkins에서 env 파일을 생성하기

[어떻게 하는지](https://devops.stackexchange.com/questions/13085/how-to-update-env-file-in-production-while-jenkins-deployment)

[공식문서 - Scripted pipeline syntax](https://www.jenkins.io/doc/pipeline/steps/credentials-binding/#withcredentials-bind-credentials-to-variables)

Declarative pipeline syntax가 선호되기 때문에 공식문서의 내용을 그대로 따라하면 안 된다.

Jenkins의 크리덴셜로 env 파일을 등록하고, 이 파일을 withCredentials를 활용하여 가져온 뒤 workspace로 복사하는 것이다.

### ENV Credential 등록 방법

![](/attachment/2024/04/17/01update-env.png)

![](/attachment/2024/04/17/02update-env.png)

![](/attachment/2024/04/17/01create-env.png)

![](/attachment/2024/04/17/02create-env.png)

![](/attachment/2024/04/17/03create-env.png)

### ENV 업데이트 방법

![](/attachment/2024/04/17/01update-env.png)

![](/attachment/2024/04/17/02update-env.png)

![](/attachment/2024/04/17/03update-env.png)

![](/attachment/2024/04/17/04update-env.png)

![](/attachment/2024/04/17/05update-env.png)

<details>
<summary>✅ 수정된 Jenkinsfile 코드</summary>
<div markdown="1">

```
pipeline {
  agent any
  
  environment {
    DOCKER_REGISTRY_CREDENTIALS = '레지스트리이름'
    GITHUB_CREDENTIALS = '깃허브크리덴셜이름'
    app = ''
  }
  
  stages {

    stage('Prepare') {
      steps {
        script {
          // Copy secret environment file to workspace
          withCredentials([file(credentialsId: 'env', variable: 'mySecretEnvFile')]) {
            sh "cp $mySecretEnvFile $WORKSPACE"
          }
        }
      }
    }
    
    stage('Clone') {
      steps {
        // Clone repository
        git branch: 'main',
        credentialsId: "${env.GITHUB_CREDENTIALS}",
        url: '깃허브주소'
      }
    }
    
    stage('Build Image') {
      steps {
        // Build Docker image
        script {
            app = docker.build('이미지이름')
        }
      }
    }
    
    stage('Push Image') {
      steps {
        // Push Docker image
        script {
            docker.withRegistry('https://registry.hub.docker.com', "${env.DOCKER_REGISTRY_CREDENTIALS}") {
                app.push("${env.BUILD_NUMBER}")
                app.push("latest")
            }
        }
      }
    }
    
    stage('Deploy') {
      steps {
        // Deploy Docker container
        sh "docker container stop 컨테이너이름"
        sh "docker container prune -f"
        sh "docker run -d -p 호스트포트:컨테이너포트 --name 컨테이너이름 -v 볼륭이름:볼륨과연결할컨테이너경로 --restart=always 이미지이름"
      }
    }
  }
}
```

</div>
</details>

## 새로운 문제 발생 - workspace cleanup

시간이 지나면 workspace가 지워지는데, 배포 직후 다시 파이프라인을 실행하면 workspace에 이미 존재하는 폴더가 있어 permission denied 오류가 발생한다.

![](/attachment/2024/04/17/permission-denied.png)

`sudo`로 해결할 수도 없다. 비밀번호를 입력해야 하기 때문이다.

`rm -rf` 로 삭제하는 건 제대로 동작하지 않는다.

[jenkins cleanup document](https://github.com/jenkinsci/ws-cleanup-plugin)

```js
pipeline {
    agent any
    options {
        // This is required if you want to clean before build
        skipDefaultCheckout(true)
    }
    stages {
        stage('Build') {
            steps {
                // Clean before build
                cleanWs()
                // We need to explicitly checkout from SCM here
                checkout scm
                echo "Building ${env.JOB_NAME}..."
            }
        }
    }
```

공식문서의 cleanup 방식을 참고하여 추가적으로 수정을 진행하였다.

<details>
<summary>✅ cleanup이 추가된 Jenkinsfile 코드</summary>
<div markdown="1">

```
pipeline {
  agent any
  
  environment {
    DOCKER_REGISTRY_CREDENTIALS = '레지스트리이름'
    GITHUB_CREDENTIALS = '깃허브크리덴셜이름'
    app = ''
  }

  options {
    // This is required if you want to clean before build
    skipDefaultCheckout(true)
  }
  
  stages {

    stage('Prepare') {
      steps {
        // Clean before build
        cleanWs()
        // We need to explicitly checkout from SCM here
        checkout scm
        script {
          // Copy secret environment file to workspace
          withCredentials([file(credentialsId: 'env', variable: 'mySecretEnvFile')]) {
            sh "cp $mySecretEnvFile $WORKSPACE"
          }
        }
      }
    }
    
    stage('Clone') {
      steps {
        // Clone repository
        git branch: 'main',
        credentialsId: "${env.GITHUB_CREDENTIALS}",
        url: '깃허브주소'
      }
    }
    
    stage('Build Image') {
      steps {
        // Build Docker image
        script {
            app = docker.build('pirogramming/homepage')
        }
      }
    }
    
    stage('Push Image') {
      steps {
        // Push Docker image
        script {
            docker.withRegistry('https://registry.hub.docker.com', "${env.DOCKER_REGISTRY_CREDENTIALS}") {
                app.push("${env.BUILD_NUMBER}")
                app.push("latest")
            }
        }
      }
    }
    
    stage('Deploy') {
      steps {
        // Deploy Docker container
        sh "docker container stop 컨테이너이름"
        sh "docker container prune -f"
        sh "docker run -d -p 호스트포트:컨테이너포트 --name 컨테이너이름 -v 볼륨이름:볼륨과연결할컨테이너경로 --restart=always 이미지이름"
      }
    }
  }
}
```

</div>
</details>