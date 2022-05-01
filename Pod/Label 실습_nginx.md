# Label 실습

![Untitled](Label%20%E1%84%89%E1%85%B5%E1%86%AF%E1%84%89%E1%85%B3%E1%86%B8%209b8630b3c4604313a328148bf879e6c3/Untitled.png)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: ngin
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