# Ingress VPC란?

## 1. 개념 요약

Ingress VPC는 **외부 인터넷에서 AWS 내부로 들어오는 인바운드 트래픽을 최초로 수신·제어하는 전용 관문 VPC**다.  
쉽게 말해, **"외부에서 오는 모든 트래픽이 가장 먼저 거쳐야 하는 입구 VPC"** 로, 보안 검사와 라우팅을 여기서 집중 처리한다.

***

## 2. 특징

- **인바운드 트래픽 단일 진입점**
  - 모든 외부 트래픽이 이 VPC를 통해서만 내부로 진입
  - 워크로드 VPC는 인터넷과 직접 연결하지 않음

- **보안 장치 집중 배치**
  - WAF, ALB, AWS Network Firewall, IPS/IDS 등 인바운드 보안 도구를 여기에 모아 관리
  - 중앙에서 일괄 적용 → 정책 일관성 보장

- **VPC Ingress Routing 활용**
  - IGW(인터넷 게이트웨이)로 들어오는 트래픽을 라우팅 테이블로 강제로 미들박스(방화벽)에 먼저 보내는 구조
  - 내부 서브넷이 인터넷과 직접 통신하지 않음

- **Transit Gateway와 연동**
  - Ingress VPC → Transit Gateway → Workload VPC 경로로 트래픽을 전달

***

## 3. 어디에 쓰이는가

- **3 VPC 아키텍처의 입구 역할**
  - Ingress VPC → (검사) → Workload VPC 구조에서 첫 번째 VPC로 사용

- **중앙 집중 보안 정책**
  - 여러 워크로드 VPC가 있어도 인바운드 보안 정책을 한 곳에서 관리

- **DDoS 방어 및 WAF 적용**
  - Shield, WAF 등 인바운드 보호 도구를 단일 Ingress VPC에만 배포해 비용·복잡도 절감

- **규정 준수**
  - 외부에서 들어오는 모든 트래픽을 기록·검사해야 하는 금융·공공기관 환경

***

## 4. Ingress / Egress / Inspection VPC 역할 비교

| 항목 | Ingress VPC | Egress VPC | Inspection VPC |
|---|---|---|---|
| **트래픽 방향** | 외부 → 내부 (인바운드) | 내부 → 외부 (아웃바운드) | VPC 간 (East-West) |
| **주요 장치** | WAF, ALB, 방화벽 | NAT Gateway, 프록시 | IDS/IPS, Network Firewall |
| **역할** | 외부 공격 차단 | 아웃바운드 필터링 | 내부 트래픽 검사 |
| **인터넷 연결** | IGW 있음 | IGW 있음 (NAT) | IGW 없음 |

***

## 5. 한 줄 정리

Ingress VPC는 **외부 인터넷에서 내부 AWS 환경으로 진입하는 모든 트래픽의 단일 입구**로, WAF·방화벽·IPS를 중앙 집중 배치해 인바운드 보안을 일괄 통제하는 "북쪽 관문 VPC"다.
