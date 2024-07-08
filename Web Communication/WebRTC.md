# WebRTC

<br>

> 웹 애플리케이션과 사이트가 중간자 없이 브라우저 간에 오디오나 영상 미디어를 포착하고 마음대로 스트림할 뿐 아니라, 임의의 데이터도 교환할 수 있도록 하는 기술

<br>

## 시그널링

---

> 두 피어(브라우저) 간의 연결을 설정하기 위해 네트워크 정보를 교환하는 단계

- WebRTC 지원 기능이 아닌 WebRTC 연결 전 미리 준비해야 하는 과정

<br>

### 단계
#### 1. SDP 제안/응답 : 한 피어가 특정한 미디어 스트림을 교환할 것을 제안하고 상대 피어도 동의하면 응답
- `SDP(Session Description Protocol)` : 스트리밍 미디어의 초기화 인수(해상도, 형식, 코덱 등)를 기술하기 위한 포맷

<br>

#### 2. ICE 후보 교환/결정 : ICE는 두 피어의 P2P 연결을 가능하게 하도록 최적의 경로를 찾음
- `ICE(Interactive Connectivity Establishment) : P2P 네트워킹에서 두 컴퓨터가 가능한 한 직접적으로 서로 통신할 수 있는 방법을 찾기 위해 컴퓨터 네트워킹에 사용되는 기술
- 방화벽(Firewall), 여러 대의 컴퓨터가 하나의 공인 IP를 공유하는 `NAT(Network Address Translation)`, 유휴 상태의 IP를 일시적으로 임대받는 `DHCP(Dynamic Host Configuration Protocol)`으로 인해 공인 IP만으로 특정 사용자를 알 수 없음
- `NAT 트레버셜(NAT Traversal)` : P2P통신을 하기 위해서 단말기가 NAT를 우회하여 단말기의 IP정보를 알아내 서로 통신을 하는 기술
  * `STUN(Session Traversal Utilities for NAT)` : 클라이언트는 STUN 서버에 요청을 보내고, STUN 서버는 클라이언트의 공인 IP 주소와 포트를 응답
    - 대역폭과 서버 부담이 상대적으로 적지만 모든 유형의 NAT에서 작동하는 것은 아님(Symmetric NAT)
  * `TURN (Traversal Using Relays around NAT)` : 두 피어 간의 직접적인 P2P 연결이 불가능한 경우 데이터를 중계 서버를 통해 전송하기 위해 사용
     - 모든 유형의 NAT에서 작동하지만 대역폭과 서버 부담이 상대적으로 큼
- `ICE 후보` : 피어 간의 연결을 설정하기 위해 수집된 잠재적 네트워크 경로로 이 중 최적의 경로가 선택됨
   * `Local Address` : 클라이언트의 사설주소(Private IP, Port), 동일한 로컬 네트워크 내의 피어 간의 연결을 설정하는 데 사용
   * `Server Reflexive Address` : NAT 가 매핑한 클라이언트의 공인망(Public IP, Port), NAT 라우터 뒤에 있는 장치가 외부 네트워크와의 연결을 설정하는 데 사용
   * `Relayed Address` : TURN 서버에 연결하여 중계되는 IP 주소와 포트, 다른 모든 후보를 사용한 직접 연결이 실패할 경우 데이터를 TURN 서버를 통해 중계
   * ICE 후보는 일반적으로 Local Address, Server Reflexive Address, Relayed Address 순으로 선택
 - `Trickle ICE` : 두 개의 피어가 ICE 후보를 수집하고 교환하는 과정을 병렬 프로세스로 수행할 수 있게 하는 기술
 - NAT의 보안 이슈 등으로 최선의 ICE 후보를 찾지 못할 경우 폴백으로 세팅한 TURN 서버를 P2P 대용으로 설정

> P2P 연결 설정 및 활성화, 각 피어에 의해 로컬 데이터 스트림의 엔드포인트가 생성되며, 이 데이터는 양방향 통신 기술을 사용하여 양방향으로 전송

<br>

## 단점
 - 모든 브라우저와 호환 되지 않음(IE, 사파리 등)
 - 시그널링 서버에 대한 명시적인 표준이 없음
 - UDP 위에서 동작하므로 데이터 전송 속도는 빠르지만 데이터 손실이 발생할 수 있음
