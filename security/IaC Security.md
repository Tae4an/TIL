# IaC Security란?

## 개념 정의
IaC Security(Infrastructure as Code Security, 코드형 인프라 보안)는 **인프라를 코드로 정의·관리하는 IaC 파일(Terraform, CloudFormation, Kubernetes YAML 등)에서 보안 구성 오류와 취약점을 배포 전에 자동으로 스캔·탐지·수정하여 안전한 클라우드 인프라를 보장하는 보안 실천 방법**이다. 배포된 리소스를 사후 점검하는 기존 방식과 달리, 코드 단계에서 보안을 "Shift Left"하여 문제를 조기에 차단한다.

쉽게 말해 "인프라 코드를 Git에 커밋하기 전에 보안 오류를 자동으로 찾아내고 고쳐서, 배포되는 클라우드 환경이 처음부터 안전하게 만드는 것"이다.

## 핵심 원리

**Shift Left Security (보안 좌측 이동)**
- 배포 후 발견하는 것이 아닌, 개발·코드 작성 단계에서 보안 검증
- 문제 수정 비용 대폭 절감 (개발 단계 수정 < 테스트 < 운영 수정)
- 보안이 개발 속도를 저해하지 않고 자동화로 통합

**Policy as Code (정책의 코드화)**
- 보안 정책을 코드로 정의
- CIS Benchmark, 규정 준수 요구사항을 자동 검증 규칙으로 구현
- 일관되고 반복 가능한 보안 적용

**DevSecOps 통합**
- CI/CD 파이프라인에 보안 스캔 자동 삽입
- Git pre-commit, Pull Request, 빌드 단계에서 자동 검증
- 개발자에게 즉각적인 보안 피드백

**프로액티브 vs 리액티브**
- 전통적 방식: 배포 후 CSPM으로 스캔 → 문제 발견 → 수정 → 재배포 (리액티브)
- IaC Security: 배포 전 코드 스캔 → 문제 차단 → 안전한 배포 (프로액티브)

## IaC Security가 탐지하는 주요 위험

### 1. 과도한 공개 노출
- S3 버킷 퍼블릭 접근 허용
- RDS, Redis 등 DB 인터넷 노출
- 보안 그룹 0.0.0.0/0 전체 오픈
- ALB, API Gateway 인증 없는 공개

### 2. IAM 권한 오류
- 와일드카드(*) Admin 권한 부여
- 과도한 서비스 역할 권한
- 교차 계정 접근 오류
- 임시 자격증명 미사용

### 3. 암호화 누락
- EBS 볼륨 암호화 미적용
- S3 서버 측 암호화(SSE) 비활성화
- RDS 암호화 미적용
- 전송 중 암호화(TLS/SSL) 누락

### 4. 로깅·모니터링 부족
- CloudTrail 비활성화
- VPC Flow Logs 미설정
- 로그 보관 기간 부족
- 로그 암호화 미적용

### 5. Kubernetes 구성 오류
- 특권 컨테이너(Privileged) 허용
- Root 사용자로 컨테이너 실행
- hostNetwork/hostPID 사용
- NetworkPolicy 부재

### 6. 네트워크 보안
- VPC 피어링 오류
- 불필요한 포트 오픈
- NACL 규칙 오류
- 프라이빗 서브넷 NAT 미설정

### 7. 규정 준수 위반
- PCI DSS, HIPAA, CIS Benchmark 위반
- 데이터 레지던시 요구사항 미준수
- 보안 그룹 명명 규칙 위반

### 8. 시크릿 노출
- 하드코딩된 API 키, 패스워드
- 평문 시크릿 저장
- 시크릿 매니저 미사용

## IaC Security 작동 방식

### 1. 코드 작성 단계
- 개발자가 Terraform, CloudFormation, Kubernetes YAML 작성
- IDE 플러그인으로 실시간 보안 힌트 제공 (선택 사항)

