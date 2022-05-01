# Label

- label
    - app : 애플리케이션의 구성요소, 마이크로서비스 유형 지정
    - rel : 애플리케이션의 버전
    
- 예제
    
    ```yaml
    apiVersion: apps/v1
    kind: Pod
    metadata:
      labels:
        creation_method : manual
        env : prod
    ```
    

1. 레이블 추가
    
    ```markdown
    kubectl **label** pod http-go **test=foo**
    ```
    
2. 레이블 수정
    
    ```yaml
    kubectl **label** pod http-go **test=foo --overwrite**
    ```
    
3. 레이블 삭제 ****
    
    ```markdown
    kubectl **label** pod http-go **test-**
    ```
    
4. 레이블 보기
    
    ```yaml
    kubectl get pod **--show-labels**
    ```
    
5. 레이블 필터링 해서 보기
    
    ```yaml
    kubectl get pod **--show-labels -l 'env'**
    ```
    
6. 특정 레이블 컬럼으로 확인
    
    ```yaml
    kubectl get pod **-L app,rel**
    ```
    

[Label 실습](Label%20a80be457f73b4d67811cfb2f36f88ab2/Label%20%E1%84%89%E1%85%B5%E1%86%AF%E1%84%89%E1%85%B3%E1%86%B8%209b8630b3c4604313a328148bf879e6c3.md)