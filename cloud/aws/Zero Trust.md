# AWS Zero Trust란?

## 개념 정의

Zero Trust는 "**절대 신뢰하지 말고, 항상 검증하라(Never Trust, Always Verify)**"는 원칙에 기반한 보안 모델이다. 기존 경계 기반 보안이 "내부 네트워크에 있으면 신뢰"했던 것과 달리, Zero Trust는 위치(내부/외부)에 관계없이 **모든 요청·사용자·디바이스·서비스를 매번 검증**하고, 최소 권한만 부여하며, 지속적으로 모니터링한다.

쉽게 말해, "VPN 안에 있다고 믿지 않고, IAM 역할이 있다고 무조건 믿지 않으며, 모든 접근을 신원·컨텍스트·상태 기반으로 검증하는 보안 사고방식"이다.

## Zero Trust의 3대 핵심 원칙

### 1. 명시적 검증 (Verify Explicitly)
- 네트워크 위치만으로 신뢰 부여 금지
- 신원(Identity), 디바이스 상태, 위치, 서비스, 워크로드, 데이터 분류 등 모든 컨텍스트를 기반으로 인증·인가
- 1회 로그인으로 세션 내내 신뢰하지 않고, 이상 징후 감지 시 재검증

### 2. 최소 권한 (Least Privilege Access)
- 사용자·서비스·애플리케이션에 필요한 최소한의 권한만 부여
- Just-In-Time(JIT) 액세스, Just-Enough-Access(JEA) 구현
- 권한 경계·SCP로 위임 권한 상한선 설정

### 3. 침해 가정 (Assume Breach)
- 이미 침해가 발생했다는 전제하에 설계
- Blast Radius 최소화 (계정 분리, 마이크로세그멘테이션)
- 모든 트래픽 암호화, 지속적 모니터링, 이상 행동 자동 탐지

## AWS Zero Trust 구현 핵심 서비스

### Identity (신원 검증)
- **IAM Identity Center**: 중앙 집중식 직원 신원 관리, SSO, MFA 강제
- **IAM 역할·정책**: 최소 권한 기반 접근 제어, Permissions Boundary
- **AWS Cognito**: 애플리케이션 사용자 인증 (외부 사용자)
- **AWS IAM Roles Anywhere**: 온프레미스·멀티 클라우드 환경에서도 IAM 역할 사용

### Network (네트워크 제어)
- **VPC + Security Group + NACL**: 마이크로세그멘테이션, 불필요한 네트워크 경로 제거
- **VPC Lattice**: 서비스 간 IAM 기반 접근 제어, 네트워크 위치 대신 신원 기반 통신
- **AWS Verified Access**: VPN 없이 IAM·신원 기반으로 내부 애플리케이션 접근 제어
- **Network Firewall + WAF**: 경계 필터링

### Data (데이터 보호)
- **AWS KMS**: 저장 데이터 암호화, 키 접근 세밀 제어
- **Secrets Manager**: 자격 증명 저장·자동 로테이션
- **Macie**: S3 민감 데이터 자동 탐지·분류
- **VPC Endpoint**: 인터넷 통하지 않고 AWS 서비스에 프라이빗 접근

### Monitoring (지속적 모니터링)
- **CloudTrail**: 모든 API 호출 감사 로그 기록
- **GuardDuty**: 비정상 행동·위협 지능형 탐지
- **Security Hub**: 보안 상태 통합 대시보드
- **IAM Access Analyzer**: 외부 공개된 리소스·미사용 권한 탐지
- **Amazon Detective**: 보안 이상 탐지 심층 조사

## 기존 보안 모델 vs Zero Trust

| 항목 | 기존 경계 기반 보안 | Zero Trust |
|------|-------------------|------------|
| 신뢰 기준 | 네트워크 위치 (내부 = 신뢰) | 신원·컨텍스트 기반 (위치 무관) |
| VPN | 내부망 접근의 핵심 수단 | 불필요 (신원 기반 접근) |
| 권한 | 광범위한 접근 허용 | 최소 권한, JIT 액세스 |
| 모니터링 | 경계 진입 후 최소 감시 | 모든 요청 지속 모니터링 |
| 침해 가정 | 내부는 안전하다 가정 | 이미 침해됐다 가정 |
| 횡적 이동 | 내부망 내 자유로운 이동 | 마이크로세그멘테이션으로 차단 |

## AWS 환경에서 Zero Trust 구현 방법

### 1. Identity 강화
- 모든 IAM 사용자에 MFA 강제 적용
- 루트 계정 일상 사용 금지, 루트 MFA 설정
- 액세스 키 최소화, 역할(Role) 기반 임시 자격 증명 사용
- IAM Access Analyzer로 과도한 권한 주기적 탐지·제거

### 2. 네트워크 마이크로세그멘테이션
- VPC, Subnet, Security Group을 세밀하게 분리
- 서비스 간 통신에 VPC Lattice + IAM Auth Policy 적용
- 불필요한 VPC Peering·인터넷 게이트웨이 제거
- VPC Endpoint로 AWS 서비스에 프라이빗 접근

### 3. 애플리케이션 접근 제어
- AWS Verified Access로 VPN 없이 신원 기반 내부 앱 접근 구현
- 애플리케이션별 접근 정책 정의, 디바이스 상태 조건 추가

### 4. 데이터 암호화·분류
- 전송 중(TLS)/저장 중(KMS) 암호화 강제
- Macie로 민감 데이터 탐지·분류
- Secrets Manager로 자격 증명 자동 로테이션

### 5. 지속적 모니터링·자동 대응
- GuardDuty + Security Hub로 위협 지속 탐지
- CloudTrail 로그 전체 활성화 (모든 리전)
- 이상 징후 탐지 시 EventBridge + Lambda로 자동 격리

### 6. 멀티 계정 전략
- 환경별·워크로드별 계정 분리 (Blast Radius 최소화)
- SCP로 계정 레벨 최대 권한 상한선 설정
- Organizations + Control Tower로 중앙 거버넌스 구현

## 정리

AWS Zero Trust는 "위치가 아닌 신원으로 신뢰를 판단한다"는 원칙하에, 명시적 검증, 최소 권한, 침해 가정의 세 원칙을 IAM Identity Center, VPC Lattice, Verified Access, GuardDuty, KMS, Secrets Manager, Security Hub 등 AWS 네이티브 서비스로 구현하는 보안 전략이며, VPN 의존도를 줄이고 신원·컨텍스트 기반 접근 제어와 지속적 모니터링으로 Blast Radius를 최소화하고, 멀티 계정 전략과 마이크로세그멘테이션으로 침해 발생 시 횡적 이동을 차단한다.
