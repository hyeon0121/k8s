# Pod - 연습문제

1. yaml 사용해서 jenkins로 jenkins-manual 포드 생성
    
    `vi jenkins-manual-pod.yaml`
    
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: jenkins-manual
    spec:
      containers:
      - name: jenkins
        image: jenkins/jenkins
        ports:
        - containerPort: 8080
    ```
    
    `kubectl create -f jenkins-manual-pod.yaml`
    

1. jenkins 포드에서 curl 명령어로 로컬호스트:8080 접속
    
    `kubectl exec jenkins-manual --curl 127.0.0.1:8080 -s`
    

3. jenkins 포드 8888 포트 포워딩

`kuebectl port-forward jenkins-manual 8888:8080`

4. 현재 jenkins-manual 설정 yaml로 출력

`kubectl get pod jenkins-manual -o yaml`