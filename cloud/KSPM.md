# KSPM이란?

## 개념 정의
KSPM(Kubernetes Security Posture Management, 쿠버네티스 보안 태세 관리)은 **Kubernetes 클러스터와 그 안에서 실행되는 워크로드의 보안 구성 오류와 취약점을 지속적으로 모니터링·탐지·수정하여 K8s 환경의 보안 태세를 자동화하는 보안 관리 체계**다. CSPM이 클라우드 인프라 전반을 다룬다면, KSPM은 Kubernetes 환경에 특화된 보안 관리를 제공한다.

쉽게 말해 "Kubernetes 클러스터 설정 오류와 보안 위험을 자동으로 찾아내고 고쳐주는 K8s 전문 보안 도구"다.

## 핵심 특징

**Kubernetes 특화 보안**
- K8s 고유의 구성 요소(Control Plane, Node, Pod, RBAC, NetworkPolicy 등) 보안 검증
- 컨테이너 오케스트레이션 환경의 복잡성에 맞춘 전문 보안
- CSPM의 Kubernetes 버전이라 할 수 있음

**자동화된 구성 스캔**
- 클러스터 구성을 CIS Kubernetes Benchmark, NSA/CISA 가이드라인 등과 비교
- 보안 정책 위반, 설정 오류 자동 탐지
- 수동 점검 불가능한 대규모 클러스터 관리

**지속적 모니터링**
- 클러스터 구성 변경을 실시간 추적
- 드리프트(Drift) 탐지: 승인된 구성에서 벗어난 변경 감지
- 새로운 배포, 업데이트 시 즉시 보안 평가

**DevSecOps 통합**
- CI/CD 파이프라인에 통합해 배포 전 보안 검증(Shift Left)
- IaC(Helm Chart, Kubernetes Manifest)에 대한 사전 스캔
- 개발 단계부터 보안 자동화

**멀티클러스터 지원**
- 여러 클러스터(개발·스테이징·프로덕션)를 중앙에서 통합 관리
- 온프레미스, 클라우드(EKS, AKS, GKE), 하이브리드 환경 지원

**위험 우선순위화**
- 탐지된 보안 이슈를 심각도·영향도 기준으로 분류
- 중요도 높은 위험부터 처리 가능

## KSPM이 탐지하는 주요 위험

### 1. RBAC 오류 (Role-Based Access Control)
- 과도한 권한 부여(예: cluster-admin 남용)
- 불필요한 ServiceAccount 권한
- 비활성 사용자에게 남아있는 권한
- 네임스페이스 간 권한 누수

### 2. API Server 보안
- 외부에 노출된 Kubernetes API Server
- 인증·인가 없는 API 접근 허용
- 안전하지 않은 포트 오픈(예: 8080 비보안 포트)

### 3. Pod Security
- 특권 컨테이너(Privileged Container) 실행
- Root 사용자로 컨테이너 실행
- hostNetwork, hostPID, hostIPC 사용
- Security Context 미설정

### 4. 네트워크 보안
- NetworkPolicy 미설정(Pod 간 무제한 통신)
- Ingress/Egress 규칙 부재
- 불필요한 외부 노출

### 5. etcd 보안
- etcd 데이터 암호화 미적용
- etcd API 접근 통제 부족
- 백업 보안 미흡

### 6. Secret 관리
- Secret을 평문으로 저장
- Secret 암호화 미적용
- 과도한 Secret 접근 권한

### 7. 감사 로그
- 감사 로그(Audit Log) 비활성화
- 로그 보관 기간 부족
- 로그 무결성 보호 미흡

### 8. 컨테이너 이미지
- 취약점 있는 이미지 사용
- 최신 보안 패치 미적용
- 신뢰되지 않은 레지스트리 사용

### 9. Admission Controller
- PodSecurityPolicy, ValidatingWebhook 미설정
- 보안 정책 적용 누락

### 10. 노드 보안
- Kubelet 설정 오류
- 노드 간 통신 암호화 미적용
- 패치되지 않은 노드 OS

## KSPM 작동 방식

### 1. 보안 정책 정의
- CIS Kubernetes Benchmark 등 표준 프레임워크 선택
- 조직 맞춤형 보안 정책 추가
- RBAC, NetworkPolicy, PodSecurityPolicy 등 정책 설정

### 2. 클러스터 구성 스캔
- Kubernetes API를 통해 클러스터 구성 정보 수집
- Control Plane, Worker Node, Pod, Service 등 전체 구성 분석
- 에이전트 기반 또는 에이전트리스 방식

### 3. 위험 탐지 및 평가
- 정의된 정책과 실제 구성 비교
- 규칙 기반 분석으로 오류·취약점 식별
- 위험도 점수 산정 및 우선순위 지정

### 4. 알림 및 리포팅
- 탐지된 보안 이슈를 대시보드에 시각화
- 실시간 알림(Slack, PagerDuty, Email)
- 규정 준수 리포트 자동 생성

### 5. 수정(Remediation)
- 자동 수정: 정책 위반 자동 해결
- 수동 수정: 단계별 가이드 제공
- IaC 수정 제안(Helm Chart, YAML 파일)

### 6. 지속적 모니터링
- 클러스터 변경 사항 실시간 추적
- 새로운 배포 시 자동 재평가
- 드리프트 탐지 및 경고

## KSPM의 주요 기능

**CIS Kubernetes Benchmark 자동 점검**
- 업계 표준 보안 기준 자동 적용
- 준수율 대시보드 제공

