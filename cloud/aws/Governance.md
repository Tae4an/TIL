# AWS 거버넌스(Governance)란?

## 개념 정의
AWS 거버넌스는 **조직이 AWS 클라우드를 일관되고 안전하며 비용 효율적으로 사용하도록 규칙·프로세스·조직 구조·도구를 설계하고 운영하는 프레임워크**를 의미한다.  
쉽게 말해 “AWS를 아무나 제멋대로 쓰지 않게, 보안·비용·품질 기준을 정하고 지키게 만드는 운영·관리 체계”다.

## 핵심 목표

- **비즈니스 목표 정렬**: 클라우드 사용이 회사의 전략·규정·보안 정책과 일관되도록 보장.  
- **위험 최소화**: 보안 사고, 규정 위반, 비용 폭주, 아키텍처 품질 저하를 예방.  
- **자율성과 통제 균형**: 팀이 빠르게 구축·실험하면서도, 회사 공통 기준(보안·비용·표준)을 벗어나지 않게 하는 것.

## AWS 거버넌스의 주요 영역

1. **계정·조직 구조 거버넌스**
   - AWS Organizations 기반 멀티 계정 전략 설계(Prod/Dev, 부서, 프로젝트 단위 분리 등).
   - OU(Organizational Unit) 구조 정의: 보안 강도·규정 준수 수준·업무 특성에 따라 계정 묶기.
   - SCP(Service Control Policy)로 계정 단위 상한 권한(사용 가능한 서비스·리전·동작) 정의.

2. **아이덴티티·접근 제어 거버넌스**
   - IAM, SSO, Directory Service 등을 활용한 역할 기반 접근 통제(RBAC) 정립.
   - 최소 권한 원칙, 중앙 계정 로그인, MFA 의무화, Root 사용 금지 정책 등.
   - 크로스 계정 역할(AssumeRole) 전략, 인적 사용자 vs 머신 계정(서비스 계정) 구분.

3. **비용·사용량 거버넌스(FinOps)**
   - 계정/프로젝트/팀/환경별 비용 태깅 전략 수립(예: Project, Owner, Environment 태그).
   - 예산(Budget)·알람, 코스트 리포트, 예약/저축 플랜, 리소스 Rightsizing 정책 운영.
   - 사용하지 않는 리소스 정리 규칙(Off-hours 종료, Zombie 리소스 스캐닝 등).

4. **보안·규정 준수 거버넌스**
   - 보안 기준(Baseline) 정의: VPC 구조, 보안 그룹 기본 정책, 암호화 필수 여부, 로깅 요구사항 등.
   - Config, CloudTrail, GuardDuty, Security Hub, KMS 등을 활용한 표준 보안 스택 정의.
   - 규정 준수(ISO, SOC, HIPAA 등)에 맞춘 데이터·접근·로깅·보존 정책.

5. **리소스 표준·아키텍처 거버넌스**
   - 표준 아키텍처 패턴(3-Tier, Serverless, Microservice, Data Lake 등) 카탈로그화.
   - 표준 VPC 템플릿, 보안 그룹, IAM Role, Logging 구성을 IaC(CloudFormation/Terraform/CDK)로 제공.
   - Change Management, 리뷰 프로세스(예: 중요 워크로드는 아키텍처 리뷰 필수).

6. **로깅·모니터링·감사 거버넌스**
   - 모든 계정/리전에서 CloudTrail·Config·CloudWatch Logs 필수 활성화.
   - 중앙 로깅 계정으로 로그 집계, 변경 추적 및 보안 이벤트 상시 모니터링.
   - 운영·보안·비용 관련 대시보드 및 알람 표준화.

7. **데이터 거버넌스**
   - 데이터 분류(공개/내부/민감/기밀) 정책, S3·RDS·Redshift·Lake Formation 기반 접근 제어.
   - 데이터 수명주기 관리(보존 기간, 아카이빙, 삭제).
   - 데이터 소유자·관리자(Owner/Steward) 역할 정의와 승인 프로세스.

## AWS 거버넌스에 자주 쓰이는 서비스

- **조직·계정 관리**
  - AWS Organizations, Control Tower(계정/OU 기본 세트+Landing Zone)
- **정책·보안·감사**
  - IAM, IAM Identity Center(SSO), Config, CloudTrail, GuardDuty, Security Hub, KMS
- **비용 관리**
  - AWS Budgets, Cost Explorer, Cost and Usage Report, Savings Plans/Reserved Instances
- **리소스 표준화/IaC**
  - CloudFormation, CDK, Service Catalog, Systems Manager(패치/구성 관리)
- **데이터 거버넌스**
  - Lake Formation, Glue Data Catalog, S3/Bucket Policy, Macie(민감 데이터 탐지)

## 거버넌스 수준별 접근 방식

1. **기본 수준(스타트업·소규모 팀)**
   - 단일 또는 소수 계정 + 기본 IAM 정책 + 최소한의 비용 알람.
   - 필수 로깅(CloudTrail, Config)과 보안 기초(Baseline SG, KMS)만 우선 정립.

2. **중간 수준(여러 팀·환경 존재)**
   - 멀티 계정 구조(Prod/Non-prod, 팀별 계정) + Organizations + SCP.
   - 표준 VPC/IaC 템플릿, 공통 모니터링·알람, 태깅·비용 관리 정착.

3. **고급 수준(엔터프라이즈)**
   - Control Tower 기반 Landing Zone, 중앙 IAM/SSO, 광범위한 SCP·정책 세트.
   - 데이터 거버넌스, 규정 준수, 감사 대응 프로세스와 자동화까지 통합.

## 실무적인 설계 팁 (요약)

- **먼저 구조부터**: 계정/OU 구조, 기본 VPC 설계, 태깅 규칙을 먼저 정해놓고 시작.
- **모든 변경은 코드로**: CloudFormation/Terraform/CDK 기반 IaC로 표준 아키텍처 제공.
- **“허용 리스트” 방식**: SCP·IAM·네트워크 정책을 “허용된 것만 가능한” 방향으로 설계.
- **가시성 확보**: 로깅·모니터링·비용 리포트·대시보드를 최우선으로 구축.
- **정책은 가볍게 시작 후 발전**: 처음부터 과도하게 복잡하게 만들지 말고, 최소 기준 → 점진적 강화.

AWS 거버넌스는 특정 하나의 서비스가 아니라, 조직이 AWS를 쓰는 전체 방식(계정 구조, 권한, 비용, 보안, 데이터, 운영 표준)을 설계·운영하는 프레임워크이기 때문에, 아키텍트 관점에서 “조직의 규칙과 자동화된 가드레일을 어떻게 배치할지”를 중심으로 생각하면 정리하기 좋다.
