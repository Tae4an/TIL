# Azure Virtual Network(VNet)란?

## 개념 정의
Azure Virtual Network(VNet)는 Microsoft Azure에서 제공하는 **클라우드 기반 가상 네트워크 플랫폼**이다. 클라우드 리소스(가상머신, 데이터베이스, 여러 서비스 등)가 서로 안전하게 통신하고, 온프레미스 네트워크나 인터넷과도 연결할 수 있는 네트워크 격리 환경을 제공한다.

쉽게 말하면 “Azure 클라우드 안에서 구축하는 나만의 안전한 네트워크”로, 온프레미스 네트워크와 유사하게 IP, 서브넷, 경로, 방화벽, VPN 등을 자유롭게 설계하며, 네트워크 보안과 확장성을 동시에 만족시킬 수 있다.

## 핵심 특징

**논리적 분리와 네트워크 격리**
- 클라우드 리소스를 VNet 단위로 격리(서버, DB, App 등)
- 서브넷(Subnet)으로 내부 네트워크 세분화, Security 및 역할 분리

**IP·라우팅 제어**
- 사설/공인 IP 자유 지정 및 DHCP 자동 할당
- 라우팅 테이블/사용자 정의 경로(UdR)로 네트워크 흐름 제어

**보안 및 제어 관리**
- 네트워크 보안 그룹(NSG)으로 인바운드/아웃바운드 트래픽 통제
- Azure Firewall, Application Gateway, Bastion, DDoS Protection 등 추가 보안 서비스 연동

**하이브리드 연결 지원**
- 온프레미스와 VPN Gateway, ExpressRoute 등 다양한 방법으로 안전한 연결
- 여러 VNet을 Peering(피어링)으로 연결해 리소스 간 통신 확장
- 글로벌 VNet Peering 지원(리전 간 통합 네트워크 구현)

**확장성 및 고가용성**
- 수백~수천 개 리소스 확장 배치, 멀티 서브넷·멀티 리전 활용 가능
- 네트워크 트래픽 모니터링(Azure Monitor, Network Watcher) 내장

## 주요 구성 요소

- **VNet**: 네트워크의 논리적 최상위 컨테이너, IP 대역·DNS·라우팅·서브넷·NSG 등 포함
- **서브넷(Subnet)**: VNet 내 세부 네트워크, 부서/역할/보안/환경별 분리 설계
- **NSG(Network Security Group)**: 트래픽 필터링 규칙 집합, 인스턴스/서브넷에 적용
- **라우팅 테이블**: 경로 및 인터페이스 지정, 사용자 정의 가능
- **VPN Gateway/ExpressRoute**: 온프레미스와의 안전한 연결(암호화, 고가용성)
- **VNet Peering**: 여러 VNet 간 빠른 내부 통신
- **DNS/Private DNS**: 클라우드 리소스명 해석, 도메인 기반 접근 관리

## 기존 온프레미스 네트워크 vs Azure VNet 비교

|         | 온프레미스 네트워크      | Azure VNet            |
|---------|-------------------------|-----------------------|
| 구축    | 직접 하드웨어/운영       | 클릭 몇 번/자동화      |
| IP      | 직접 할당               | 범위·자동·서브넷 단위  |
| 라우팅  | 수동 구성               | 테이블, UdR, 피어링   |
| 보안    | 하드웨어 방화벽/ACL      | NSG, Gateway, DDoS    |
| 확장성  | 물리적 한계             | 리전·서브넷 무제한     |
| 연결    | 온프레미스, MPLS 등      | VPN, ExpressRoute     |
| 모니터링| 별도 솔루션 필요         | Azure 내장 도구        |

## 주요 기능

- 서브넷단 네트워크 분리·자동화
- NSG로 트래픽·보안 정책 모듈화
- VPN, ExpressRoute로 하이브리드 환경 통합
- 피어링(VNet Peering)으로 네트워크 확장·비용 최적화
- Azure Bastion 등으로 보안성 높은 내부 접근 환경 제공
- Network Watcher로 구성/성능/로그 실시간 모니터링

## 주요 활용 사례

- 다양한 환경(dev/stage/prod)별 VNet 분리·배포
- 온프레미스-클라우드 하이브리드 환경 통신
- 멀티 리전 글로벌 네트워크 아키텍처 구성
- 대규모 PaaS/SaaS 서비스의 네트워크 확장·관리
- 내부 보안 정책 및 접근제어 자동화(Zero Trust 구현)

## 기본 워크플로

### 1. VNet 생성
```bash
az network vnet create \
  --name myvnet \
  --resource-group mygroup \
  --address-prefix 10.0.0.0/16
```
- 서브넷은 `--subnet-name`, `--subnet-prefix` 옵션으로 추가 지정

### 2. NSG·라우팅 테이블 구성
```bash
az network nsg create --name mynsg --resource-group mygroup
az network vnet subnet update \
  --vnet-name myvnet \
  --name mysubnet \
  --network-security-group mynsg
```

### 3. 하이브리드 연결(VPN Gateway) 예시
```bash
az network vnet-gateway create \
  --resource-group mygroup \
  --name mygateway \
  --public-ip-address mygateway-ip \
  --vnet myvnet \
  --gateway-type Vpn
```

## 비용 구조

- VNet 자체는 무료
- Gateway, ExpressRoute, Bastion 등 추가 네트워크 리소스별 요금은 별도 과금
- 데이터 전송/트래픽/보안 서비스 등은 사용량 기반 과금

## 보안 모범 사례

- 환경·용도별 VNet 분리, 서브넷 단위 최소 권한 NSG 적용
- 모든 인바운드 접근은 NSG, Firewall, Bastion 등 보안 계층 추가
- 하이브리드 연결 시 암호화·DDoS Protection 적극 활용
- 트래픽·변경 로그 Network Watcher·Monitor로 실시간 분석 및 알람 설정

Azure Virtual Network(VNet)는 클라우드 인프라의 네트워크 기반을 완성시켜주는 핵심 서비스로, 보안, 확장성, 하이브리드 환경, 자동화 네트워크 운영을 위해 반드시 이해해야 할 주요 개념이다.
