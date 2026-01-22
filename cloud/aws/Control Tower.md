# AWS Control Tower란?

## 개념 정의
AWS Control Tower는 AWS Organizations 기반으로 멀티 계정 환경을 자동으로 구성하고 중앙에서 거버넌스 정책(Guardrails)을 일관되게 적용하는 관리형 서비스로, 보안·컴플라이언스 모범 사례를 반영한 Landing Zone을 자동 생성하고 Account Factory로 표준화된 계정 프로비저닝을 제공한다.

쉽게 말해 "수십~수백 개의 AWS 계정을 안전하고 일관되게 관리할 수 있도록, 자동으로 계정 구조를 만들고 보안 규칙을 강제하며, 새 계정을 표준화된 방식으로 생성할 수 있게 해주는 멀티 계정 거버넌스 서비스"다.

## 핵심 기능

### Landing Zone (착륙 지점)
AWS 모범 사례를 반영한 멀티 계정 환경 기반 구조로, 관리 계정, 보안 계정(Audit), 로그 계정(Log Archive), OU(Organizational Unit) 구조를 자동으로 생성하고 중앙 집중식 로깅·감사·SSO를 구성한다.

### Guardrails (가드레일)
계정 전체에 일관되게 적용되는 거버넌스 규칙으로, 예방형 가드레일(SCP로 특정 작업 차단)과 탐지형 가드레일(AWS Config로 규정 위반 감지)로 나뉘며, OU 단위로 적용된다.

### Account Factory
표준화된 템플릿으로 새 AWS 계정을 자동 생성하며, 가드레일·네트워크·SSO 권한을 자동 적용하고 Service Catalog를 통해 셀프 서비스 계정 프로비저닝을 제공한다.

### 중앙 집중식 로깅·감사
모든 계정의 CloudTrail 로그를 로그 계정(Log Archive)에 중앙 집중화하고, AWS Config 변경 이력을 보안 계정(Audit)에 집계하며, GuardDuty·Security Hub 등을 위임 관리자로 통합한다.

### SSO 통합
AWS IAM Identity Center(구 SSO)로 단일 자격 증명으로 모든 계정에 접근하고, RBAC 기반 권한 관리 및 외부 IdP 연동(Azure AD, Okta 등)을 지원한다.

## 주요 구성 요소

### 1. Landing Zone 기본 계정 구조

#### Management Account (관리 계정)
AWS Organizations의 루트 계정으로, Control Tower·Organizations·Account Factory를 실행하며, 워크로드는 배치하지 않고 관리 용도로만 사용한다.

#### Log Archive Account (로그 계정)
모든 계정의 CloudTrail 로그·VPC Flow Logs·Config 스냅샷 등을 중앙 집중 저장하며, 변조 방지를 위해 읽기 전용 접근만 허용한다.

#### Audit Account (보안 감사 계정)
AWS Config·CloudTrail·GuardDuty·Security Hub 등 보안 서비스의 위임 관리자 계정으로, 전체 조직의 보안·컴플라이언스 상태를 모니터링한다.

### 2. Organizational Units (OU)

#### Foundational OU (Security, Infrastructure)
Log Archive·Audit 계정을 포함하는 핵심 OU로, 조직 전체의 보안·로깅 기반을 제공한다.

#### Custom OU (Workloads)
실제 워크로드를 실행하는 계정들을 그룹화하며, 팀·프로젝트·환경(Dev, Test, Prod)별로 구분한다.

### 3. Guardrails (가드레일)

#### Preventive Guardrails (예방형)
SCP(Service Control Policy)로 특정 API 작업을 차단하며, 정책 위반을 사전에 방지한다.

**예시**
- CloudTrail 로깅 비활성화 금지
- 루트 사용자 MFA 강제
- S3 버킷 공개 액세스 차단
- 특정 리전 외 리소스 생성 금지 (Region Deny)

#### Detective Guardrails (탐지형)
AWS Config Rules로 리소스 상태를 지속 모니터링하고, 규정 위반 시 알림을 보내며, 사후 대응이 필요하다.

**예시**
- EBS 볼륨 암호화 미적용 감지
- Security Group 과도한 포트 개방 감지
- IAM 비밀번호 정책 위반 감지

#### Mandatory vs Strongly Recommended vs Elective
- **Mandatory**: 자동 활성화, 비활성화 불가 (CloudTrail 활성화 등)
- **Strongly Recommended**: AWS 권장, 수동 활성화
- **Elective**: 선택적 적용

### 4. Account Factory
Service Catalog 기반 셀프 서비스 포털로, 표준화된 계정 생성 템플릿을 제공하고 계정 생성 시 자동으로 가드레일·네트워크·SSO 권한을 적용하며, Terraform과 통합 가능하다.

## 작동 방식

### 1. Landing Zone 설정
Control Tower 콘솔에서 "Set up Landing Zone" 클릭 시 자동으로 Management, Log Archive, Audit 계정과 Security·Sandbox OU를 생성하고, CloudTrail·Config·SSO를 구성하며, 기본 Guardrails를 적용한다.