**런타임 모니터링**
- 실행 중인 클러스터의 이상 행위 탐지
- 의심스러운 API 호출, 권한 상승 시도 감지

**컨테이너 이미지 스캔**
- CVE(Common Vulnerabilities and Exposures) 스캔
- 취약점 있는 이미지 배포 차단

**CI/CD 파이프라인 통합**
- GitOps 워크플로에 보안 검증 삽입
- 배포 전 정책 위반 차단
- 개발자 피드백 자동화

**멀티클러스터 관리**
- 단일 콘솔로 수십~수백 개 클러스터 관리
- 클러스터 간 보안 상태 비교

**규정 준수 자동화**
- PCI DSS, HIPAA, SOC 2 등 K8s 관련 요구사항 검증
- 감사 리포트 자동 생성

**위협 인텔리전스 통합**
- 알려진 K8s 공격 패턴·취약점 DB 연동
- 최신 위협 정보 반영

## KSPM vs 관련 개념

| 구분 | KSPM | CSPM | CWPP |
|------|------|------|------|
| 초점 | Kubernetes 구성·정책 | 클라우드 인프라 구성 | 워크로드 런타임 |
| 대상 | K8s 클러스터, Pod, RBAC | S3, IAM, VPC, 보안 그룹 | VM, 컨테이너 프로세스 |
| 주요 기능 | K8s 오류 탐지, RBAC 검증 | 구성 검증, 규정 준수 | 멀웨어·침입 차단 |
| 예시 | 특권 Pod 탐지, NetworkPolicy 부재 | S3 퍼블릭 차단 | 컨테이너 랜섬웨어 차단 |

**KSPM은 CSPM의 Kubernetes 특화 버전**이며, 실무에서는 CSPM + KSPM + CWPP를 통합한 CNAPP(Cloud-Native Application Protection Platform)로 운영하는 추세다.

## 주요 활용 사례

**대규모 K8s 환경 보안 관리**
- 수십~수백 개 클러스터를 자동으로 보안 점검
- 인적 오류 최소화

**DevSecOps 구현**
- CI/CD 파이프라인에 보안 자동화
- 배포 전 보안 검증으로 취약점 사전 차단

**규정 준수 자동화**
- CIS Benchmark, PCI DSS 등 자동 점검
- 감사 준비 시간 단축

**멀티클러스터 가시성 확보**
- 온프레미스·클라우드 혼재 환경 통합 관리
- 클러스터별 보안 상태 비교

**제로 트러스트 구현**
- RBAC 최소 권한 원칙 자동 적용
- NetworkPolicy로 마이크로세그멘테이션

## 주요 KSPM 솔루션

**전문 KSPM/CNAPP 솔루션**
- Wiz, Orca Security
- Prisma Cloud (Palo Alto Networks)
- Aqua Security
- Sysdig Secure

**클라우드 네이티브**
- AWS Security Hub + Amazon Inspector
- Azure Defender for Cloud
- Google Cloud Security Command Center

**오픈소스**
- kubescape (ARMO)
- kube-bench (Aqua Security)
- Falco (runtime 보안)
- OPA (Open Policy Agent)

## 실무 도입·운영 팁

**초기 평가**
- 현재 클러스터 보안 상태 진단
- CIS Benchmark 기준으로 Gap Analysis
- 우선순위 설정(Critical → High → Medium)

**정책 커스터마이징**
- 기본 정책(CIS) + 조직 맞춤 정책
- False Positive 줄이기 위한 예외 규칙 설정
- 개발·프로덕션 환경별 차등 정책

**CI/CD 통합**
- Git pre-commit hook에 Manifest 스캔
- Helm Chart 배포 전 자동 검증
- 정책 위반 시 파이프라인 실패 설정

**자동 수정 단계적 적용**
- 초기엔 탐지·알림만
- 검증 후 자동 수정 확대
- 중요 클러스터는 수동 승인 유지

**팀 교육 및 협업**
- 개발자에게 K8s 보안 Best Practice 교육
- 보안팀·DevOps팀 공동 정책 수립
- 알림·티켓 자동화(Jira, Slack 연동)

**지속적 개선**
- 주간·월간 보안 태세 트렌드 분석
- 반복되는 오류 패턴 프로세스 개선
- 새로운 K8s 버전·보안 기준 반영

## Kubernetes 보안의 4C (Cloud Native Security의 4 Layers)

KSPM은 다음 4가지 계층을 모두 커버한다

1. **Cloud**: 클라우드 인프라(AWS, Azure, GCP) 보안
2. **Cluster**: Kubernetes 클러스터 구성 보안
3. **Container**: 컨테이너 이미지·런타임 보안
4. **Code**: 애플리케이션 코드 보안

## 요약

KSPM은 Kubernetes 클러스터의 보안 구성 오류와 취약점을 지속적으로 모니터링·탐지·수정하는 K8s 전문 보안 관리 체계로, CSPM의 Kubernetes 특화 버전이다. CIS Kubernetes Benchmark 자동 점검, RBAC/NetworkPolicy 검증, 멀티클러스터 관리, CI/CD 통합을 통해 대규모 컨테이너 오케스트레이션 환경에서 자동화된 보안 태세 관리를 실현한다. 현대 클라우드 네이티브 보안의 핵심 요소로, CSPM·CWPP와 함께 CNAPP에 통합되는 추세다.
