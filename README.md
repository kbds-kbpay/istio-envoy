#istio/envoy 정리

#### Gartner의 MSA에 대한 Service구분

- [그림 1] Microservices Architecture Components @ 2018 Gartner, Inc.
![](https://user-images.githubusercontent.com/49928041/215317658-f2d86854-e802-4e46-8e11-432804940fb2.png)
- Service Mesh파트는 Micro화된 서비스간의 연결을 위해서 각 서비스에 대한 Configuration을 통합해 관리 하고 서비스를 찾아 갈수 있도록 Discovery 기능 및 서비스에 대한 분배 라우팅 역할을 해야 합니다. 
- MSA에서 Service Mesh 구성은 서비스의 연속성 및 확장성을 위해서 중요한 포인트 중에 하나입니다.

-[그림 2] Service Mesh에서의 호출은 서비스에 딸린 proxy끼리 이뤄지게 됩니다.
![](https://user-images.githubusercontent.com/49928041/215318416-d9c5b7cf-3af8-47d3-9480-d205dec3d783.png)

#### 서비스 타입에 따라, 

1.  Mesh-Native Code 방식: 플랫폼 제공 벤더에서 제공해주는 서비스 매쉬 타입(벤더에서 제어)   Azure Service Fabric

2. Mesh-Aware Code 방식: 프로그램 코드 기반 서비스 매쉬 타입(개발자가 직접 제어) Spring Cloud Neflix Eureka

3. Mesh-Agnostic Code 방식: 플랫폼에서 관리자가 임의로 설정(사이드카 인젝션으로 제어)  **istio/envoy**


다양한 기능을 수행하려면 기존 TCP기반의 proxy로는 한계가 있습니다.
그래서 Service Mesh에서의 통신은 사이드카로 배치된 경량화되고 L7계층기반의 proxy를 사용하게 됩니다.

Data Plane : 프록시들로 이루어져 트래픽을 설정값에 따라 컨트롤
Control Plane : 프록시들에 설정값을 전달하고 관리하는 컨트롤러 역할
![](https://user-images.githubusercontent.com/49928041/215318714-a5c35210-890d-4db1-bbd9-7b822abd1d91.png)

------------










### Istio
[Links](https://istio.io)
- Istio는 분산 어플리케이션에 추가될 수 있는 오픈소스 Service Mesh입니다.
- Istio is an open source service mesh that layers transparently onto existing distributed applications.

### service mesh
- Service Mesh는, 어플리케이션 코드의 변경 없이 가시성과 트래픽 관리 그리고 보안을 담당해줄 전담 인프라스트럭쳐 계층이라고 함.
- A service mesh is a dedicated infrastructure layer that you can add to your applications. It allows you to transparently add capabilities like observability, traffic management, and security, without adding them to your own code.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbvdXJ4%2FbtqvVDO3zmN%2F7oUfEbB77YYSu9ramwdJWK%2Fimg.png)

API Gateway가 마이크로서비스 외부 클라이언트들이 외부에서 접속 할 수 있도록 하는 것과 달리 서비스매쉬는 마이크로서비스 내부에서 마이크로서비스 간의 서비스 Discovery(식별), Routing(경로), Load Balancing(부하분산), 인증/인가, 보안 등의 역할을 담당합니다.





