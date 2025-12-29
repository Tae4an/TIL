# Amazon Inspector란?

## 개념 정의
Amazon Inspector는 AWS 워크로드(EC2, 컨테이너, Lambda)와 코드 저장소에서 소프트웨어 취약점(Software Vulnerabilities)과 의도하지 않은 네트워크 노출(Unintended Network Exposure)을 자동으로 지속적으로 검색하고 관리하는 완전 관리형 취약점 관리 서비스다. 50개 이상의 취약점 인텔리젠스 소스를 활용해 알려진 CVE(Common Vulnerabilities and Exposures)를 탐지하고, 위험 기반 우선순위 점수를 제공한다.

쉽게 말해 "EC2·컨테이너·Lambda의 보안 취약점을 자동으로 찾아내고, 위험도 높은 것부터 수정하도록 알려주는 AWS 취약점 스캐너"다.

## 핵심 특징

**자동 워크로드 검색**
- 활성화하면 모든 EC2 인스턴스, ECR 컨테이너 이미지, Lambda 함수 자동 발견
- 수동으로 스캔 대상 지정 불필요
- 신규 리소스 배포 시 자동 스캔 시작

**지속적 모니터링(Continuous Scanning)**
- 일회성이 아닌 24/7 지속적 취약점 검사
- 리소스 변경·업데이트 시 자동 재스캔
- 새로운 CVE 발견 시 기존 리소스 자동 재평가

**에이전트 기반 + 에이전트리스 스캔**
- EC2 에이전트 기반: SSM Agent로 깊이 있는 취약점 검사
- EC2 에이전트리스(Preview): EBS 스냅샷으로 SSM 없이도 스캔 가능 (하이브리드 모드)
- ECR·Lambda: 에이전트 불필요, 자동 스캔

**위험 기반 우선순위화**
- CVE 정보 + 네트워크 접근성 + 악용 가능성 → 컨텍스트 기반 위험 점수 산정
- Critical → High → Medium → Low로 분류
- 중요도 높은 취약점부터 처리 가능

**50+ 취약점 인텔리젼스 소스**
- AWS Security Bulletin
- NVD(National Vulnerability Database)
- CISA Known Exploited Vulnerabilities
- 벤더별 보안 권고
- 실시간 위협 정보 통합

**SBOM(Software Bill of Materials) 생성**
- 모니터링되는 모든 리소스의 소프트웨어 구성 요소 목록 자동 생성
- 중앙 집중식 SBOM 내보내기
- 공급망 보안 강화

**규정 준수 지원**
- NIST CSF, PCI DSS, HIPAA 등 규제 요구사항 지원
- CIS Benchmark 자동 점검
- 감사 리포트 자동 생성

## 스캔 대상 및 유형

### 1. Amazon EC2 인스턴스

**스캔 방법**
- 에이전트 기반(SSM Agent): 기본 방식, 상세 스캔
- 에이전트리스(Agentless): EBS 스냅샷 기반, SSM 설치 불가 환경
- 하이브리드 모드: SSM 우선, 없으면 자동으로 에이전트리스

**스캔 내용**
- OS 패키지 취약점 (Amazon Linux, Ubuntu, Windows 등)
- 애플리케이션 취약점
- 네트워크 접근 가능성 (인터넷 노출 여부)
- 패치 누락

**스캔 시점**
- EC2 인스턴스 시작 시
- 소프트웨어 설치·업데이트 시
- 새로운 CVE 발견 시 자동 재스캔

### 2. Amazon ECR 컨테이너 이미지

**스캔 방법**
- ECR에 이미지 푸시 시 자동 스캔
- 새로운 CVE 발견 시 기존 이미지 재스캔
- 에이전트 불필요

**스캔 내용**
- OS 패키지 취약점
- 프로그래밍 언어 패키지 취약점 (Python, Java, Node.js, Go 등)
- 레이어별 취약점 분석

**Enhanced Scanning (확장 스캔)**
- 레지스트리 수준에서 활성화
- OS + 프로그래밍 언어 패키지 모두 스캔
- 더 깊이 있는 취약점 탐지

