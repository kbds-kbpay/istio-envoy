#istio/envoy 정리
------------
## 목차

1. Gartner의 MSA에 대한 Service구분

2. Service Mesh란

3. ISTIO

4. ENVOY
----------



#### Gartner의 MSA에 대한 Service구분

- [그림 1] Microservices Architecture Components @ 2018 Gartner, Inc.
![](https://user-images.githubusercontent.com/49928041/215317658-f2d86854-e802-4e46-8e11-432804940fb2.png)

#### Service Mesh :
- Service Mesh는 마이크로서비스 간 통신을 위해 각 서비스를 식별(Discovery)하고, 경로를 파악(Routing)하며, 로드 발란싱(Load Balancing)을 하고 전체 서비스의 장애 전파를 차단(Circuit Break)하며 Telemetry와 통합되어 로깅, 모니터링, 트래이싱 기능을 담당하는 계층
**- MSA에서 Service Mesh 구성은 서비스의 연속성 및 확장성을 위해서 중요한 포인트 중에 하나입니다.**

-[그림 2] Service Mesh에서의 호출은 서비스에 딸린 proxy끼리 이뤄지게 됩니다.
![](https://user-images.githubusercontent.com/49928041/215318416-d9c5b7cf-3af8-47d3-9480-d205dec3d783.png)


#### Service Mesh 장단점
###### 장점
기능을 어플리케이션 외부에 구현하며 **재사용** 가능하다.
MicroService Architecture를 도입하면서 발생한 **런타임 복잡성 이슈를 해결**한다.
어플리케이션 개발시 언어와 미들웨어 등에 **종속성을 제거**한다.
###### 단점
시스템의 런타임 인스턴스 수가 크게 증가한다. (최소 2배수)
서비스 간 통신에 네트워크 레이어가 추가된다.
신기술이다. 구현체가 Release 될 때까지 시간이 필요하다.


#### 서비스 타입에 따라, 

1.  Mesh-Native Code 방식: 플랫폼 제공 벤더에서 제공해주는 서비스 매쉬 타입(벤더에서 제어)   Azure Service Fabric

2. Mesh-Aware Code 방식: 프로그램 코드 기반 서비스 매쉬 타입(개발자가 직접 제어) Spring Cloud Neflix Eureka

3. Mesh-Agnostic Code 방식: 플랫폼에서 관리자가 임의로 설정(사이드카 인젝션으로 제어)  **istio/envoy**


#### 다양한 기능 수행을 위한 L7 프록시 사용이 필요
다양한 기능을 수행하려면 기존 TCP기반의 proxy로는 한계가 있습니다.
그래서 Service Mesh에서의 통신은 사이드카로 배치된 경량화되고 L7계층기반의 proxy를 사용하게 됩니다.

##### Data Plane : 프록시들로 이루어져 트래픽을 설정값에 따라 컨트롤
##### Control Plane : 프록시들에 설정값을 전달하고 관리하는 컨트롤러 역할
![](https://user-images.githubusercontent.com/49928041/215318714-a5c35210-890d-4db1-bbd9-7b822abd1d91.png)

------------

Envoy를 이용해서 서비스 매쉬를 구현하기 위해서는 Envoy로 구성된 데이타 플레인을 컨트롤할 솔루션이 필요하다. Envoy를 데이타 플레인으로 사용하고 이를 컨트롤 해주는 오픈 소스 솔루션이 Istio 이다. (http://istio.io)


### Istio
[istio 소개글 Links](https://istio.io)
- istio는 IBM, Redhat, VMware, Google등이 참여해 개발한  Service Mesh(서비스 매쉬) 구현을 위한 오픈소스 솔루션입니다. istio는 마이크로서비스 간의 모든 네트워크 통신을 담당 할 수 있는 프록시인 Envoy를 사이드카 패턴으로 마이크로 서비스들에 배포한 다음, 프록시들의 설정 값 저장 및 관리/감독을 수행하고, 프록시들에 설정값을 전달하는 **컨트롤러 역할**을 수행합니다.
-  kubernetes를 기본으로 지원
-  Control Plane — Data Plane 구조로 동작
-  Envoy를 기본 Proxy로 사용
​
- Istio는 분산 어플리케이션에 추가될 수 있는 오픈소스 Service Mesh입니다.
- Istio is an open source service mesh that layers transparently onto existing distributed applications.
- istio는 Serive Mesh를 구성하는 방식의 하나로 Side Car 역할을 하는 Proxy(envoy)와 이를 제어하는 Control Plane(istio)으로 구성 됨









