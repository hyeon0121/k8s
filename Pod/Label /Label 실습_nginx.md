# Label 실습

1. app=nginx 레이블을 가진 포드 생성
    
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
          protocol: TCP
    ```
    
2. app=nginx 가진 포드 get
    
    ```yaml
    kubectl get pod -l app=nginx
    ```
    
3. get된 포드의 레이블의 app 확인
    
    ```yaml
    kubectl get pod -l app=nginx -L app
    ```
    
4. app=nginx 레이블을 가진 포드에 team=dev1 레이블 추가
    
    ```yaml
    kubectl label pod nginx team=devl1
    ```