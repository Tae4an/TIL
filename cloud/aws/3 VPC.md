# AWS 3 VPC 아키텍처란?
## 1. 개념 요약
AWS 3 VPC 아키텍처는 **Ingress VPC, Egress VPC, Inspection VPC(또는 Workload VPC) 3개의 VPC를 분리해 역할을 나누는 네트워크 보안 설계 패턴**이다.  
**들어오는 트래픽, 나가는 트래픽, 보안 검사를 각각 다른 VPC에서 담당**하게 해 중앙 집중적이고 세밀한 트래픽 제어를 가능하게 한다.

***
## 2. VPC 3개의 역할
- **Ingress VPC (인바운드 관문)**
  - 외부 인터넷에서 들어오는 트래픽을 최초로 수신
  - WAF, ALB, 방화벽 등 인바운드 보안 장치를 여기에 배치

- **Egress VPC (아웃바운드 관문)**
  - 내부에서 인터넷으로 나가는 트래픽의 단일 출구
  - NAT Gateway, 프록시, 아웃바운드 방화벽을 여기에 배치

- **Inspection VPC (보안 검사)**
  - VPC 간(East-West) 트래픽, 온프레미스↔AWS 트래픽을 검사
  - AWS Network Firewall, Suricata, 3rd-party IPS/IDS를 여기에 배치

***
## 3. 어디에 쓰이는가
- **엔터프라이즈 멀티 VPC 환경**
  - 수십~수백 개 VPC의 모든 트래픽을 3개 VPC로 통제

- **보안 컴플라이언스**
  - 모든 인바운드·아웃바운드를 반드시 검사해야 하는 금융·공공기관

- **AWS Organizations + Transit Gateway 환경**
  - Transit Gateway로 모든 VPC를 3 VPC 허브에 연결하는 허브앤스포크 구조

- **Zero Trust 네트워크 설계**
  - VPC 간 트래픽도 신뢰하지 않고 무조건 Inspection VPC를 경유하게 설계

***
## 4. 트래픽 흐름
| 방향 | 경로 |
|---|---|
| **인바운드 (North-South ↓)** | 인터넷 → Ingress VPC → Transit Gateway → Workload VPC |
| **아웃바운드 (North-South ↑)** | Workload VPC → Transit Gateway → Egress VPC → 인터넷 |
| **VPC 간 (East-West ↔)** | Workload VPC → Transit Gateway → Inspection VPC → Transit Gateway → 대상 VPC |

모든 경로에서 Transit Gateway가 중심 허브 역할을 하며, 트래픽이 반드시 지정된 VPC를 거치도록 라우팅 테이블로 강제한다.

***
## 5. 한 줄 정리
AWS 3 VPC 아키텍처는 **Ingress·Egress·Inspection VPC로 역할을 분리**해 모든 인바운드·아웃바운드·VPC 간 트래픽을 중앙에서 검사하고 제어하는 "엔터프라이즈급 보안 네트워크 설계 패턴"이다.
