# 글로벌 서버의 백 엔드 아키텍처를 위한 기술적 솔루션

## 두 게임 서버의 백 엔드 아키텍처의 장점과 단점은 지난 호에서 설명한 바와 같습니다. 이번에는 글로벌 서버의 기술적인 솔루션 중 하나를 소개합니다.

1. 글로벌 서버 아키텍처

<img width="386" alt="image" src="https://user-images.githubusercontent.com/92770458/142786226-b68fdaad-f9a8-4d67-80df-b32168376fe9.png">

글로벌 서버에서는 전 세계 플레이어가 동일한 서버군에 접속해야 합니다. 최대 과제는 지리적으로 다른 지역에 있는 플레이어의 네트워크 지연입니다. 특히 네트워크 수준이 낮은 지역에서는 패킷 손실이나 네트워크 변동에 의해 매우 불안정한 상태가 될 수 있습니다.
따라서 글로벌 서버를 실현하기 위한 주요 기술적 수단으로서 서로 다른 지역에 있는 플레이어가 배틀 서버에 액세스할 때의 네트워크 품질과 안전성을 향상시키는 네트워크 액셀러레이션을 채용하고 있습니다.
글로벌 서버, 네트워크 액셀러레이션 솔루션에는 크게 2가지 아이디어가 있습니다.

## 1. 물리적으로 가능한 한 플레이어 가까이에 서버를 설치
## 2. 장거리 공중 회선 전송을 안정적인 인트라넷 전송으로 변경

카테고리 I, 플레이어와 서버의 물리적 거리를 단축
예를 들어 일반적인 CDN 기술로는 화상 등의 일부 정적 콘텐츠를 고속화할 수 있습니다. 텐센트 게임즈는 CDN+COS를 사용하여 정적 콘텐츠를 고속화합니다.

카테고리 II, 장거리 공중 회선 전송을 자체 제작한 인트라넷 전송으로 변경
예: 
텐센트 클라우드: 	글로벌 애플리케이션 액셀러레이션 GAAP	https://cloud.tencent.com/product/gaap

텐센트 클라우드: 	Anycast Public Network Acceleration AIA	https://cloud.tencent.com/product/aia

AWS: 	Global Accelerator	https://amazonaws-china.com/cn/global-accelerator/

이들 제품은 모두 독자적인 인트라넷을 사용하여 지역 간 액셀러레이션을 실현하고 있습니다.
텐센트 클라우드에 대한 지식이 어느 정도 있으므로 텐센트 클라우드를 예로 들어 그 원리를 간단히 소개하겠습니다.
GAAP(Global Application Acceleration Platform)는 비즈니스에 최적화된 글로벌 액세스 레이턴시를 실현하는 PAAS 제품입니다. 글로벌 노드 간의 고속 채널, 포워딩 클러스터, 인텔리전트 라우팅 기술을 이용하여 로컬 액세스를 실현하고 트래픽을 소스 사이트에 전송하여 글로벌 사용자의 액세스 러그나 고속 레이턴시 문제를 해결합니다.
초기에 IEG 게임은 중국의 플레이어가 미국의 AWS에 액세스할 수 있도록 해야만 가속이 가능했으므로 Fullnat+toa를 사용한 TCP 프록시 포워딩과 IP 패스 스루 탑재라는 해결책을 제안했습니다. 이 솔루션은 ‘GAAP’ 제품을 개발한 텐센트 클라우드가 서서히 구축 및 개발한 것입니다.

<img width="404" alt="image" src="https://user-images.githubusercontent.com/92770458/142786292-20a0c4a2-0c04-4fe8-99cf-9c45df78be04.png">


GAAP는, 고속 채널의 입구와 소스 에어리어의 출구로 한 쌍의 포워딩 클러스터를 배치하고 장거리 공중 네트워크 전송의 중간부를 자체 제작 인트라넷 전송으로 변경하여 네트워크의 안전성을 향상시키는 심플한 원리입니다.

<img width="405" alt="image" src="https://user-images.githubusercontent.com/92770458/142786305-3a3ea363-9cff-4cbe-9a2d-f869623d7366.png">

GAAP의 최대 장점은 클라이언트와 서버가 모두 텐센트 클라우드상에 없는 머신이라도 텐센트 클라우드의 노드와 충분히 가까운 장소에 있기만 하면 된다는 점입니다. 이러한 장점과 관련하여 위 그림의 게임 글로벌 서버 이외에 전형적으로 실용할 수 있는 것으로 온라인 게임의 액셀러레이터가 있습니다. 클라이언트는 실제 사용자이고, 소스 사이트는 AWS 등 텐센트 클라우드 이외의 머신입니다.

인트라넷의 고속화를 실현하는 다른 한 가지 툴은 애니캐스트 기술입니다. 예를 들어 텐센트 클라우드의 AIA 제품인 ‘Anycast Internet Acceleration’이 있습니다.
AIA의 원리도 매우 심플한데, 애니캐스트(Anycast 기술)로만 동일한 IP 주소가 여러 지역에서 라우팅되므로 클라이언트의 트래픽은 공중 네트워크를 통해 가장 가까운 텐센트 클라우드의 노드까지 간 후 텐센트 클라우드의 인트라넷을 통해 타깃 주소에 도달하는 것입니다.
텐센트 클라우드의 Anycast IP는 텐센트 클라우드의 백 엔드 서버나 clb 등의 제품에 바인딩할 수 있으며, 일반적인 IP와 마찬가지로 간단하면서도 간편하게 사용할 수 있습니다.
AIA는 GAAP에 비해 도입이나 설정이 간단합니다. 글로벌하면서 독특한 IP, 그리고 DNS에 의존하지 않는 명확한 아키텍처가 있고 설정은 간편합니다. 하지만 AIA의 백 엔드 업무는 텐센트 클라우드상의 머신에 한정되며, 서드 파티의 서버에 액세스할 때는 추가 설정이 필요합니다.
 
 <img width="386" alt="image" src="https://user-images.githubusercontent.com/92770458/142786321-b6fa0dad-c75a-4743-8441-5b1402778720.png">

