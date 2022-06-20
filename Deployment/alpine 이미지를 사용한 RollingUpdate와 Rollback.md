# alpine 이미지를 사용한 RollingUpdate와 Rollback

1. alpine:3.4 이미지를 사용하여 deployment 생성
    1. replicas : 10
    2. maxSurge: 50%
    3. maxUnavailable: 50%
2. alpine:3.5로 롤링업데이트 수행
3. alpine:3.4로 롤백 수행

⭐️ 쿠버네티스1.18 업데이트 이후 kubectl 명령어 변경

- run → create deploy
- —dry-run → —dry-run=client

```bash
$ kubectl create deploy --image alpine:3.4 alpine-deploy --dry-run=client
deployment.apps/alpine-deploy created (dry run)

$ kubectl create deploy --image alpine:3.4 alpine-deploy --dry-run=client -o yaml > alpine-deploy.yaml
$ ls
alpine-deploy.yaml      http-go-deploy.yaml     http-go-pod.yaml        jenkins-manual-pod.yaml nginx-rc.yaml
http-go-deploy-v1.yaml  http-go-pod-v2.yaml     jenkins-deployment.yaml nginx-pod.yaml
```

1. alpine-deploy.yaml 파일

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: alpine-deploy
  name: alpine-deploy
spec:
  replicas: 1
  selector:
    matchLabels:
      app: alpine-deploy
# 정책 수정
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 50%
      maxUnavailable: 50%
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: alpine-deploy
    spec:
      containers:
      - image: alpine:3.4
        name: alpine
        resources: {}
status: {}
```

deployment 생성

```bash
$ kubectl create -f alpine-deploy.yaml --record=true
deployment.apps/alpine-deploy created

$ kubectl get all
NAME                                 READY   STATUS      RESTARTS   AGE
pod/alpine-deploy-8659f8ff96-2l4g9   0/1     Completed   2          29s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   7m39s

NAME                            READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/alpine-deploy   0/1     1            0           29s

NAME                                       DESIRED   CURRENT   READY   AGE
replicaset.apps/alpine-deploy-8659f8ff96   1         1         0       29s

```

1. replias=10 스케일링

```bash
$ kubectl rollout history deploy alpine-deploy
deployment.apps/alpine-deploy
REVISION  CHANGE-CAUSE
1         kubectl create --filename=alpine-deploy.yaml --record=true

$ kubectl scale deploy alpine-deploy --replicas=10
deployment.apps/alpine-deploy scaled

$ kubectl get pod
NAME                             READY   STATUS              RESTARTS   AGE
alpine-deploy-8659f8ff96-2l4g9   0/1     CrashLoopBackOff    4          118s
alpine-deploy-8659f8ff96-5fjvc   0/1     ContainerCreating   0          5s
alpine-deploy-8659f8ff96-62w7c   0/1     ContainerCreating   0          5s
alpine-deploy-8659f8ff96-7pxpb   0/1     ContainerCreating   0          5s
alpine-deploy-8659f8ff96-98r5q   0/1     ContainerCreating   0          5s
alpine-deploy-8659f8ff96-d4s2q   0/1     ContainerCreating   0          5s
alpine-deploy-8659f8ff96-fdgcm   0/1     ContainerCreating   0          5s
alpine-deploy-8659f8ff96-lj9gd   0/1     ContainerCreating   0          5s
alpine-deploy-8659f8ff96-x6p5s   0/1     ContainerCreating   0          5s
alpine-deploy-8659f8ff96-xpqb6   0/1     ContainerCreating   0          5s
```

1. alpine:3.5로 롤링업데이트 수행

```bash
$ kubectl get rs
NAME                       DESIRED   CURRENT   READY   AGE
alpine-deploy-8659f8ff96   10        10        0       2m44s

$ kubectl edit deploy alpine-deploy
deployment.apps/alpine-deploy edited

$ kubectl get rs
NAME                       DESIRED   CURRENT   READY   AGE
alpine-deploy-5bf4b7b6c9   10        10        0       11s
alpine-deploy-8659f8ff96   5         5         0       4m33s

$ kubectl rollout history deploy alpine-deploy
deployment.apps/alpine-deploy
REVISION  CHANGE-CAUSE
1         kubectl create --filename=alpine-deploy.yaml --record=true
2         kubectl create --filename=alpine-deploy.yaml --record=true
```
<img src="https://user-images.githubusercontent.com/34363687/174523963-bf634e9a-f6c1-4131-ad0d-e5f3cd781465.png" width="500" height="250">



1. alpine:3.4로 롤백

```bash
$ kubectl rollout undo deploy alpine-deploy --to-revision=1
deployment.apps/alpine-deploy rolled back

$ kubectl rollout history deploy alpine-deploy
deployment.apps/alpine-deploy
REVISION  CHANGE-CAUSE
2         kubectl create --filename=alpine-deploy.yaml --record=true
3         kubectl create --filename=alpine-deploy.yaml --record=true
```
