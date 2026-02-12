# AWS Perimeter Security란?

## 개념 정의

AWS Perimeter Security는 **애플리케이션이 인터넷과 만나는 경계(Perimeter)** 에서 악의적인 트래픽·공격·비인가 접근을 차단하는 보안 아키텍처 및 서비스 조합이다. CloudFront, WAF, Shield, Route 53, Global Accelerator, Network Firewall 같은 AWS 엣지 및 네트워크 서비스를 사용해, OWASP Top 10 공격, DDoS, 봇, 지리적 차단, 애플리케이션 계층 공격을 방어하고, 악성 트래픽이 VPC·애플리케이션에 도달하기 전에 차단한다.

쉽게 말해, "인터넷에 노출된 AWS 리소스(웹/API) 앞단에 여러 보안 계층을 쌓아, 공격 트래픽이 애플리케이션 코어 네트워크에 들어오기 전에 엣지에서 걸러내는 경계 방어 전략"이다.

## 핵심 구성 서비스

### Amazon CloudFront
- 글로벌 엣지 로케이션에서 콘텐츠 전송
- WAF와 연계하여 엣지에서 공격 차단
- DDoS 공격 흡수 및 지리적 제한

### AWS WAF (Web Application Firewall)
- Layer 7 웹 공격 차단 (SQL Injection, XSS, 봇 등)
- CloudFront, ALB, API Gateway에 배포
- 관리형 규칙·사용자 정의 규칙으로 트래픽 필터링

### AWS Shield
- **Shield Standard**: 모든 AWS 고객에게 무료 제공, Layer 3/4 DDoS 자동 방어
- **Shield Advanced**: 대규모 DDoS 완화, 실시간 공격 알림, DDoS Response Team(DRT) 지원, 비용 보호

### Amazon Route 53
- DNS 기반 트래픽 라우팅 및 Health Check
- DDoS 공격에 강한 Anycast 네트워크
- 지리 기반 라우팅·장애 조치로 가용성 강화

### AWS Global Accelerator
- Anycast IP로 전 세계 엣지에서 트래픽 수신
- 네트워크 계층에서 DDoS 방어
- 애플리케이션 엔드포인트로 최적 경로 라우팅

### AWS Network Firewall
- VPC 경계에서 네트워크 트래픽 검사
- IDS/IPS, TLS 검사, 지리 IP 필터링
- Stateful·Stateless 규칙으로 Ingress/Egress 트래픽 제어

### AWS Firewall Manager
- 멀티 계정·멀티 리전에서 WAF, Shield, Security Group, Network Firewall 정책을 중앙 관리
- 자동 적용·자동 복구로 정책 준수 강제

## Perimeter Security 아키텍처 패턴

### 집중형 경계 (Centralized Perimeter)
**구조**  
- 중앙 Network 계정에 CloudFront·WAF·Shield를 배포
- 모든 워크로드 계정이 중앙 경계를 통해 인터넷 트래픽 처리

**장점**  
- 중앙 보안팀이 일관된 정책 관리
- 비용·리소스 효율적

**단점**  
- 워크로드 팀의 유연성 제한
- 중앙 변경 시 모든 워크로드 영향

### 분산형 경계 (Distributed Perimeter)
**구조**  
- 각 워크로드 계정이 자체 CloudFront·WAF·Shield 배포
- Firewall Manager로 공통 기본 정책 강제, 워크로드별 추가 규칙 허용

**장점**  
- 워크로드 팀이 독립적으로 보안 규칙 관리
- 공격 영향이 특정 워크로드로 격리

**단점**  
- 자동화·거버넌스 필요
- Config·CloudTrail로 준수 여부 지속 모니터링 필요

## 주요 활용 사례

### DDoS 방어
Shield Standard + Shield Advanced + CloudFront로 Layer 3/4/7 DDoS를 엣지에서 흡수하고, 애플리케이션 오리진 보호

### OWASP Top 10 방어
WAF 관리형 규칙(Core Rule Set)로 SQL Injection, XSS, CSRF, Path Traversal 등 자동 차단

### 봇 관리
WAF Bot Control로 악의적 봇·스크래퍼 차단, CAPTCHA로 사람 검증

### 지리적 차단 (Geo-Blocking)
CloudFront·WAF·Route 53으로 특정 국가에서의 접근 차단, 규정 준수 달성

### 콘텐츠 전송 보호
CloudFront + OAI(Origin Access Identity)로 S3 직접 접근 차단, CloudFront를 통해서만 콘텐츠 제공

### 하이브리드 환경 보호
온프레미스 오리진을 CloudFront로 보호하고, WAF로 웹 공격 차단하여 데이터센터 부하 감소

## Data Perimeter vs Network Perimeter

### Network Perimeter (네트워크 경계)
- **경계**: 인터넷과 VPC 간 네트워크 경계
- **서비스**: CloudFront, WAF, Shield, Network Firewall
- **목적**: 악성 트래픽 차단, DDoS 방어

### Data Perimeter (데이터 경계)
- **경계**: 신뢰할 수 있는 Identity·리소스·네트워크만 데이터 접근 허용
- **서비스**: IAM, SCP, VPC Endpoint Policy, Resource Policy
- **목적**: 의도하지 않은 데이터 접근·유출 방지, Zero Trust 구현

## 모범 사례

### 다층 방어 (Defense in Depth)
CloudFront + WAF + Shield + Network Firewall + Security Group을 조합해 여러 계층에서 방어

### 엣지 우선 필터링
악성 트래픽을 CloudFront 엣지에서 먼저 차단하여 오리진 부하·비용 감소

### Firewall Manager 중앙 관리
멀티 계정 환경에서 Firewall Manager로 공통 정책 자동 배포·강제

### Shield Advanced 전략적 사용
인터넷 진입점(CloudFront, ALB)만 Shield Advanced로 보호, 중복 보호로 인한 비용 낭비 방지

### 지속적 모니터링
CloudWatch, GuardDuty, Security Hub로 경계 보안 상태 모니터링, 이상 징후 조기 탐지

### 자동화·IaC
Terraform, CloudFormation으로 경계 보안 인프라를 코드화하여 일관성·재현성 확보

## 정리

AWS Perimeter Security는 CloudFront, WAF, Shield, Route 53, Global Accelerator, Network Firewall을 조합하여 애플리케이션과 인터넷 경계에서 DDoS, 웹 공격, 봇, 지리적 위협을 차단하는 방어 전략이며, 집중형·분산형 아키텍처 중 선택 가능하고, Firewall Manager로 멀티 계정 중앙 관리하며, 엣지에서 트래픽을 먼저 필터링하여 오리진 보호와 비용 절감을 동시에 달성하고, Data Perimeter(Identity 기반 접근 제어)와 함께 사용하여 네트워크·데이터 양 측면의 경계 보안을 구현한다.
