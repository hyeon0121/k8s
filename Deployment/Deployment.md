# Deployment

- 애플리케이션을 다운타임 없이 업데이트 가능하도록 도와주는 리소스
- 레플리카셋 상위에 배포됨

- 연습문제
    1. deploy-jenkins 생성
    2. app:jenkins-test로 레이블링
    3. 배포된 포드 하나 삭제 후 생성되는 포드 관찰
    4. 새로 생성된 포드의 레이블을 바꾸어 Deployment의 관리영역에서 벗어나기
    5. 레플리카수 5개로 스케일링
    6. edit 명령어를 이용해 10개로 스케일링 
    
1. deploy-jenkins.yaml 파일 생성

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deploy-jenkins
  labels:
    app: jenkins-test
spec:
  replicas: 3
  selector:
    matchLabels:
      app: jenkins-test
  template:
    metadata:
      labels:
        app: jenkins-test
    spec:
      containers:
      - name: jenkins
        image: jenkins
        ports:
        - containerPort: 8080
```

```objectivec
$ kubectl create -f deploy-jenkins.yaml

### 조회 ###
$ kubectl get all
NAME                                  READY   STATUS              RESTARTS   AGE
pod/deploy-jenkins-5d7c95487d-2zzw6   0/1     ContainerCreating   0          4s
pod/deploy-jenkins-5d7c95487d-c6bjj   0/1     ContainerCreating   0          4s
pod/deploy-jenkins-5d7c95487d-xqlhl   0/1     ContainerCreating   0          4s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   30s

NAME                             READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/deploy-jenkins   0/3     3            0           4s

NAME                                        DESIRED   CURRENT   READY   AGE
replicaset.apps/deploy-jenkins-5d7c95487d   3         3         0       4s

```

1. 배포된 포드 하나 삭제 후 자동으로 생성되는 포드 관찰

```objectivec
$ kubectl delete pod deploy-jenkins-5d7c95487d-2zzw6
pod "deploy-jenkins-5d7c95487d-2zzw6" deleted

$ kubectl get pod
NAME                                  READY   STATUS             RESTARTS   AGE
pod/deploy-jenkins-5d7c95487d-c6bjj   0/1     ImagePullBackOff   0          4m14s
pod/deploy-jenkins-5d7c95487d-jdj64   0/1     ImagePullBackOff   0          39s
pod/deploy-jenkins-5d7c95487d-xqlhl   0/1     ImagePullBackOff   0          4m14s

```

1. label 삭제

```objectivec
$ kubectl get pods --show-labels
NAME                              READY   STATUS             RESTARTS   AGE     LABELS
deploy-jenkins-5d7c95487d-c6bjj   0/1     ImagePullBackOff   0          6m21s   app=jenkins-test,pod-template-hash=5d7c95487d
deploy-jenkins-5d7c95487d-jdj64   0/1     ImagePullBackOff   0          2m46s   app=jenkins-test,pod-template-hash=5d7c95487d
deploy-jenkins-5d7c95487d-xqlhl   0/1     ImagePullBackOff   0          6m21s   app=jenkins-test,pod-template-hash=5d7c95487d

$ kubectl label pod deploy-jenkins-5d7c95487d-c6bjj app-
pod/deploy-jenkins-5d7c95487d-c6bjj unlabeled

$ kubectl get pods --show-labels
NAME                              READY   STATUS              RESTARTS   AGE    LABELS
deploy-jenkins-5d7c95487d-4qsxb   0/1     ContainerCreating   0          3s     app=jenkins-test,pod-template-hash=5d7c95487d
deploy-jenkins-5d7c95487d-c6bjj   0/1     ErrImagePull        0          11m    pod-template-hash=5d7c95487d
deploy-jenkins-5d7c95487d-jdj64   0/1     ImagePullBackOff    0          8m4s   app=jenkins-test,pod-template-hash=5d7c95487d
deploy-jenkins-5d7c95487d-xqlhl   0/1     ErrImagePull        0          11m    app=jenkins-test,pod-template-hash=5d7c95487d
```

1. 레플리카수 5개 스케일링

```objectivec
$ kubectl scale deploy deploy-jenkins --replicas=5
deployment.apps/deploy-jenkins scaled

$ kubectl get pod
NAME                              READY   STATUS              RESTARTS   AGE
deploy-jenkins-5d7c95487d-4qsxb   0/1     ImagePullBackOff    0          6m6s
deploy-jenkins-5d7c95487d-6t5px   0/1     ContainerCreating   0          4s
deploy-jenkins-5d7c95487d-c6bjj   0/1     ImagePullBackOff    0          17m
deploy-jenkins-5d7c95487d-jdj64   0/1     ImagePullBackOff    0          14m
deploy-jenkins-5d7c95487d-wpv76   0/1     ContainerCreating   0          4s
deploy-jenkins-5d7c95487d-xqlhl   0/1     ImagePullBackOff    0          17m
```

1. edit 명령어로 레플리카 수 10개로 스케일링

```objectivec
$ kubectl edit deploy deploy-jenkins
deployment.apps/deploy-jenkins edited

$ kubectl get pod
NAME                              READY   STATUS              RESTARTS   AGE
deploy-jenkins-5d7c95487d-4qsxb   0/1     ImagePullBackOff    0          9m2s
deploy-jenkins-5d7c95487d-6t5px   0/1     ImagePullBackOff    0          3m
deploy-jenkins-5d7c95487d-9xqf6   0/1     ContainerCreating   0          6s
deploy-jenkins-5d7c95487d-brn9v   0/1     ContainerCreating   0          6s
deploy-jenkins-5d7c95487d-c6bjj   0/1     ImagePullBackOff    0          20m
deploy-jenkins-5d7c95487d-dcdxj   0/1     ContainerCreating   0          6s
deploy-jenkins-5d7c95487d-jdj64   0/1     ImagePullBackOff    0          17m
deploy-jenkins-5d7c95487d-mkmv2   0/1     ContainerCreating   0          6s
deploy-jenkins-5d7c95487d-pjjfb   0/1     ContainerCreating   0          6s
deploy-jenkins-5d7c95487d-wpv76   0/1     ImagePullBackOff    0          3m
deploy-jenkins-5d7c95487d-xqlhl   0/1     ImagePullBackOff    0          20m
```