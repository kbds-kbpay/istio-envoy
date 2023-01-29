# istio-envoy
istio/envoy 용어정리

Istio를 사용하기 전에
여 규칙이나 환경이 변경되면 업데이트합니다.

Istio를 사용하기 전에
Istio를 사용하기 전에




출처 : https://www.linkedin.com/pulse/istio%EB%8A%94-%EB%AC%B4%EC%97%87%EC%9D%B4%EA%B3%A0-%EC%99%9C-%EC%A4%91%EC%9A%94%ED%95%A0%EA%B9%8C-sean-lee/?originalSubdomain=kr

Istio 공식 홈페이지(https://istio.io)에 따르면,
Istio는 분산 어플리케이션에 추가될 수 있는 오픈소스 Service Mesh 입니다.

Istio is an open source service mesh that layers transparently onto existing distributed applications.

service mesh(서비스 메쉬)란?
A service mesh is a dedicated infrastructure layer that you can add to your applications. It allows you to transparently add capabilities like observability, traffic management, and security, without adding them to your own code.
> Service Mesh는, 어플리케이션 코드의 변경 없이 가시성과 트래픽 관리 그리고 보안을 담당해줄 전담 인프라스트럭쳐 계층이라고 함.

ISTIO는 서비스메쉬라는 개념을 구현한 오픈소스 솔루션이며, Google과 IBM 그리고 Lyft 3사의 개발자들에 의해 공동으로 개발되어 2017년 5월 Istio 버전 0.1이 공개 됨.

같은 성질의 서비스메쉬들..
AWS의 App Mesh // Envoy 함께 사용
Hashicorp의 Consul // Envoy 함께 사용
Kong의 Mesh, Linkerd 들이 있다.

WHY? 서비스메쉬를 사용하는 걸까?
마이크로서비스 환경도 DNS와 로드밸런서로 관리하면 되는게 아닐까?
장점 : 가시성 (Observability), 트래픽 관리 (Traffic Management), 보안 (Security), 계층 (Layer)에 있다.

**가시성**
가시성의 3대요소
	로그, 메트릭, 트레이스 >> 인프라 및 어플리케이션에 대한 중요한 인사이트를 제공해주며, 점차 자동화되고 있는 현대 IT 환경에서 그 중요도를 더해나가고 있습니다.


기존 에는,  로그, 메트릭, 트레이스에 대해
	직접 로깅과 메트릭수집 그리고 트레이스 관련 코드를 개발자가 코드로 작성해야만 이용할 수 있었다.
	어플리케이션 로그 관점에서는 다음과 같은 솔루션을 도입해서 활용 가능하나,마이크로서비스 환경에서 마이크로서비스들끼리 어떻게 트래픽을 주고 받는지에 관한 로그는 따로 확보하기가 쉽지 않았습니다.
	1. ELK 스택(Elasticsearch를 사용)
	2. Sumo logic, Splunk, Amazon CloudWatch Logs 등 여러 솔루션들을 도입해서 활용하면 되지만  메트릭과 트레이싱도 마찬가지였습니다

	즉, Service Mesh는 어플리케이션 바깥에서 어떤 일이 벌어지는지에 대한 가시성을 자동화된 계층이 처리해줌으로써

	개발자로 하여금 어플리케이션의 품질을 개선하거나 신규 기능을 추가하는 등의 보다 생산적인 곳에 시간을 할애할 수 있게 만들어줍니다. 이러한 가시성이 Service Mesh가 제공하는 장점입니다.
	또한 Istio는 내부적으로 사용하는 Envoy의 강력한 Observability 기능 덕분에 매우 쉽게 로그와 메트릭, 트레이싱을 구현할 수 있습니다.

**트래픽 관리**
Service Mesh라는 용어의 Mesh를 네트워크의 용어 관점에서 바라봤을때,
	Mesh Network는 "https://namu.wiki/w/%EB%A9%94%EC%8B%9C%20%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC"
	1) 장점[편집]
	노드가 단 하나라도 살아있다면, 전체 네트워크가 항상 작동된다.
	2) 단점[편집]
	'살아있는' 노드들과 통신을 유지하는 과정에서 무지막지한 자원 소모가 발생한다.
	네트워크를 구성하는 각 노드들이 다른 노드의 작동 가능/작동 불가능에 상관없이 항상 네트워크를 가동하는 형태의 구조이다.

	이러한 어원적 특성으로 보았을때,
	 Istio는 Sidecar Pattern을 이용해서 어플리케이션 코드의 변경없이 작업자가 VirtualService와 DestinationRule 이라는 Istio의 Custom Resource를 통해서 손쉽게 원하는 서비스로 트래픽을 보낼 수 있게 만들어줍니다. 이런 기능을 활용하면 VirtualService의 weight와 subset을 변경하는 것 만으로 Canary 배포 등의 작업을 쉽게 수행할 수 있습니다.
	 
	이를 통해서 얻는 이점은
	순수하게 Kubernetes 만으로 Canary를 구현하려 했다면, 직접 replica의 개수를 조정했어야 가능했겠지만 이 과정이 매우 단순해진 것입니다. 

	또한 ISTIO에 Envoy의 기능을 활용하여 Header와 Path 등 L7 라우팅 역시 매우 손쉽게 설정할 수 있습니다. 이것이 Service Mesh가 제공하는 트래픽 관리의 이점입니다.

