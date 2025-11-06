# TCP/IP
### TCP/IP란?
TCP/IP (Transmission Control Protocol / Internet Protocol)
는 인터넷 통신의 기반이 되는 프로토콜(통신 규약)들의 집합이다.

TCP/IP의 목적은 서로 다른 종류의 컴퓨터, 운영체제, 네트워크 간에도
데이터를 표준화된 방식으로 주고받게 하는 것 이다.

## TCP/IP 4계층 구조
| 계층                                       | 역할                      | 대표 프로토콜                     | 데이터 단위                         |
| ---------------------------------------- | ----------------------- | --------------------------- | ------------------------------ |
| **L4  응용 계층 (Application Layer)**         | 사용자와 직접 통신              | HTTP, HTTPS, FTP, DNS, SMTP | 메시지(Message)                   |
| **L3 전송 계층 (Transport Layer)**           | 송신-수신 간 신뢰성, 데이터 흐름 제어  | TCP, UDP                    | 세그먼트(Segment), 데이터그램(Datagram) |
| **L2 인터넷 계층 (Internet Layer)**           | IP 주소 지정, 라우팅           | IP, ICMP, ARP               | 패킷(Packet)                     |
| **L1  네트워크 접근 계층 (Network Access Layer)** | 실제 물리 전송 (LAN, Wi-Fi 등) | Ethernet, Wi-Fi, MAC        | 프레임(Frame), 비트(Bit)            |

## 데이터 전송 과정
TCP/IP의 핵심 개념중 하나는 바로 캡슐화와 디캡슐화 이다.  

```
**데이터가 전송될 때**  

응용 계층 데이터
    ↓
전송 계층 (TCP 헤더 추가)           -> 세그먼트
    ↓
인터넷 계층 (IP 헤더 추가)          -> 패킷
    ↓
네트워크 계층 (Ethernet 헤더 추가)  -> 프레임 
```
수신 측에서는 이 과정을 역순으로 진행하여 각 계층이 헤더를 제거하고 상위 계층에 데이터를 전달한다.  
패킷 안에는 또다른 패킷이 들어갈수 있다. 
L2프레임 안에 L3패킷이 들어갈수 있고 L3패킷 안에는 L4 Tcp Segment가 들어갈 수 있다.
그리고 패킷이나 프레임같은 것들을 열어서 안의 Payload를 꺼내는 것을 Decapsulation이라고 한다.

## 응용 계층 (L4)
#### 데이터 단위
- 메시지(Message)
#### 하는 일
- 사용자/프로그램이 바로 쓰는 응용 프로토콜로 통신 규칙을 정의
- 전송계층(TCP/UDP)을 호출해 메시지(payload) 를 내려보냄
- 표현/세션 기능(인코딩, 압축, 대화 유지 등)을 프로토콜 내부에서 함께 처리하는 경우가 많음
#### 핵심 메커니즘
- HTTP: 메서드(GET/POST/PUT/DELETE), 상태코드, 헤더/본문, 캐싱(ETag, Cache-Control)
- DNS: 이름 → IP 매핑, 재귀/반복 질의, 캐시 TTL
- HTTP/2/3: 멀티플렉싱, 헤더 압축(HPACK/QPACK), 0-RTT(QUIC)
- 보안: TLS(HTTPS), 인증(쿠키/토큰/OAuth), CORS

## 전송 계층 (L3)
#### 데이터 단위
- TCP 세그먼트 / UDP 데이터그램
#### 하는 일
- 송신–수신 끝단(end-to-end) 사이의 데이터 전송 품질을 보장/조절
- 포트 번호로 프로세스(소켓) 식별, 세그먼트화/재조립
#### 대표 프로토콜
- TCP: 연결지향(3-way handshake), 순서/무결성 보장, 혼잡제어(Reno/CUBIC/BBR), 흐름제어, 재전송
- UDP: 비연결, 순서/재전송 없음 → 지연이 치명적인 실시간 서비스에 유리(DNS, VoIP, 게임)
#### 핵심 메커니즘
- TCP
  - 연결: 3-way handshake, 종료: FIN/ACK (또는 RST)
  - 신뢰성: 시퀀스/ACK, 타이머 기반 재전송, 슬라이딩 윈도우
  - 혼잡제어: Slow start → Congestion avoidance → Fast retransmit/recovery
  - 성능: Nagle, Delayed ACK, 윈도우 스케일링, SACK, TLS 오버헤드
- UDP
  - 오버헤드 작음, 손실/순서 보장 없음 → 앱 레벨에서 보정 필요(FEC, 재전송 로직)

## 인터넷 계층 (Internet Layer) (L2)
#### 데이터 단위
- 패킷(Packet)
#### 하는 일
- IP 주소 지정과 라우팅으로 패킷을 목적지 네트워크까지 전달
- 최적 경로 선택, 단편화(IPv4), 오류/진단(ICMP)

## 네트워크 접근 계층 (Network Access / Link + Physical) (L1)
#### 데이터 단위
- 프레임(Frame) / 비트(Bit)
#### 하는 일
- 동일 로컬 링크 내에서 프레임 전송, 매체 접속 제어(MAC), 오류 검출(FCS)
- 물리 계층(전기/광/무선 신호) 포함한 실제 전송
#### 핵심 메커니즘
- MTU/MSS: 링크 MTU(보통 1500), 터널/암호화 시 오버헤드로 패킷 단편화/손실 가능
- VLAN: 논리적 네트워크 분리, 태깅/트렁크 포트
- Half/Full Duplex, 속도 협상: 링크 품질/충돌 도메인