### 2. Git Pre-commit Hook
- 커밋 전 로컬에서 IaC 파일 스캔
- 보안 문제 발견 시 커밋 차단
- 즉각적인 피드백으로 수정 유도

### 3. Pull Request / Code Review
- PR 생성 시 자동 IaC 스캔 실행
- GitHub Actions, GitLab CI, Azure DevOps에서 자동화
- 보안 문제를 코멘트로 표시
- PR 승인 조건으로 보안 통과 설정 가능

### 4. CI/CD 파이프라인 통합
- 빌드·테스트 단계에서 IaC 스캔
- 정책 위반 시 빌드 실패
- 배포 전 마지막 안전망

### 5. 스캔 및 분석
- 정적 분석(SAST): IaC 파일을 파싱하고 보안 정책과 비교
- 규칙 엔진, 머신러닝으로 오류·취약점 식별
- 위험도 점수(Critical, High, Medium, Low) 부여

### 6. 자동 수정 (Auto-Remediation)
- 일부 도구는 수정 코드 제안 또는 자동 패치
- Pull Request로 수정 제안 제출
- 개발자가 승인 후 머지

### 7. 리포팅 및 대시보드
- 스캔 결과 시각화
- 트렌드 분석: 시간에 따른 보안 개선도
- 경영진용 요약 리포트

## IaC Security의 주요 기능

**정적 코드 분석(SAST for IaC)**
- Terraform, CloudFormation, ARM, Bicep, Kubernetes YAML, Helm Chart 지원
- 수천 개의 사전 정의된 보안 규칙
- 커스텀 정책 추가 가능

**Policy as Code**
- OPA (Open Policy Agent), Rego 언어로 정책 정의
- 조직 맞춤형 보안 기준 코드화
- 버전 관리로 정책 이력 추적

**CI/CD 통합**
- GitHub Actions, GitLab CI, Jenkins, CircleCI, Azure Pipelines 플러그인
- API로 모든 CI/CD 시스템 통합 가능

**시크릿 스캔**
- 하드코딩된 API 키, 패스워드, 토큰 탐지
- AWS Access Key, GitHub Token 등 자동 식별

**멀티클라우드 지원**
- AWS, Azure, GCP, Kubernetes 등 모든 주요 플랫폼
- 클라우드별 보안 Best Practice 자동 적용

**수정 가이드**
- 각 문제에 대한 상세 설명
- 수정 방법 코드 예시 제공
- 자동 수정 PR 생성

**규정 준수 자동화**
- CIS Benchmark, PCI DSS, HIPAA, SOC 2 매핑
- 준수율 대시보드

## IaC Security의 장점

**조기 문제 발견**
- 배포 전 보안 문제 차단
- 운영 중 사고 방지
- 수정 비용 대폭 절감

**일관된 보안 적용**
- 모든 배포에 동일한 보안 기준 자동 적용
- 인적 오류 제거
- 보안 드리프트 방지

**개발 속도 유지**
- 보안 검토를 자동화해 배포 지연 최소화
- 개발자가 보안 전문가 없이도 안전한 코드 작성 가능
- DevOps 속도와 보안 균형

**가시성 및 추적성**
- 모든 인프라 변경이 코드로 추적
- Git 히스토리로 누가·언제·무엇을·왜 변경했는지 명확
- 감사 및 컴플라이언스 용이

**롤백 및 재현성**
- 문제 발생 시 이전 버전으로 즉시 롤백
- 동일한 인프라를 다른 환경(개발·스테이징·프로덕션)에 재현 가능

## 주요 IaC Security 도구

### 전문 IaC Security 스캐너

**오픈소스**
- Checkov (Bridgecrew/Palo Alto): Terraform, CloudFormation, K8s YAML 스캔
- tfsec: Terraform 전용 보안 스캐너
- KICS (Keeping Infrastructure as Code Secure): 멀티 IaC 도구 지원
- Terrascan: Terraform, K8s, Helm 스캔

