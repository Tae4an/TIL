# AWS Blast Radius란?

## 개념 정의

Blast Radius(폭발 반경)는 AWS 환경에서 **보안 침해, 장애, 잘못된 변경이 발생했을 때 영향을 받는 범위**를 의미한다. 군사 용어 "폭발 반경"에서 유래한 개념으로, 하나의 IAM 자격 증명 탈취, 서비스 장애, 잘못된 배포 등이 얼마나 많은 리소스·서비스·계정·사용자에게 파급될 수 있는지를 나타낸다.

쉽게 말해, "하나가 잘못됐을 때 얼마나 많은 것이 함께 터지는가"의 범위다. Blast Radius가 클수록 리스크가 크고, 작을수록 피해가 격리된다.

## Blast Radius의 두 가지 맥락

### 1. 보안 침해 시 Blast Radius
IAM 자격 증명 탈취, S3 퍼블릭 노출, EC2 인스턴스 침해 등 보안 사고가 발생했을 때, 공격자가 접근할 수 있는 리소스·권한·데이터의 범위다.

**예시**
- AdministratorAccess IAM 유저 탈취 → 계정 전체 리소스 접근 가능 → **Blast Radius 최대**
- 단일 Lambda 함수 역할 탈취 → 해당 함수의 제한된 권한만 노출 → **Blast Radius 최소**

### 2. 장애·변경 시 Blast Radius
잘못된 배포, 코드 버그, 인프라 변경이 얼마나 많은 사용자·서비스에 영향을 주는지의 범위다.

**예시**
- 단일 AZ 배포 → AZ 장애 시 전체 서비스 다운 → **Blast Radius 큼**
- Multi-AZ 배포 → AZ 장애 시 해당 AZ만 영향 → **Blast Radius 작음**

## Blast Radius를 줄이는 핵심 전략

### 1. 멀티 계정 전략 (Multi-Account Strategy)
계정 단위가 가장 강력한 격리 경계다.

- 환경별 계정 분리: Production, Staging, Development를 별도 계정으로 분리
- 워크로드별 계정 분리: 서비스 A, B, C를 각각 다른 계정에 배포
- 기능별 계정 분리: 보안, 로그, 네트워크, 공유 서비스 계정 분리
- AWS Organizations + SCP로 계정 간 권한 제어

**효과**: 한 계정이 침해되어도 다른 계정으로 확산 방지

### 2. 최소 권한 원칙 (Least Privilege)
가장 기본적인 Blast Radius 감소 방법이다.

- IAM 사용자·역할에 필요한 권한만 부여
- `AdministratorAccess`, `FullAccess` 정책 사용 금지
- IAM Access Analyzer로 미사용 권한 지속 탐지·제거
- Lambda 함수마다 별도 IAM 역할 생성 (함수별 최소 권한)

**효과**: 자격 증명 탈취 시 공격자가 접근 가능한 범위 최소화

### 3. 권한 경계 (Permissions Boundary)
개발자가 스스로 권한을 설정할 때 범위를 제한한다.

- 개발자가 생성하는 IAM 역할에 권한 경계 자동 적용
- CI/CD 파이프라인 역할에 배포 필요 권한만 허용

### 4. 가용 영역 격리 (AZ Isolation)
단일 AZ 장애의 Blast Radius를 줄인다.

- Multi-AZ 배포로 AZ 장애 시 서비스 연속성 확보
- AZ별 독립적인 자원 운영

### 5. 셀 기반 아키텍처 (Cell-Based Architecture)
AWS 내부에서도 사용하는 방식으로, 전체 사용자를 여러 "셀"로 분할하여 하나의 셀에서 장애가 발생해도 다른 셀에 영향이 없도록 설계한다.

- 전체 사용자를 N개 셀로 분할
- 각 셀은 독립적인 스택 (EC2, DB, 캐시 등)
- 한 셀 장애 시 영향은 전체의 1/N

### 6. 리전 격리 (Region Isolation)
리전 단위 장애의 Blast Radius를 줄인다.

- 주요 서비스는 멀티 리전 배포
- Route 53 Failover로 자동 전환
- 리전 간 데이터 복제로 DR 확보

### 7. 배포 단계별 Blast Radius 최소화 (Canary/Blue-Green)
잘못된 배포의 영향 범위를 최소화한다.

- **Canary 배포**: 전체 트래픽의 1~5%에만 신규 버전 배포 후 모니터링
- **Blue/Green 배포**: 신구 환경을 완전히 분리하고 전환 시점에 스위칭
- **롤백 자동화**: 이상 감지 시 이전 버전으로 즉시 자동 롤백

### 8. 시크릿 관리 강화
자격 증명 탈취 시 Blast Radius를 줄인다.

- Secrets Manager로 DB 비밀번호·API 키 관리 및 자동 로테이션
- EC2 메타데이터 서비스 IMDSv2 강제 적용 (SSRF 공격으로 자격 증명 탈취 방지)
- 액세스 키 사용 최소화, 역할(Role) 기반 인증 선호

## 실제 Blast Radius 시나리오 비교

| 시나리오 | Blast Radius 큰 경우 | Blast Radius 작은 경우 |
|----------|---------------------|----------------------|
| IAM 침해 | AdministratorAccess 단일 유저 | 기능별 최소 권한 역할 |
| EC2 침해 | 단일 계정 전체 권한 역할 | 해당 EC2 작업만 허용하는 역할 |
| 잘못된 배포 | 전체 트래픽 한 번에 전환 | Canary 1% 배포 후 점진 확대 |
| AZ 장애 | 단일 AZ 배포 | Multi-AZ 배포 |
| 랜섬웨어 | 단일 계정에 모든 데이터 | 계정 분리 + 격리된 백업 계정 |
| Lambda 침해 | 계정 전체 Lambda 공유 역할 | 함수마다 별도 최소 권한 역할 |

## 정리

Blast Radius는 AWS 환경에서 보안 침해나 장애 발생 시 영향이 미치는 범위로, 멀티 계정 전략, 최소 권한 원칙, 권한 경계, AZ 격리, 셀 기반 아키텍처, 리전 격리, Canary/Blue-Green 배포로 줄일 수 있으며, "기본 차단이 강할수록, 격리가 세밀할수록 Blast Radius가 줄어든다"는 원칙하에 설계 단계부터 격리 경계를 명확히 정의하는 것이 핵심이다.
