# VPC Lattice란?

## 개념 정의

Amazon VPC Lattice는 여러 VPC와 AWS 계정에 걸친 마이크로서비스 간의 네트워크 연결, 보안, 모니터링을 중앙에서 관리하는 완전관리형 애플리케이션 네트워킹 서비스다. HTTP/HTTPS, gRPC 같은 애플리케이션 계층(Layer 7) 통신을 지원하며, VPC Peering, Transit Gateway 없이도 서비스 디스커버리와 라우팅을 자동으로 처리하고, IAM 기반 접근 제어와 URI/Method 기반 정밀 라우팅을 제공한다.

쉽게 말해 "수십, 수백 개 마이크로서비스가 여러 VPC와 계정에 흩어져 있을 때, 복잡한 네트워크 설정 없이 서비스끼리 자동으로 찾아서 통신하게 해주고, 누가 어떤 API를 호출할 수 있는지 IAM으로 제어하는 애플리케이션 메시"다.

## 핵심 특징

- 멀티 VPC·멀티 계정 서비스 연결 자동화
- Layer 7 애플리케이션 프로토콜 지원 (HTTP/HTTPS, gRPC)
- 서비스 디스커버리 내장 (DNS 자동 라우팅)
- IAM 기반 세밀한 접근 제어
- URI/Method 기반 정밀 라우팅
- VPC Peering/TGW 불필요
- 완전관리형 (인프라 관리 불필요)

## 주요 구성 요소

**Service Network (서비스 네트워크)**
- 여러 서비스를 논리적으로 묶는 애플리케이션 계층 경계
- 서비스 디스커버리와 연결을 자동으로 구현
- 공통 인증 정책과 관찰성 정책 적용
- VPC를 서비스 네트워크에 연결하면 해당 VPC 내 리소스가 네트워크 내 모든 서비스와 통신 가능

**Service (서비스)**
- 실제 애플리케이션 워크로드의 논리적 표현
- Listener와 Target Group으로 구성
- DNS 이름으로 접근 가능
- 여러 서비스 네트워크에 연결 가능

**Listener (리스너)**
- 들어오는 요청을 수신하는 진입점
- 프로토콜(HTTP/HTTPS)과 포트 지정
- Routing Rule 정의

**Routing Rule (라우팅 규칙)**
- URI 패턴, HTTP 메서드, 헤더 기반 라우팅
- 가중치 기반 트래픽 분산 (Blue/Green, Canary)
- 특정 Target Group으로 요청 전달

**Target Group (타겟 그룹)**
- 실제 워크로드를 실행하는 대상
- EC2 인스턴스, IP 주소, Lambda 함수, ALB, ECS 작업, Kubernetes Pod 지원
- Health Check 및 로드 밸런싱

**Auth Policy (인증 정책)**
- IAM 리소스 정책으로 요청 레벨 인증·인가
- 서비스 네트워크 또는 개별 서비스에 적용
- 어떤 Principal이 어떤 서비스/경로에 접근 가능한지 정의

**Service Directory (서비스 디렉터리)**
- 계정 레벨의 중앙 집중식 서비스 레지스트리
- 소유한 서비스 또는 AWS RAM으로 공유받은 서비스 확인
- 서비스 검색 및 관리

## 작동 방식

1. **서비스 네트워크 생성**: 논리적 애플리케이션 경계 정의
2. **VPC 연결**: 서비스가 실행되는 VPC를 서비스 네트워크에 연결
3. **서비스 생성**: 애플리케이션 워크로드를 서비스로 등록
4. **Listener·Routing Rule 설정**: URI/Method 기반 라우팅 규칙 정의
5. **Target Group 연결**: EC2, Lambda, ALB, ECS 등 실제 대상 지정
6. **Auth Policy 적용**: IAM 정책으로 접근 제어
7. **DNS 자동 생성**: 서비스에 고유 DNS 이름 부여
8. **클라이언트 요청**: 클라이언트가 서비스 DNS 이름으로 요청
9. **Route 53 Resolver**: 요청을 VPC Lattice로 전달
10. **VPC Lattice 라우팅**: Routing Rule에 따라 Target Group으로 요청 전달
11. **모니터링**: CloudWatch로 메트릭 및 로그 자동 전송

