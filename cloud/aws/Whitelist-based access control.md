# AWS 화이트리스트 기반 접근 제어

## 개념 정의

AWS에서 화이트리스트 기반 접근 제어는 **허용된 주체(IP, 네트워크, 계정, 도메인 등)만 접근을 허용하고 나머지는 기본 차단하는 방식의 접근 통제**를 의미한다.  
IP allow list, 보안 그룹 허용 규칙, WAF IP Set, 리소스 정책(Principal 제한) 등이 모두 화이트리스트 패턴에 해당함.  

***

## 주요 구현 방식 개요

- Security Group 기반 IP/보안그룹 화이트리스트  
- AWS WAF IP Set 기반 화이트리스트  
- 서비스별 리소스 정책(예: S3, API Gateway, OpenSearch 등)에서 Principal / Source IP 화이트리스트  
- WorkSpaces, 프런트엔드 서비스 등에서 IP Access List(Allow List) 구조 사용  

각 방식은 레이어와 적용 대상이 다름. EC2/VPC 레벨이면 Security Group, L7 HTTP 트래픽이면 WAF, 서비스 API 권한이면 리소스 정책이 기본 선택지.  

***

## 1. Security Group 기반 화이트리스트

Security Group은 VPC 내 리소스(EC2, RDS, ALB 등)에 붙는 **가상 방화벽** 역할을 한다.  
기본 동작은 “명시적으로 허용된 트래픽만 허용, 그 외는 암묵적으로 차단”이므로 구조 자체가 화이트리스트 개념에 가깝다.  

**구성 방법**

- 인바운드 규칙에서 다음만 허용  
  - 프로토콜: TCP/UDP/ICMP 등 최소 필요 프로토콜만  
  - 포트: 22(SSH), 443(HTTPS) 등 필요한 포트만  
  - 소스: 특정 IP 또는 CIDR(예: `203.0.113.10/32`, `203.0.113.0/24`)  
- 나머지 모든 IP/포트는 별도 Deny 규칙 없이도 자동 차단됨.  

**보안그룹 간 화이트리스트**

- 소스 IP 대신 **다른 Security Group ID**를 소스로 지정 가능.  
  - 예:  
    - Web 서버 SG: `sg-web`  
    - DB 서버 SG: `sg-db`  
  - `sg-db` 인바운드에 소스로 `sg-web`을 지정 → web 인스턴스만 DB 접근 허용.  

**특징**

- 인스턴스/리소스 단위 접근 제어.  
- 오토스케일링, IP 변경에도 SG 기준으로 제어 가능.  
- IP 화이트리스트 + SG 화이트리스트 혼합 사용 가능.  

***

## 2. AWS WAF 기반 IP 화이트리스트

웹 트래픽(HTTP/HTTPS) 레벨에서 특정 IP/대역만 허용하려면 AWS WAF에서 **IP Set + Allow Rule** 패턴을 사용한다.  

**구성 개념**

- IP Set: 허용할 IP/대역 목록(CIDR 표기)  
- Web ACL Rule:  
  - 조건: “IP 주소가 지정한 IP Set에 포함”  
  - 액션: Allow  
- 기본 액션(Default action)을 Block으로 설정  
  → IP Set에 포함된 요청만 통과, 나머지는 차단 = 화이트리스트 정책.  

**적용 대상**

- CloudFront  
- Application Load Balancer (ALB)  
- API Gateway / AppSync / Cognito / App Runner 등 WAF 연동 가능한 L7 엔드포인트  

**특징**

- HTTP 헤더, URI, 쿼리스트링, 쿠키 등과 조합해 복합 조건도 가능.  
- 관리형 WAF 규칙과 함께 사용해,  
  “화이트리스트 IP라도 특정 공격 패턴은 차단” 같은 정책도 설계 가능.  

***

## 3. 리소스 정책 기반 화이트리스트

S3, API Gateway, OpenSearch, SNS, SQS 등 일부 서비스는 **리소스 기반 정책(Resource Policy)** 에서 접근 가능한 주체/조건을 직접 정의할 수 있다.  

**대표 패턴**

- Condition으로 **Source IP 제한**  
  - 예: S3 버킷 정책에서 `aws:SourceIp` 조건을 사용해 특정 IP/대역만 허용.  
- Principal으로 **허용 계정/Role만 명시**  
  - 예: “이 버킷은 계정 A, Role X,Y만 접근 가능” → 계정/Role 화이트리스트.  

**특징**

- VPC 밖에서도 서비스 레벨에서 직접 접근 제어 가능.  
- Security Group/WAF와 조합해 다중 계층 화이트리스트 설계 가능.  

***

## 4. 서비스별 전용 Allow List 기능 예

- WorkSpaces IP Access Control Group  
  - 사용자 단말의 출발지 IP 기준으로 WorkSpaces 콘솔/세션 접속 허용/차단.  
- Databricks on AWS, SaaS형 콘솔 등  
  - 자체 IP Access List에서 허용 IP만 콘솔/API 접근 허용.  

이들 역시 원리는 “허용된 IP 목록 외에는 차단”이라는 화이트리스트 접근 제어.  

***

## 5. 설계 시 고려 사항 및 모범 사례

**1) 최소 권한·최소 개방 원칙**

- 필요 포트·프로토콜·IP 범위만 열기.  
- 관리용(SSH/RDP)는 회사 고정 IP, VPN, Bastion 호스트 IP만 허용.  

**2) 계층별 통제**

- VPC 레벨: Security Group / NACL  
- L7 웹 레벨: AWS WAF IP Set  
- 서비스 레벨: 리소스 정책(SourceIp, Principal)  
- 사용자/역할 레벨: IAM 정책  

각 계층에서 가능한 한 좁게 허용 범위 설계.  

**3) 운영·유지관리**

- 화이트리스트 변경(신규 IP 추가, 폐기 IP 제거)을 표준 변경 프로세스로 관리.  
- 여러 계정/리전에 동일 리스트를 써야 한다면,  
  - AWS Firewall Manager,  
  - IaC(Terraform/CloudFormation)로 중앙 관리.  

***

요약하면, AWS에서 화이트리스트 기반 접근 제어는  
Security Group, AWS WAF, 리소스 정책, 서비스별 IP Access List 등을 활용해  
“허용된 IP/네트워크/주체만 접근 가능, 기본은 차단” 구조를 구현하는 것을 의미한다.