**상용 솔루션**
- Snyk IaC: 개발자 친화적, CI/CD 통합 강력
- Prisma Cloud (Palo Alto Networks): CNAPP 통합
- Wiz, Orca Security: 종합 클라우드 보안 플랫폼
- Aqua Trivy: 오픈소스 + 상용, 멀티 스캔 (IaC, 컨테이너, 시크릿)

### 정책 엔진
- OPA (Open Policy Agent): 정책 as Code 표준
- Sentinel (HashiCorp): Terraform Cloud 통합
- Conftest: OPA 기반 IaC 정책 검증

### CI/CD 통합 플랫폼
- Jit: 종합 AppSec 플랫폼, IaC + SAST + DAST 통합

## 실무 도입·운영 팁

### 초기 설정

**1. 도구 선택**
- 사용 중인 IaC 도구(Terraform, CloudFormation 등)에 맞는 스캐너 선택
- 멀티 IaC 환경이면 Checkov, KICS 같은 범용 도구
- 예산이 있다면 Snyk, Prisma Cloud 같은 상용 솔루션

**2. 정책 정의**
- 조직 보안 기준 정리
- CIS Benchmark + 커스텀 정책
- 우선순위 설정 (Critical/High 먼저)

**3. CI/CD 통합**
- GitHub Actions, GitLab CI에 스캔 단계 추가
- Pre-commit hook 설정 (로컬 스캔)
- PR에 자동 코멘트 설정

### 점진적 적용

**단계 1: 가시성 확보 (Alert Only)**
- 초기엔 스캔만 실행, 빌드 실패 안 시킴
- 현재 보안 수준 파악
- False Positive 조정

**단계 2: Critical 차단**
- Critical 이슈만 빌드 실패
- 팀이 적응할 시간 확보

**단계 3: 전체 적용**
- High 이상 모두 차단
- 예외 규칙 정리

### 개발자 교육

**보안 의식 제고**
- IaC 보안 Best Practice 교육
- 일반적인 오류 패턴 공유
- 수정 예시 제공

**개발자 친화적 피드백**
- 명확한 에러 메시지
- 수정 방법 코드 예시
- IDE 통합으로 실시간 힌트

### 지속적 개선

**정책 업데이트**
- 새로운 위협·규제 반영
- 팀 피드백 수렴
- 정기적 정책 리뷰

**메트릭 추적**
- 스캔 통과율 추세
- 이슈 수정 시간(MTTR)
- False Positive 비율
- 배포 속도 영향

## IaC Security vs CSPM

| 구분 | IaC Security | CSPM |
|------|-------------|------|
| 시점 | 배포 전 (Pre-deployment) | 배포 후 (Post-deployment) |
| 대상 | IaC 코드 (Terraform, YAML) | 실제 클라우드 리소스 |
| 접근 | Shift Left (사전 예방) | 사후 탐지·수정 |
| 통합 | CI/CD, Git | 클라우드 API |
| 장점 | 문제 조기 차단, 비용 절감 | 런타임 드리프트 탐지 |

**이상적 전략: IaC Security + CSPM 병행**
- IaC Security로 배포 전 대부분의 문제 차단
- CSPM으로 배포 후 수동 변경·드리프트 탐지
- 이중 안전망 구축

## 요약

IaC Security는 인프라 코드를 배포 전에 자동으로 스캔·검증하여 보안 구성 오류와 취약점을 조기에 차단하는 Shift Left 보안 실천 방법이다. CI/CD 파이프라인과 Git 워크플로에 통합되어 개발자가 안전한 인프라 코드를 작성하도록 돕고, Policy as Code로 일관된 보안 기준을 자동 적용한다. Terraform, CloudFormation, Kubernetes YAML 등 모든 IaC 도구를 지원하며, CSPM과 병행해 배포 전후 이중 보안을 실현하는 현대 DevSecOps의 핵심 요소다.