## 주요 활용 사례

**마이크로서비스 아키텍처 연결**
수백 개의 마이크로서비스가 여러 VPC와 계정에 분산되어 있을 때, VPC Peering/TGW 없이 서비스 네트워크로 자동 연결하고, 서비스 디스커버리로 DNS 기반 통신을 구현하며, IAM으로 서비스 간 인증·인가를 중앙 관리한다.

**Zero Trust 아키텍처**
IP 기반 보안 그룹 대신 IAM 기반 접근 제어를 적용하여 "인증된 서비스만 특정 API 호출 가능"하게 강제하고, Principal 수준의 세밀한 권한 관리로 최소 권한 원칙을 구현하며, 네트워크 경계가 아닌 신원 기반 보안을 달성한다.

**Blue/Green·Canary 배포**
가중치 기반 라우팅으로 트래픽을 신규 버전에 점진적으로 전환하며, 예: 90% 트래픽은 v1, 10%는 v2로 라우팅하고, 문제 발생 시 즉시 롤백하며, 무중단 배포를 간소화한다.

**멀티 테넌트 애플리케이션**
테넌트별로 서비스를 격리하면서도 공통 서비스(인증, 로깅)는 공유하고, Auth Policy로 테넌트별 접근을 제어하며, 서비스 네트워크로 논리적 경계를 구현한다.

**하이브리드·멀티 클라우드 통합**
온프레미스 서비스를 VPC Lattice에 연결하여 클라우드 서비스와 통합하고, 여러 AWS 계정과 리전의 서비스를 단일 서비스 네트워크로 묶으며, 일관된 보안·모니터링 정책을 적용한다.

**실시간 금융 거래·사기 탐지**
ECS Fargate에서 실행되는 여러 마이크로서비스(거래 모니터링, 규칙 기반 스코어링, 알림)를 VPC Lattice로 연결하고, 크로스 클러스터·크로스 계정 통신을 간소화하며, 저지연 실시간 처리를 지원한다.

## 통합 서비스

- Amazon VPC: 서비스가 실행되는 네트워크
- Amazon EC2: Target Group 대상
- AWS Lambda: 서버리스 Target
- Application Load Balancer: Target Group 대상
- Amazon ECS/Fargate: 컨테이너 Target
- Amazon EKS: Kubernetes Pod Target
- AWS RAM: 서비스 네트워크·서비스 공유
- AWS IAM: 접근 제어 정책
- Amazon Route 53: DNS 라우팅
- Amazon CloudWatch: 메트릭·로그
- AWS CloudTrail: API 호출 감사

## VPC Lattice vs App Mesh vs PrivateLink

| 항목 | VPC Lattice | App Mesh | PrivateLink |
|------|-------------|----------|-------------|
| 계층 | Layer 7 애플리케이션 | Layer 7 서비스 메시 | Layer 4 네트워크 |
| 배포 | 완전관리형 (인프라 없음) | Envoy 사이드카 필요 | VPC 엔드포인트 생성 |
| 서비스 디스커버리 | 내장 (DNS 자동) | Cloud Map 통합 | 수동 설정 |
| 접근 제어 | IAM 정책 | mTLS, IAM | 보안 그룹 |
| 라우팅 | URI/Method 기반 | HTTP 헤더/경로 | 포트 기반 |
| 타깃 | EC2, Lambda, ALB, ECS, EKS | EC2, ECS, EKS | EC2, NLB |
| 복잡도 | 낮음 (자동화) | 높음 (사이드카 관리) | 중간 |
| 적합 사례 | 멀티 VPC 마이크로서비스 | 세밀한 트래픽 제어 | 단방향 서비스 노출 |

## 모범 사례