**보안**
	하나의 모노리스로 구현되고 내부 컴포넌트간에 function call이었던 것이 독립적인 마이크로서비스 간의 통신으로 동작하게 되자 Man in the middle 공격의 가능성도 대비할 필요가 생겼습니다
	Man in the middle "중간자 공격"
	https://ko.wikipedia.org/wiki/%EC%A4%91%EA%B0%84%EC%9E%90_%EA%B3%B5%EA%B2%A9"
	중간자 공격(man in the middle attack, MITM)은 네트워크 통신을 조작하여 통신 내용을 도청하거나 조작하는 공격 기법
	많은 암호 프로토콜은 중간자 공격을 막기 위하여 인증을 사용한다. 예를 들어, TLS/SSL 프로토콜은 공개 키를 기반으로 한 인증을 사용한다.
	이에대한 대응으로, Service Mesh들은 모두 서비스를 인증/인가하는 기능들을 제공하고 있습니다

	 Istio에서 매우 손쉽게 설정할 수 있는 mTLS (Mutual TLS) 설정입니다.
	 이를 통해 Istio Control Plane이 알지 못하는 서비스와는 아예 통신이 불가능하게 막을 수 있으며, Policy를 통해 어떤 서비스가 어떤 서비스와 통신할 수 있는지 없는지 여부를 코드화하여 관리할 수 있습니다. 이런 Service Mesh의 보안기능들을 활용하면 어플리케이션 코드의 변경없이 클러스터의 보안을 강화할 수 있는 보안 관점의 이점입니다.
 
**계층**
	공통된 기능을 별도의 계층 (Layer)로 분리하는 접근 덕분에 어플리케이션 개발자는 집중해야할 대상이 명확해지고 생산성을 높일 수 있게 됩니다.
	또한 어플리케이션 코드와 Service Mesh 영역이 디커플링되어 있기 때문에, 동일한 워크로드를 Istio가 아닌 App Mesh, Consul, Linkerd 등과 연동한다고 하더라도 어플리케이션 코드에서는 변경이 필요없습니다. 이것이 Service Mesh가 제공하는 네번째 가치입니다.

	디커플링:컴포넌트간의 상호의존을 줄인 구성으로 각각의 컴포넌트 변경이나 장애의 영향을 줄인다.

	 L4 Load Balancer와 L7 Load Balancer는 어떤 차이점이 있을까요?
	L4 Load Balancer는 IP, Port 를 기준으로 스케줄링 알고리즘을 통해 부하를 분산합니다.
	클라이언트에서 로드밸런서(DNS)로 요청을 보냈을 때 최적의 서버로 요청을 전송하고 결과를 클라이언트에게 줍니다. 즉, 요청하는 서비스의 종류와 상관 없이 공장을 여러 개 돌리는 것입니다. 

	L7 Load Balancer는 L7 위에서 동작하기 때문에 IP, Port 이외에도 URI, Payload, Http Header, Cookie 등의 내용을 기준으로 부하를 분산합니다. 그래서 콘텐츠 기반 스위칭이라고도 합니다.
	스위치의 관점에서 바라보았을때 L7영역으로 생각할수 있다.
	ISTIO의 Layer로 분리되는 방법이 네트워크스위치의 L4기능이 아닌 L7의 역할을 소화하므로,
	어플리케이션 코드에서의 변경이 필요없는 장점이 생긴다.