### 2. OU 생성 및 Guardrails 적용
커스텀 OU(예: Production, Development)를 생성하고, OU 단위로 Guardrails를 활성화하면 해당 OU 내 모든 계정에 자동 적용된다.

### 3. Account Factory로 계정 생성
Service Catalog에서 계정 생성 요청 시 표준 템플릿으로 계정이 프로비저닝되고, 지정된 OU로 자동 배치되며, Guardrails·로깅·SSO가 자동 적용된다.

### 4. 컴플라이언스 모니터링
Dashboard에서 전체 조직의 Guardrails 준수 상태를 실시간 확인하고, 위반 발생 시 SNS·EventBridge로 알림을 받으며, Audit 계정에서 Security Hub·Config로 종합 분석한다.

## 주요 활용 사례

### 대규모 조직의 멀티 계정 거버넌스
수백 개 계정을 운영하는 대기업이 중앙에서 보안·컴플라이언스 정책을 일관되게 강제하고, 팀별·프로젝트별 계정을 표준화된 방식으로 프로비저닝한다.

### 규제 산업 컴플라이언스
금융·의료·공공 등 규제가 엄격한 산업에서 GDPR·HIPAA·PCI DSS 요구사항을 Guardrails로 자동 적용하고, 감사 로그를 중앙 집중 보관한다.

### 데이터 레지던시 (Region Deny)
Region Deny Guardrail로 특정 리전(예: EU만 허용) 외에서 리소스 생성을 차단하여 데이터 주권 요구사항을 충족한다.

### 계정 프로비저닝 자동화
개발팀이 셀프 서비스로 새 계정을 요청하면 Account Factory가 자동으로 표준 보안 설정이 적용된 계정을 생성하고, 승인 워크플로우를 통합할 수 있다.

## 통합 AWS 서비스

- **AWS Organizations**: 계정 구조·OU·SCP 관리
- **AWS IAM Identity Center (SSO)**: 중앙 집중식 인증·권한
- **AWS CloudTrail**: 전체 계정 API 로깅
- **AWS Config**: 리소스 변경 추적·규정 준수
- **AWS Service Catalog**: Account Factory 포털
- **AWS CloudFormation**: Landing Zone 인프라 자동화
- **Amazon SNS**: Guardrails 위반 알림
- **AWS Security Hub**: 보안 상태 종합 대시보드

## Control Tower vs Organizations

| 항목 | Control Tower | Organizations |
|------|--------------|---------------|
| 계층 | Organizations 위에 구축된 추상화 계층 | 기본 멀티 계정 관리 |
| 자동화 | Landing Zone·Account Factory 자동 | 수동 구성 필요 |
| Guardrails | 사전 정의된 규칙 제공 | SCP 직접 작성 필요 |
| 로깅·감사 | 자동 중앙 집중화 | 수동 구성 필요 |
| SSO | 자동 통합 | 별도 설정 필요 |
| 적합 사례 | 빠른 구축·표준화 중시 | 세밀한 커스터마이징 필요 |

## 비용
- Control Tower 자체는 무료
- 단, 생성된 리소스(CloudTrail, Config, SNS 등)에 대한 AWS 서비스 비용 발생
- 일반적으로 계정당 월 $5~$20 정도 추가 비용

## 모범 사례

### Management Account 격리
Management Account에는 워크로드를 배치하지 않고 관리 용도로만 사용하며, SCP가 적용되지 않으므로 루트 계정 접근을 최소화한다.

### OU 구조 설계
비즈니스 단위·환경(Dev/Test/Prod)·규정 요구사항별로 OU를 설계하고, 각 OU에 적절한 Guardrails를 적용한다.

### Guardrails 단계적 적용
Mandatory Guardrails부터 시작해 Strongly Recommended를 점진적으로 활성화하며, 조직 요구사항에 맞춰 Elective Guardrails를 선택한다.

### 기존 조직에 Control Tower 적용
기존 Organizations가 있어도 Control Tower를 활성화할 수 있으나, Management Account에 워크로드가 없고 구조가 단순할 때 권장한다.

### 정기 모니터링
Dashboard에서 Guardrails 위반·드리프트(수동 변경) 상태를 주기적으로 점검하고, 자동 알림을 설정한다.

## 요약
AWS Control Tower는 AWS Organizations 위에서 멀티 계정 환경을 자동 구성하고 중앙 거버넌스를 제공하는 서비스로, Landing Zone(Management·Log Archive·Audit 계정 + OU 구조)을 자동 생성하고, Guardrails(예방형 SCP·탐지형 Config Rules)를 OU 단위로 일관 적용하며, Account Factory로 표준화된 계정 프로비저닝과 SSO 통합을 제공하고, CloudTrail·Config·Security Hub로 중앙 집중식 로깅·감사를 구현하며, Region Deny로 데이터 레지던시를 강제하고, 대규모 조직 거버넌스·규제 컴플라이언스·계정 자동화에 활용된다.
