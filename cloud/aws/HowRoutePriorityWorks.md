# AWS VPC 라우팅 테이블 우선순위 처리 메커니즘

## 개요
AWS VPC에서 트래픽은 가장 구체적인 경로를 사용하여 라우팅된다. 이를 **가장 긴 접두사 일치(Longest Prefix Match)** 라고 하며, 라우팅 테이블에 겹치거나 일치하는 경로가 있을 때 추가 규칙이 적용된다.

## 라우팅 우선순위 결정 순서

AWS는 다음 4단계 순서로 라우팅 우선순위를 결정한다.

### 1. 가장 긴 접두사 일치 (Longest Prefix Match) - 최우선
트래픽과 일치하는 가장 구체적인 경로를 사용한다.

- `10.10.2.15/32`는 `10.10.2.0/24`보다 우선순위가 높다
- `172.31.0.0/16`과 `0.0.0.0/0` 중에서는 `172.31.0.0/16`이 더 구체적이므로 우선 적용된다

**예시**
```
| 대상          | 타겟                      |
|---------------|---------------------------|
| 10.0.0.0/16   | local                     |
| 172.31.0.0/16 | pcx-11223344556677889     |
| 0.0.0.0/0     | igw-12345678901234567     |
```
- `172.31.0.0/16` 대역 트래픽은 피어링 연결로 라우팅된다
- VPC 내부(`10.0.0.0/16`) 트래픽은 local 경로를 사용한다
- 나머지 모든 트래픽은 인터넷 게이트웨이로 라우팅된다

### 2. 정적 경로 (Static Routes)
동일한 CIDR 블록에 대해 정적 경로가 전파된 경로보다 우선한다.

**정적 경로를 사용하는 리소스**
- Internet Gateway
- NAT Gateway
- Network Interface
- Instance ID
- Gateway VPC Endpoint
- Transit Gateway
- VPC Peering Connection
- Gateway Load Balancer Endpoint

**예시**
```
| 대상            | 타겟                  | 전파   |
|-----------------|-----------------------|--------|
| 10.0.0.0/16     | local                 | 아니요 |
| 172.31.0.0/24   | vgw-11223344556677889 | 예     |
| 172.31.0.0/24   | igw-12345678901234567 | 아니요 |
```
- 동일한 `172.31.0.0/24` 대상에서 정적 경로(IGW)가 전파된 경로(VGW)보다 우선한다
- 결과적으로 `172.31.0.0/24`로 향하는 모든 트래픽은 인터넷 게이트웨이로 라우팅된다

### 3. 접두사 목록 경로 (Prefix List Routes)
접두사 목록을 참조하는 경로가 전파된 경로보다 우선한다.

**적용 규칙**
- 라우팅 테이블에 접두사 목록을 참조하는 경로와 일치하는 전파된 경로가 있으면, 접두사 목록 참조 경로가 우선 적용된다
- 겹치는 경로의 경우 전파/정적/접두사 목록 여부와 관계없이 **더 구체적인 경로가 항상 우선**한다
- 여러 접두사 목록이 겹치는 경우 AWS가 무작위로 선택하며, 이후 동일한 경로가 계속 우선 적용된다

### 4. 전파된 경로 (Propagated Routes)
동적으로 전파된 경로 간의 우선순위는 다음과 같다

1. Direct Connect BGP 경로 (동적 경로)
2. VPN 정적 경로
3. VPN BGP 경로 (동적 경로, Virtual Private Gateway)

## 중요 원칙

### IPv4와 IPv6의 독립적 평가
IPv4 및 IPv6 주소 또는 CIDR 블록에 대한 경로는 서로 독립적이다. AWS는 IPv4 트래픽 또는 IPv6 트래픽과 일치하는 가장 구체적인 경로를 각각 별도로 판단하여 트래픽 라우팅 방법을 결정한다.

### 구체성이 최우선
전파된 경로인지, 정적 경로인지, 접두사 목록 참조 경로인지와 관계없이 **더 구체적인 경로가 항상 우선**한다.

### Local 경로의 최우선성
Site-to-Site VPN 연결 또는 AWS Direct Connect 연결에서 전파된 경로가 VPC의 로컬 경로와 겹치는 경우, 전파된 경로가 더 구체적이더라도 **로컬 경로가 가장 우선**한다.

## 참고 문서
AWS 공식 문서: "How route priority works" - https://docs.aws.amazon.com/vpc/latest/userguide/route-tables-priority.html

한글 버전: https://docs.aws.amazon.com/ko_kr/vpc/latest/userguide/route-tables-priority.html

VPN 라우팅 우선순위: https://docs.aws.amazon.com/ko_kr/vpn/latest/s2svpn/vpn-route-priority.html
