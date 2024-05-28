---
layout: post
title: "쿠버네티스 env 파일 다루기"
tags: Server
excerpt_separator: <!--more-->
toc: true
---

## env, envFrom을 활용하여 Kubernetes의 파드 템플릿에 Secret, ConfigMap을 등록하는 방법을 알아보자.<!--more-->

웹 애플리케이션에 사용되는 비밀번호, 비밀키 등은 .gitignore로 등록되어 Github 상에 공개되지 않고 팀원들과만 공유하게 된다.

예를 들어 Node.js 애플리케이션의 경우 개발 환경에서 다음과 같이 비밀키를 다룬다.

1. `.env` 파일을 만들고 안에 내용을 적는다.

    <a href="/attachment/2024/05/28/01node-env.png" target="_blank">
      <img src="/attachment/2024/05/28/01node-env.png" width="60%" />
    </a>

2. `.gitignore`에 `.env` 파일을 추가한다.

    <a href="/attachment/2024/05/28/02node-gitignore.png" target="_blank">
      <img src="/attachment/2024/05/28/02node-gitignore.png" width="60%" />
    </a>

3. 팀원들과 노션 등으로 `.env` 파일 자체를 공유한다.

4. 소스코드에서는 `process.env`를 통해 설정 값에 접근한다.

    <a href="/attachment/2024/05/28/03node-processenv.png" target="_blank">
      <img src="/attachment/2024/05/28/03node-processenv.png" width="60%" />
    </a>

## 컨테이너 기술을 활용한 배포 상황에 대해 생각해 보자.

`.env` 파일을 소유하고 있는 로컬에서 이미지를 빌드하게 되면, <mark>이미지 내부에 <code>.env</code>파일을 가지게 된다.</mark>

이것은 마치 **컴파일 타임**에 설정 정보를 주입하는 것이다.

설정 정보는 **런타임** 시에 주입이 되어야 적절하다.

## 설정 정보를 애플리케이션 런타임 시에 주입되도록 하려면 어떻게 해야 할까?

[Jenkins에서는 Credentials를 지원한다.]({% link _posts/2024-04-17-jenkins-env.md %}){:target="_blank"}

쿠버네티스에서는 **Secret**과 **ConfigMap**을 활용할 수 있다.

## 쿠버네티스에 적용

### Secret과 ConfigMap은 무엇일까?

namespace의 개념을 활용한 클러스터에 설정 정보를 보관하는 방식이다.

<mark><bold>Secret</bold><mark>

- base64로 인코딩하여 작성
- 비밀번호와 같은 민감 정보를 저장
- 내용을 볼 수 없다

```yml
# db-credentials.yml

apiVersion: v1
kind: Secret
metadata:
  name: db-credentials
data:
  MYSQL_ROOT_PASSWORD: abcde
  MYSQL_PASSWORD: abcde
```

<mark><bold>ConfigMap</bold><mark>

- base64 인코딩이 필요 없다
- `kubectl describe configmap` 명령어를 통해 등록된 내용을 확인할 수 있다
- 내용을 볼 수 있다

```yml
# db-env.yml

apiVersion: v1
kind: ConfigMap
metadata:
  name: db-env
data:
  MYSQL_DATABASE: yourdbname
  MYSQL_USER: yourusername
```

### Secret과 ConfigMap yml 파일을 작성했다면, 적용하자.

```bash
kubectl apply -f db-configmap.yml
kubectl apply -f db-env.yml
```

### 이제 Pod 템플릿에서 Secret과 ConfigMap의 설정 정보를 런타임 시에 가져올 수 있다.

### MySQL 데이터베이스 스테이트풀셋을 만들어 보자.

데이터베이스이므로 다음과 같은 오브젝트들이 필요하게 된다.

- PVC
- StatefulSet
- Service
- Secret
- ConfigMap

Secret과 ConfigMap은 완료되었다.

- PVC 작성

```yml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-auth-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

- StatefulSet 작성

    📚 [MySQL StatefulSet 참고자료](https://kubernetes.io/ko/docs/tasks/run-application/run-replicated-stateful-application/){:target="_blank"}

```yml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: auth-db-sfs
spec:
  selector:
    matchLabels:
      app: auth-db-sfs
      app.kubernetes.io/name: mysql
  serviceName: "auth-db"
  replicas: 1
  template:
    metadata:
      labels:
        app: auth-db
        app.kubernetes.io/name: mysql
    spec:
      containers:
      - name: auth-db
        image: mysql:5.7 # 버전은 일정하게 유지하는 것이 좋다
        ports:
        - containerPort: 3306
        volumeMounts:
        - name: mysql-auth-vol
          mountPath: /var/lib/mysql # 파드 내부 디렉토리와 PV를 연결
          subPath: mysql # 이게 없으면 /var/lib/mysql이 비어있지 않다는 오류 발생
        envFrom: # 설정 주목!
        - configMapRef:
            name: db-env
        - secretRef:
            name: db-credentials
      volumes:
      - name: mysql-auth-vol
        persistentVolumeClaim:
          claimName: mysql-auth-pvc # 위의 PVC 이름을 가져온다
```

*설정 정보 연결 부분을 주목해 보자!*

```yml
envFrom: # 설정 주목!
- configMapRef:
    name: db-env # 들여쓰기 두 번!
- secretRef:
    name: db-credentials
```

**🤔 envFrom**

앞서 설정한 Secret, ConfigMap 설정 정보를 **key와 value 쌍으로 그대로** 한꺼번에 가져온다면 `envFrom`을 사용하면 된다.

`envFrom` 사용 시에는 `configMapRef`, `secretRef`를 사용한다.

📚 [envFrom 참고 자료](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/){:target="_blank"}로 들어가서 `envFrom`을 검색해보자.

**🤔 env**

만약 Secret, ConfigMap에 설정해 둔 key와 실제 애플리케이션에서 사용하는 key가 다르다면 `env`를 사용한다.

`env` 사용 시에는 `configMapKeyRef`, `secretKeyRef`를 사용한다.

📚 [env 참고 자료](https://kubernetes.io/ko/docs/concepts/configuration/secret/){:target="_blank"}로 들어가서 `env`를 검색해보자.

현재는 mysql 이미지를 사용하고 있고 key를 맞춰준 상태라 `envFrom`을 사용했다.

<a href="/attachment/2024/05/28/04mysqlenv.jpeg" target="_blank">
  <img src="/attachment/2024/05/28/04mysqlenv.jpeg">
</a>

- Service

    IP를 DNS 이름으로 해결하기 위해 사용한다.

```yml
apiVersion: v1
kind: Service
metadata:
  name: auth-db
  labels:
    app: auth-db
    app.kubernetes.io/name: mysql
spec:
  ports:
  - port: 3306 # 애플리케이션이 열어두고 있는 포트와 같아야 함
  selector:
    app: auth-db
```