### 3. AWS Lambda 함수

**Lambda Standard Scanning**
- 함수 배포 시 자동 스캔
- 함수 업데이트 시 재스캔
- 레이어(Layer) 포함 스캔
- 함수 코드 내 종속성 취약점 검사

**스캔 내용**
- 애플리케이션 종속성 취약점
- 런타임 환경 취약점
- 레이어 취약점

### 4. 코드 저장소 (Code Repository Scanning)

**CI/CD 통합**
- GitHub Actions, GitLab CI, Jenkins 등과 통합
- 빌드 전 코드 취약점 스캔
- SBOM 생성 후 Inspector로 전송

**스캔 가능 대상**
- 파일·디렉토리
- 컨테이너 이미지
- 컴파일된 바이너리 (Go, Rust)
- 아카이브 (.zip, .tar)

## 주요 기능

### 검색 결과(Findings)

**Finding 구조**
- 취약점 ID (CVE-XXXX-XXXX)
- 위험 점수 (0~10)
- 심각도 (Critical, High, Medium, Low)
- 영향받는 리소스 (인스턴스 ID, 이미지 ARN 등)
- 취약한 패키지·버전
- 수정 방법 (패치 버전, 권장 조치)

**위험 점수 계산**
- CVE 기본 점수 (CVSS Score)
- 네트워크 접근 가능성 (인터넷 노출 여부)
- 악용 가능성 (PoC 존재, 실제 공격 사례)
- 리소스 환경(프로덕션 vs 개발)

### 네트워크 도달 가능성 분석

**네트워크 노출 검사**
- EC2가 인터넷에서 직접 접근 가능한지 분석
- 보안 그룹·NACL·IGW·EIP 등 네트워크 경로 추적
- 불필요한 네트워크 노출 탐지

**예시**
- 22번 포트(SSH)가 0.0.0.0/0에 오픈
- RDP 포트가 인터넷에 노출
- 웹서버가 아닌데 80/443 포트 오픈

### 통합 대시보드

**전체 보안 상태 한눈에 파악**
- 취약점 통계 (Critical/High/Medium/Low 개수)
- 리소스별 취약점 현황
- 시간에 따른 취약점 트렌드
- 가장 취약한 리소스 TOP N

### 자동 알림 및 통합

**EventBridge 연동**
- 새로운 Finding 발견 시 자동 알림
- Slack, PagerDuty, SNS, Lambda 등으로 전달
- 자동 티케팅 (Jira, ServiceNow)

**Security Hub 통합**
- Inspector Finding을 Security Hub로 자동 전송
- 중앙 집중식 보안 모니터링

**Systems Manager 통합**
- Inspector 결과 → SSM Patch Manager로 자동 패치
- 자동 수정 워크플로

## 사용 방법

### 1. Inspector 활성화
```
AWS 콘솔 → Inspector 검색 → "Enable Inspector" 클릭
  - 자동으로 모든 스캔 유형 활성화
  - EC2, ECR, Lambda 자동 검색 시작
```

### 2. EC2 스캔 설정 (에이전트 기반)
```
EC2 인스턴스에 IAM 역할 할당
  - AmazonSSMManagedInstanceCore 정책 필요
  - SSM Agent 설치 및 실행 확인
  - 수분 내에 Inspector 자동 스캔 시작
```

### 3. ECR 스캔 설정
```
ECR 콘솔 → 레지스트리 설정 → Enhanced Scanning 활성화
  - 이미지 푸시 시 자동 스캔
  - Inspector가 자동으로 취약점 검사
```

### 4. Finding 확인 및 조치
```
Inspector 콘솔 → Findings 메뉴
  - 심각도별 필터링
  - 리소스별 그룹화
  - 수정 가이드 확인
  - 패치 적용 또는 완화 조치
```

## 주요 활용 사례

