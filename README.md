#istio/envoy 정리
------------
## 목차

1. Gartner의 MSA에 대한 Service구분

2. Service Mesh란

3. ISTIO

4. ENVOY
----------



## 1. Gartner의 MSA에 대한 Service구분

- [그림 1] Microservices Architecture Components @ 2018 Gartner, Inc.
![](https://user-images.githubusercontent.com/49928041/215317658-f2d86854-e802-4e46-8e11-432804940fb2.png)

## 2. Service Mesh
- Service Mesh는 마이크로서비스 간 통신을 위해 각 서비스를 식별(Discovery)하고, 경로를 파악(Routing)하며, 로드 발란싱(Load Balancing)을 하고 전체 서비스의 장애 전파를 차단(Circuit Break)하며 Telemetry와 통합되어 로깅, 모니터링, 트래이싱 기능을 담당하는 계층
**- MSA에서 Service Mesh 구성은 서비스의 연속성 및 확장성을 위해서 중요한 포인트 중에 하나입니다.**

-[그림 2] Service Mesh에서의 호출은 서비스에 딸린 proxy끼리 이뤄 짐
![](https://user-images.githubusercontent.com/49928041/215318416-d9c5b7cf-3af8-47d3-9480-d205dec3d783.png)


### Service Mesh 장단점
#### 장점
1. 기능을 어플리케이션 외부에 구현하며 **재사용** 가능하다.
2. MicroService Architecture를 도입하면서 발생한 **런타임 복잡성 이슈를 해결**한다.
3. 어플리케이션 개발시 언어와 미들웨어 등에 **종속성을 제거**한다.

#### 단점
1. 시스템의 런타임 인스턴스 수가 크게 증가한다. (최소 2배수)
2. 서비스 간 통신에 네트워크 레이어가 추가된다.
3. 신기술이다. 구현체가 Release 될 때까지 시간이 필요하다.



### 서비스 타입에 따라,

1.  Mesh-Native Code 방식: 플랫폼 제공 벤더에서 제공해주는 서비스 매쉬 타입(벤더에서 제어)   Azure Service Fabric

2. Mesh-Aware Code 방식: 프로그램 코드 기반 서비스 매쉬 타입(개발자가 직접 제어) Spring Cloud Neflix Eureka

3. Mesh-Agnostic Code 방식: 플랫폼에서 관리자가 임의로 설정(사이드카 인젝션으로 제어)  **istio/envoy**


### 다양한 기능 수행을 위한 L7 프록시 사용이 필요
다양한 기능을 수행하려면 기존** TCP기반의 proxy로는 한계**가 있습니다.
그래서 Service Mesh에서의 통신은** 사이드카로 배치된 경량화되고 L7계층기반의 proxy를 사용**하게 됩니다.
### 서비스 매쉬를 구현하기 위해 Envoy를 사용하여 구성된 데이타 플레인을 컨트롤할 솔루션이 필요하다. 
Envoy를 데이타 플레인으로 사용하고 이를 컨트롤 해주는 오픈 소스 솔루션이 **Istio** 이다. 

------------

## 3. Istio 란,
[istio 소개글 ](https://istio.io)
- istio는 IBM, Redhat, VMware, Google등이 참여해 개발한  Service Mesh(서비스 매쉬) 구현을 위한 오픈소스 솔루션입니다. 

istio는 마이크로서비스 간의 **모든 네트워크 통신을 담당 할 수 있는 프록시인 Envoy**를 사이드카 패턴으로 마이크로 서비스들에 배포한 다음, 프록시들의 설정 값 저장 및 관리/감독을 수행하고, 프록시들에 설정값을 전달하는 **컨트롤러 역할**을 수행합니다.

즉, Istio를 사용하면 서비스 코드 변경 없이 로드밸런싱, 서비스 간 인증, 모니터링 등을 적용하여 마이크로 서비스를 쉽게 관리할 수 있다.

각각의 마이크로 서비스에 사이드카 패턴으로 배포된 Envoy 프록시를 데이타 플레인(Data Plane)이라고 하고, 데이타 플레인을 컨트롤 하는 부분은 컨트롤 플레인(Control Plane)이라고 한다


### 3.1 아키텍처
![](https://user-images.githubusercontent.com/49928041/215320481-16bace80-5369-407f-acbc-8af43dc0ced4.png)

istio의 구조 단순화 (Data Plane, Control Plane)
![](https://user-images.githubusercontent.com/49928041/215321974-87c6b26e-9c31-4302-b78b-5a95f4c16d38.png)



### 3.2 주요기능
##### 1. 트래픽 관리(Traffic Management)

Istio의 간편한 규칙(Rule) 설정과 트래픽 라우팅(Traffic Routing) 기능을 통해 서비스 간의 트래픽 흐름과 API 호출을 제어할 수 있다. 또한 서킷 브레이커(Circuit Breaker), 타임아웃(Timeout), 재시도(Retry) 기능과 같은 서비스 레벨의 속성 구성을 단순화하고, 백분율 기반으로 트래픽을 분할하여 A/B Test, Canary Rollout, 단계적(Staged) Rollout 과 같은 작업을 쉽게 설정할 수 있다. 

트래픽에 대한 더 나은 가시성과 독창적인 장애 복구 기능을 통해 문제가 발생하기 전에 문제를 찾고, 서비스 호출을 더욱 안정적으로 만들고, 어떤 상황에서든 네트워크를 더욱 안정적으로 만들 수 있다(트래픽 관리 개념 가이드)

 

##### 2. 보안(Security)

Istio의 보안기능을 통해 개발자는 어플리케이션 레벨의 보안에 보다 더 집중할 수 있다. Istio는 기본적인 보안 통신 채널을 제공하며, 대규모 서비스 통신의 인증(Authentication), 권한부여(Authorization), 암호화(Encryption) 등을 관리한다. Istio를 사용하면 기본적으로 서비스 통신은 보호되기 때문에, 다양한 프로토콜이나 런타임에서 어플리케이션 변경을 거의 하지 않고 일관된 정책을 시행할 수 있다. Istio는 플랫폼에 독립적이지만 쿠버네티스 네트워크 정책과 함께 사용하면 파드(pod)간 혹은 서비스(Service)간 통신을 보호하는 기능 등의 다양한 이점이 있다(보안 개념 가이드)

 

##### 3. 관찰 가능성(Observability)

Istio의 강력한 트레이싱(Tracing), 모니터링(Monitoring), 로깅(Logging) 기능으로 서비스 매쉬에 배포된 서비스들에 대해 더욱 자세히 파악할 수 있다. Istio의 모니터링 기능을 통해 서비스 성능이 업스트림/다운스트림에 어떤 영향을 끼치는지 파악할 수 있다. 또한 맞춤형 대쉬보드를 통해 모든 서비스 성능 대한 가시성 확인 및 성능이 다른 프로세스들에 미치는 영향 등을 확인 할 수 있다. 

 

##### 4. 플랫폼 지원(Platform support )

Istio는 플랫폼에 독립적이며 클라우드, On-Premise, Kubernetes, Mesos 등을 포함한 다양한 환경에서 실행되도록 설계되었다. Istio는 Kubernetes에 배포하거나 Consul을 사용하여 Nomad에 배포할 수 있다. 

 

##### 5. 통합과 사용자정의(Integration and Customization)

Istio의 구성 요소들은 확장 및 커스터마이징을 통해서 ACL, 로깅, 모니터링, 할당량, 감사 등를 위한 기존 솔루션들과 통합될 수 있다.



### 3.3 주요 구성 요소

##### 1. 데이타 플레인(Data Plane)
실제 데이터 트래픽이 돌아다니는 영역이며,
데이타 플레인은 envoy를 서비스 옆에 붙여서 사이드카 형식으로 배포를 해서, 서비스로 들어오고 나가는 트래픽을 envoy를 통해서 통제하게 된다. 

##### 2. 컨트롤 플레인(Control Plane)
Envoy를 컨트롤 하는 영역이며,
Istio1.4 버전까지는 파일럿(Pilot), 믹서(Mixer), 시타델(Citadel), 갤리(Galley)로 구성되어 있었지만, Istio1.5버전부터 4개의 모듈이 Istiod 라는 하나의 모듈로 통합되었다(이 글을 작성하는 때는 2020년 9월이며  Istio 버전은 1.7 버전이다)

-----------

#### istiod ver.1.5
##### 1. istiod 
서비스 디스커버리(Service Discovery), 설정 관리(Configuration Management), 인증 관리(Certificate Management) 등을 수행한다. 
- 트래픽 동작을 제어하는 라우팅 규칙을 Envoy 전용 설정으로 변환하고, 마이크로 서비스에 사이드카 방식으로 Envoy를 배포

- Envoy 설정 변경(Istio의 Traffic Management API 활용)을 통한 서비스 메시 트래픽 세부 제어

- 내장된 identity나 자격증명관리(Credential Management)를 통해 강력한 서비스 간 인증 및 사용자 인증 기능을 지원

- 인증기관(Certificate Authority. CA)의 역할 수행. 데이터 플레인에서 안전한 mTLS통신을 허용하는 인증서를 생성
----------



#### istio ver.1.4
##### 1. 파일럿 (Pilot)
파일럿은 envoy에 대한 설정 관리를 하는 역할을 한다. 

먼저 앞에서 언급했듯이 서비스들의 엔드포인트(EndPoint)들의 주소를 얻을 수 있는 서비스 디스커버리 기능을 제공한다. 

Istio에 유용한 기능중의 하나가 트래픽의 경로를 컨트롤 하는 기능인데, 서비스에서 서비스로 호출하는 경로를 컨트롤 할 수 있다. 이외도 서비스의 안정성을 제공하기 위해서 서비스간에 호출이 발생할때 재시도(retry), 장애 전파를 막기 위한 써킷 브레이커 (Circuit breaker), Timeout 등의 기능을 제공한다. 

##### 2. 믹서(Mixer)
믹서가 하는 일은 액세스 컨트롤, 정책 통제 그리고 각종 모니터링 지표(서비스 응답시간, 평균 처리량 등)의 수집이다. 

예를 들어서 서비스의 총 처리량을 정책으로 지정하여, 그 처리량 이상으로 요청을 못받게 하거나 특정 헤더값이 일치해야 요청을 받을 수 있게 하는 등의 다양한 정책을 정의하고 이를 컨트롤 할 수 있다.

또한 서비스의 응답 시간이나 평균 처리량과 같은 다양한 지표를 수집하여 저장하는 역할을 한다. 

##### 3. 시타델(Citadel)
시타델은 보안에 관련된 기능을 담당하는 모듈이다. 서비스를 사용하기 위한 사용자 인증 (Authentication)과 인가 (Authorization)을 담당한다. 또한 Istio는 통신을 TLS(SSL)을 이용하여 암호화할 수 있는데, TLS 암호화나 또는 사용자 인증에 필요한 인증서(Certification)을 관리하는 역할을 한다.  

##### 4. 갤리(Galley) 
Istio의 구성 및 설정 검증. 배포 관리 수행
----------

### 3.4 Istio 주요 특징
#### 1. 트래픽 관리(Traffic Management)

Istio의 간편한 규칙(Rule) 설정과 트래픽 라우팅(Traffic Routing) 기능을 통해 서비스 간의 트래픽 흐름과 API 호출을 제어할 수 있다. 또한 서킷 브레이커(Circuit Breaker), 타임아웃(Timeout), 재시도(Retry) 기능과 같은 서비스 레벨의 속성 구성을 단순화하고, 백분율 기반으로 트래픽을 분할하여 A/B Test, Canary Rollout, 단계적(Staged) Rollout 과 같은 작업을 쉽게 설정할 수 있다. 

트래픽에 대한 더 나은 가시성과 독창적인 장애 복구 기능을 통해 문제가 발생하기 전에 문제를 찾고, 서비스 호출을 더욱 안정적으로 만들고, 어떤 상황에서든 네트워크를 더욱 안정적으로 만들 수 있다(트래픽 관리 개념 가이드)

 

#### 2. 보안(Security)

Istio의 보안기능을 통해 개발자는 어플리케이션 레벨의 보안에 보다 더 집중할 수 있다. Istio는 기본적인 보안 통신 채널을 제공하며, 대규모 서비스 통신의 인증(Authentication), 권한부여(Authorization), 암호화(Encryption) 등을 관리한다. Istio를 사용하면 기본적으로 서비스 통신은 보호되기 때문에, 다양한 프로토콜이나 런타임에서 어플리케이션 변경을 거의 하지 않고 일관된 정책을 시행할 수 있다. Istio는 플랫폼에 독립적이지만 쿠버네티스 네트워크 정책과 함께 사용하면 파드(pod)간 혹은 서비스(Service)간 통신을 보호하는 기능 등의 다양한 이점이 있다(보안 개념 가이드)

 

#### 3. 관찰 가능성(Observability)

Istio의 강력한 트레이싱(Tracing), 모니터링(Monitoring), 로깅(Logging) 기능으로 서비스 매쉬에 배포된 서비스들에 대해 더욱 자세히 파악할 수 있다. Istio의 모니터링 기능을 통해 서비스 성능이 업스트림/다운스트림에 어떤 영향을 끼치는지 파악할 수 있다. 또한 맞춤형 대쉬보드를 통해 모든 서비스 성능 대한 가시성 확인 및 성능이 다른 프로세스들에 미치는 영향 등을 확인 할 수 있다. 

 
#### 4. 플랫폼 지원(Platform support )

Istio는 플랫폼에 독립적이며 클라우드, On-Premise, Kubernetes, Mesos 등을 포함한 다양한 환경에서 실행되도록 설계되었다. Istio는 Kubernetes에 배포하거나 Consul을 사용하여 Nomad에 배포할 수 있다. 

 

#### 5. 통합과 사용자정의(Integration and Customization)

Istio의 구성 요소들은 확장 및 커스터마이징을 통해서 ACL, 로깅, 모니터링, 할당량, 감사 등를 위한 기존 솔루션들과 통합될 수 있다.

 

## 4. Envoy란
lyft사에서 개발한 C++로 개발된 고성능 프록시. 이스티오에서는 엔보이의 확장판을 사용.
기존 프록시 L4기능 뿐 아니라 L7 기능도 지원하면서 HTTP 뿐아니라 HTTP 2.0,TCP,gRPC까지 다양한 프로토콜을 지원한다. 

[Envoy = L7 Proxy 동작 되는 예시 링크 ](https://isn-t.tistory.com/43)


- 동적 서비스 디스커버리
- 로드밸런싱
- TLS 인증서 처리
- HTTP/2, gRPC 프록시
- 서킷브레이커
- 헬스체크
- 트래픽을 스테이지 단계별로 나눠서 보내는 기능(Staged rollouts with %-based traffic split)
- 실패 삽입
- 풍부한 메트릭

쿠버네티스 pod에 사이드카 형태로 추가되어서 배포됩니다. 
배포되고 나면 믹서(mixer)에서 활용가능한 다양한 통계 정보를 제공해 줍니다.
사이드카 형태이기 때문에 기존 컨테이너나 **코드를 재작성할 필요없이 그대로 이용**할 수 있습니다.



출처
Service Mesh 란?
https://medium.com/dtevangelist/service-mesh-%EB%9E%80-8dfafb56fc07
istio란 무엇인가?
https://arisu1000.tistory.com/27865
https://twofootdog.tistory.com/78


Envoy Proxy 소개
https://sphong0417.tistory.com/8
istio와 envoy란
https://blog.naver.com/PostView.nhn?blogId=sharplee7&logNo=222157988958