**서비스 네트워크 단위 분리**
프로덕션, 개발, 테스트 환경을 별도 서비스 네트워크로 분리하고, 도메인별(결제, 주문, 배송)로 서비스 네트워크를 나눠 논리적 경계를 명확히 한다.

**IAM Auth Policy 적극 활용**
IP 기반 보안 그룹 대신 IAM 정책으로 "어떤 서비스가 어떤 API 호출 가능"을 정의하고, Principal·경로·메서드별 세밀한 권한을 설정하며, Least Privilege 원칙을 구현한다.

**가중치 라우팅으로 점진적 배포**
신규 버전 배포 시 가중치를 점진적으로 증가시키며, 예: 5% → 25% → 50% → 100%로 트래픽을 이동하고, 모니터링 후 문제 발생 시 즉시 롤백한다.

**Health Check 설정**
Target Group에 Health Check를 설정하여 비정상 대상을 자동 제외하고, 임계값과 간격을 워크로드에 맞게 조정하며, 고가용성을 확보한다.

**CloudWatch 모니터링**
요청 수, 응답 시간, 에러율, 타깃 상태를 CloudWatch로 추적하고, 알람을 설정하여 이상 징후를 조기 감지하며, 대시보드로 서비스 간 통신을 시각화한다.

**RAM으로 서비스 공유**
공통 서비스(인증, 로깅)는 중앙 계정에서 생성하고 RAM으로 공유하며, 다른 계정이 중복 생성 없이 재사용하게 하여 관리 부담을 줄인다.

**DNS 명명 규칙 표준화**
서비스 DNS 이름에 일관된 명명 규칙을 적용하고, 예: payment-service.lattice.internal로 명명하며, 서비스 검색을 용이하게 한다.

**단계적 마이그레이션**
기존 VPC Peering/TGW 환경에서 서비스 하나씩 VPC Lattice로 이전하고, Hybrid 구성으로 안정성을 검증한 후 전면 전환한다.

## 제약 사항

**Layer 7 전용**
HTTP/HTTPS, gRPC만 지원하며, TCP/UDP는 불가능하므로 네트워크 계층은 PrivateLink/TGW 사용이 필요하다.

**리전 제한**
일부 리전에서만 사용 가능하며, 글로벌 서비스가 아니다.

**학습 곡선**
서비스 네트워크, Auth Policy 등 새로운 개념 학습이 필요하며, 기존 네트워킹 패러다임과 다르다.

**비용 발생**
서비스 네트워크, 서비스, 처리된 데이터에 과금되며, 대규모 환경에서는 비용이 상승할 수 있다.

**레거시 애플리케이션 제한**
HTTP/HTTPS 기반이 아닌 레거시 프로토콜은 지원하지 않는다.

## 비용

- 서비스 네트워크: 시간당 과금
- 서비스: 시간당 과금
- 데이터 처리: GB당 과금
- 구체적 요금은 리전별로 상이하며, AWS 공식 요금표 참조 필요

## 요약

VPC Lattice는 여러 VPC와 AWS 계정에 걸친 마이크로서비스 간 네트워크 연결, 보안, 모니터링을 중앙에서 관리하는 완전관리형 애플리케이션 네트워킹 서비스로, VPC Peering이나 Transit Gateway 없이 서비스 디스커버리와 Layer 7 라우팅을 자동으로 처리하고, IAM 기반 접근 제어로 "어떤 서비스가 어떤 API 호출 가능"을 정의하며, URI/Method 기반 정밀 라우팅과 가중치 기반 트래픽 분산으로 Blue/Green·Canary 배포를 간소화하고, EC2, Lambda, ALB, ECS, EKS를 Target으로 지원하며, 마이크로서비스 아키텍처 연결, Zero Trust 보안, 멀티 테넌트 애플리케이션, 하이브리드 클라우드 통합, 실시간 금융 거래 시스템에 최적화되어 있고, App Mesh보다 관리가 간단하지만 세밀한 트래픽 제어는 제한적이며, PrivateLink보다 애플리케이션 계층 기능이 풍부하다.
