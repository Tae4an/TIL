# Egress VPC란?

## 개념 정의

Egress VPC는 **여러 VPC(또는 온프레미스/지사)의 “인터넷으로 나가는(outbound) 트래픽”을 한 곳으로 모아 처리하는 전용 VPC 패턴**을 말한다.  
즉, 각 업무 VPC에는 인터넷 게이트웨이·NAT를 두지 않고, 공용 NAT·보안 장비·로깅을 모아 둔 전용 VPC를 하나 만든 뒤 Transit Gateway(또는 Cloud WAN, VPC Peering 등)로 연결해, 모든 아웃바운드 인터넷 트래픽을 그 VPC를 통해서만 나가게 하는 구조다.  

***

## 구조 개요

흔히 다음과 같이 구성한다.

- 업무 VPC들  
  - 퍼블릭 서브넷·인터넷 게이트웨이 없음(완전 프라이빗 또는 사설 통신만).  
  - 기본 경로 `0.0.0.0/0` → Transit Gateway/Cloud WAN 등으로 향함.  

- Egress VPC  
  - 퍼블릭/프라이빗 서브넷, Internet Gateway(IGW), NAT Gateway 등을 보유.  
  - 프라이빗 서브넷에서 Transit Gateway로부터 트래픽 수신,  
    라우트 테이블을 통해 NAT Gateway → IGW로 인터넷으로 나가게 구성.  

- Transit Gateway(또는 유사 허브)  
  - 각 업무 VPC와 Egress VPC를 허브-스포크 형태로 연결.  
  - “업무 VPC → TGW → Egress VPC → NAT → IGW → 인터넷” 흐름을 만든다.  

AWS Managed Services(AMS) 문서에서도 Egress VPC를  
“여러 네트워크의 아웃바운드 인터넷 트래픽을 수용하기 위해, 퍼블릭/프라이빗 서브넷과 NAT Gateway, Transit Gateway 연결로 구성된 전용 VPC”라고 정의한다.  

***

## 왜 쓰는지 (목적)

- NAT/보안 장비 중앙 집중  
  - 각 VPC마다 NAT Gateway, 프록시, 방화벽, IDS 등을 따로 두는 대신,  
    Egress VPC에만 집중 배치해서 비용과 운영 복잡도를 줄인다.  
- 일관된 보안 정책  
  - 모든 인터넷 아웃바운드가 한 VPC를 거치므로,  
    AWS Network Firewall, 프록시, IDS/IPS, 로깅/모니터링을 한 곳에서 적용 가능.  
- 규제·감사 대응  
  - “어디서 어떤 목적지로 나갔는지”를 단일 경로에서 로깅할 수 있어, 감사·포렌식이 쉬움.  

***

## Egress VPC vs Egress-Only Internet Gateway

- Egress VPC  
  - “아웃바운드 트래픽을 전용 VPC 하나로 중앙 집중시키는 네트워크 아키텍처 패턴”.  
  - 여러 VPC의 IPv4/IPv6 트래픽, NAT, 방화벽, 프록시, 로깅까지 포함하는 큰 설계 개념.  

- Egress-Only Internet Gateway  
  - 단일 VPC 안에서 **IPv6** 전용 아웃바운드 인터넷 게이트웨이 컴포넌트.  
  - 인바운드는 막고, 아웃바운드 IPv6만 허용하는 VPC 리소스.  

이 둘은 이름은 비슷하지만,  
Egress VPC는 “아키텍처 패턴(전용 VPC)”,  
Egress-Only Internet Gateway는 “VPC 리소스 타입”이라는 점이 다르다.