**지속적 취약점 관리**
- 모든 AWS 워크로드의 취약점 자동 추적
- 제로데이 취약점 빠른 발견
- MTTR(Mean Time To Remediation) 단축

**컨테이너 보안**
- ECR 이미지 배포 전 취약점 사전 차단
- 프로덕션에 안전한 이미지만 배포
- CI/CD 파이프라인에 보안 게이트 통합

**규정 준수 자동화**
- PCI DSS, HIPAA, NIST 요구사항 자동 점검
- 취약점 감사 리포트 자동 생성
- 정기 취약점 점검 증명

**DevSecOps 구현**
- Shift Left: 개발 초기 단계부터 취약점 검사
- CI/CD 파이프라인에 스캔 자동화
- 개발자에게 즉각적인 보안 피드백

**공급망 보안**
- SBOM 생성·관리
- 오픈소스 종속성 취약점 추적
- 소프트웨어 구성 요소 가시성 확보

**패치 우선순위화**
- 위험 점수 기반 패치 우선순위 결정
- 인터넷 노출 + Critical CVE = 최우선 처리
- 효율적인 보안 리소스 배분

## 비용

### 스캔 유형별 과금

**EC2 스캔**
- 인스턴스당 월별 과금
- 예시: $1.50 per instance/month (리전별 상이)

**ECR 스캔**
- 재스캔(Rescan) 기준 과금
- 최초 스캔: 무료
- 재스캔: 이미지당 과금

**Lambda 스캔**
- 함수당 월별 과금

**프리 티어**
- 최초 15일간 무료 체험
- 이후 사용량 기반 과금

**비용 최적화 팁**
- 필요한 리소스만 스캔 활성화
- 태그 기반 스캔 대상 필터링
- 개발 환경은 주기적 스캔으로 전환 고려

## Inspector vs 기타 스캔 도구

| 도구 | 범위 | 특징 |
|------|------|------|
| **Inspector** | AWS 네이티브 (EC2, ECR, Lambda) | 자동화, 지속적, AWS 통합 |
| **Snyk** | 멀티 플랫폼 | 개발자 친화적, 코드·컨테이너·IaC |
| **Trivy** | 오픈소스 | 무료, 컨테이너·IaC·Git 스캔 |
| **Qualys** | 온프레미스+클라우드 | 엔터프라이즈급 취약점 관리 |

## 모범 사례

**전사 활성화**
- AWS Organizations 연동해 모든 계정 자동 활성화
- 중앙 집중식 취약점 관리

**자동화된 수정 워크플로**
- EventBridge → Lambda → SSM Patch Manager
- Critical/High 취약점 자동 패치
- 수동 승인 프로세스 병행

**태그 기반 관리**
- Environment, Criticality 태그로 우선순위 조정
- 프로덕션 리소스 우선 처리
- 개발 환경은 낮은 우선순위

**정기 리뷰**
- 주간 취약점 리뷰 미팅
- 트렌드 분석 및 개선 계획 수립
- 반복되는 취약점 패턴 프로세스 개선

**CI/CD 통합**
- GitHub Actions, Jenkins에 Inspector 스캔 추가
- 빌드 실패 기준 설정 (예: Critical 발견 시)
- 개발자에게 즉시 피드백

**Security Hub 연동**
- Inspector + GuardDuty + Config + Macie 통합
- 종합 보안 대시보드 구축

## 요약

Amazon Inspector는 EC2·ECR·Lambda 워크로드의 소프트웨어 취약점과 네트워크 노출을 자동으로 지속적으로 검색하는 AWS 완전 관리형 취약점 관리 서비스다. 50개 이상의 취약점 인텔리전스 소스를 활용해 CVE를 탐지하고, 컨텍스트 기반 위험 점수로 우선순위를 부여하며, SBOM 생성·규정 준수·DevSecOps 통합을 통해 현대적인 클라우드 보안 실천을 자동화한다. 활성화만으로 리소스를 자동 검색하고 지속적으로 모니터링하여 제로데이 취약점을 빠르게 발견하고 MTTR을 단축시킨다.
