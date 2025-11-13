# AWS Transit Gateway란?

## 개념 정의
AWS Transit Gateway는 AWS에서 제공하는 **클라우드 네트워크 연결의 허브 역할을 하는 완전 관리형 네트워크 게이트웨이 서비스**다. 수많은 VPC, 온프레미스 네트워크, 계정 간 네트워크를 중앙에서 안전하고 효율적으로 연결해 대규모 클라우드/하이브리드 네트워크 아키텍처를 쉽게 구축할 수 있다.

쉽게 말해 “VPC, 온프레미스, 여러 계정 간 네트워크 인터페이스를 한 번에 통합·최적화하는 네트워크 연결 관제센터”다.

## 핵심 특징

**중앙 집중 네트워크 관리**
- 여러 VPC, 계정, 온프레미스 네트워크를 단일 Transit Gateway에 연결(Attachment)
- 각 VPC/네트워크를 직접 피어링하지 않고 허브/스포크 구조로 단순 연결

**확장성 및 고가용성**
- 수십~수백 개 이상의 VPC, 온프레미스, 리전 간 네트워크 동시 연결
- AWS에서 자동 확장·중복성·고가용성 관리

**정책 및 분리**
- 라우팅 테이블을 Transit Gateway 단위로 여러 개 생성, 네트워크별 분리 및 정책 간편 적용
- 트래픽 흐름, 네트워크 분리, 접근 제어 모두 게이트웨이에서 설계 가능

**온프레미스·멀티리전 지원**
- Direct Connect Gateway 및 VPN Gateway와 연동해 하이브리드 네트워크 통합
- Transit Gateway Peering으로 글로벌 리전 연결(멀티리전 네트워크 구현)

**네트워크 단순화·비용 절감**
- 기존 VPC Peering 대비 연결 구조/보안/관리 단순화
- 복잡한 경로/피어링 구성 대신 단일 허브에서 트래픽 최적화

## 주요 구성 요소

- **Transit Gateway**: 모든 네트워크의 연결 허브로 역할 수행
- **Attachment**: Transit Gateway에 연결된 VPC, VPN, Direct Connect, 기타 네트워크
- **Route Table**: Transit Gateway 단위로 트래픽 경로 및 접근 정책 설계
- **Multi-Account 연동**: 여러 AWS 계정 VPC를 단일 게이트웨이에 손쉽게 연결 및 중앙 관리
- **Peering**: 리전 간 Transit Gateway 간 연결(글로벌 네트워크 구성)

## 기존 VPC Peering vs Transit Gateway 비교

|           | VPC Peering             | Transit Gateway           |
|-----------|-------------------------|---------------------------|
| 구조      | 노드별 직접 연결         | 중앙 허브 연결(허브·스포크)|
| 확장성    | 수십 개 연결 한계        | 수백 개 이상 간편 연결     |
| 관리      | 피어링 수만큼 경로/ACL  | 게이트웨이에서 일괄 정책   |
| 온프레미스| 불편/제한적             | Direct/VPN 게이트웨이 연동 |
| 비용      | 경로·관리 복잡           | 관리 비용 절감, 구조 단순화|

## 주요 기능

- VPC/VPN/Direct Connect/온프레미스 네트워크 동시 연결 및 트래픽 분배
- 라우팅 테이블로 격리·정책·네트워크 흐름 제어(예: 앱·DB·관리망 분리)
- Route 53/CIDR·DNS·보안그룹/네트워크 ACL과 통합 운용
- Transit Gateway Connect로 SD-WAN/3rd-party 네트워크 인프라 연결 지원
- Multi-Region Peering으로 글로벌 네트워크 허브 구축

## 주요 활용 사례

- 대규모 조직의 여러 VPC를 중앙 Transit Gateway로 효율적으로 연결
- AWS 계정·프로젝트별 VPC 및 온프레미스 네트워크 접속, 확장 관리
- 하이브리드 클라우드(클라우드+온프레미스) 통합 네트워크 설계
- 보안망, 운영망, 개발망 등 네트워크 분리 및 정책적 분산 관리
- 글로벌 서비스 리전 간 네트워크 트래픽 안정적 분산·운용

## 기본 워크플로

### 1. Transit Gateway 생성 및 VPC 연결
```bash
aws ec2 create-transit-gateway --description "Central Network Hub"
aws ec2 create-transit-gateway-vpc-attachment \
  --transit-gateway-id tgw-xxxx \
  --vpc-id vpc-xxxx \
  --subnet-ids subnet-xxx
```

### 2. 온프레미스 및 VPN, Direct Connect 연결
- Transit Gateway에 온프레미스 VPN, Direct Connect Gateway Attachment 생성

### 3. 라우팅 테이블·정책 설계
- Transit Gateway 각 Route Table 생성 → Attachment마다 적용
- 트래픽/Security/Audit Policy 단일 허브에서 통제

### 4. Multi-Region Peering 연결
```bash
aws ec2 create-transit-gateway-peering-attachment \
  --transit-gateway-id tgw-xxx \
  --peer-transit-gateway-id tgw-yyy \
  --peer-region ap-northeast-2
```

## 비용 구조

- Transit Gateway 자체 요금(Attachment당 시간/트래픽량 기준)
- Peering, VPN, Direct Connect, Route Table 등 사용량별 추가 과금
- 구조 단순화로 관리 비용 절감 효과

## 보안 및 운영 모범 사례

- 네트워크·서브넷 분리 및 최소 경로/접근 정책 적용
- 라우팅 테이블마다 RBAC, IAM, 로그·감사 활성화
- 모든 Attachment·경로·보안 정책 IaC로 자동화 및 버전 관리
- 트래픽 모니터링, Network Firewall, GuardDuty 등 보안 서비스 연동
- 글로벌/하이브리드 네트워크 복잡도 대폭 감소 및 관리 일관성 강화

AWS Transit Gateway는 클라우드·하이브리드·글로벌 네트워크 아키텍처 설계에서 연결·확장·보안·운영 모두를 효율적으로 통합하는 핵심 네트워크 솔루션이다.
