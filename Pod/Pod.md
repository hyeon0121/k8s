# Pod

- 정의
    - apiVersion : 쿠버네티스 api 버전
    - kind : pod, replica, service 등
    - metadata : pod와 관련된 name, namespace, label 등의 정보
    - spec : container, volume 등의 정보
    - status : pod의 상태, 각 컨테이너의 설명 및 상태, pod 내부 Ip 등의 정보
    
- 도커와 k8s의 차이점
    
    만약, 컨테이너가 2개면 하나가 죽었을 때 나머지 하나에 붙고, 다시 컨테이너 하나 다시 띄우고 하니까 무중단으로 서비스 제공 가능한 것
    

[Pod 실습](Pod%20c9564e8a14e543dfa0ac8bdc93a820ca/Pod%20%E1%84%89%E1%85%B5%E1%86%AF%E1%84%89%E1%85%B3%E1%86%B8%20abbe315c20044329a2bbf71d13e07a10.md)

[Label](Pod%20c9564e8a14e543dfa0ac8bdc93a820ca/Label%20a80be457f73b4d67811cfb2f36f88ab2.md)