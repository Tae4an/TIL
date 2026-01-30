# AWS CloudWatch Container Insights란?

## 개념 정의

CloudWatch Container Insights는 컨테이너 환경(ECS, EKS, Fargate)의 성능 메트릭과 로그를 자동으로 수집, 집계, 시각화하는 CloudWatch 기능이다. 클러스터, 노드, 서비스, Pod, 태스크, 컨테이너 레벨의 CPU, 메모리, 디스크, 네트워크 메트릭을 수집하고, 컨테이너 재시작 오류 같은 진단 정보를 제공하며, 사전 구성된 대시보드로 실시간 모니터링을 지원한다.

쉽게 말해 "컨테이너 환경의 성능과 로그를 자동으로 모아서 보여주는 모니터링 도구로, 클러스터부터 개별 컨테이너까지 계층별로 상태를 추적하는 서비스"다.

## 핵심 기능

- 자동 메트릭 수집 (CPU, 메모리, 네트워크, 디스크)
- 계층별 집계 (클러스터 → 노드 → Pod → 컨테이너)
- 사전 구성된 대시보드
- 로그와 메트릭 연관 분석
- 컨테이너 재시작 오류 진단
- CloudWatch 경보 통합

## 지원 플랫폼

**Amazon EKS**
- EC2 기반 클러스터
- Fargate 기반 클러스터
- CloudWatch Agent DaemonSet으로 수집

**Amazon ECS**
- EC2 Launch Type
- Fargate Launch Type
- 태스크 및 서비스 레벨 메트릭

**자체 관리 Kubernetes**
- EC2에 설치된 Kubernetes
- CloudWatch Agent 수동 배포

**RedHat OpenShift on AWS (ROSA)**
- OpenShift 클러스터 모니터링

## 주요 구성 요소

**CloudWatch Agent**
- EKS: DaemonSet으로 각 노드에 배포
- ECS: 사이드카 컨테이너 또는 자동 수집
- 컨테이너 런타임 메트릭 수집
- 성능 로그를 Embedded Metric Format으로 전송

**FluentBit / Fluentd**
- 애플리케이션 로그 수집
- CloudWatch Logs로 전송
- DaemonSet으로 배포

**Performance Log Events**
- Embedded Metric Format (EMF) 사용
- JSON 구조화 로그로 고카디널리티 데이터 저장
- CloudWatch Logs에 저장 후 자동으로 메트릭 생성

**자동 대시보드**
- Container Insights 콘솔에서 제공
- 클러스터, 노드, Pod, 서비스, 네임스페이스 뷰
- 드릴다운으로 세부 계층 탐색

## 수집 메트릭

### 클러스터 레벨
- 노드 개수, CPU/메모리 사용률
- Pod 개수, 실행 중/대기/실패 상태
- 네트워크 처리량

### 노드 레벨
- CPU, 메모리, 디스크 사용률
- 네트워크 I/O
- Pod 개수

### Pod 레벨
- CPU, 메모리 사용량 및 예약량
- 네트워크 I/O
- 컨테이너 재시작 횟수
- 컨테이너 상태 (Running, Pending, Failed)

### 서비스 레벨 (ECS)
- 태스크 개수
- CPU, 메모리 평균 사용률
- 네트워크 처리량

### 컨테이너 레벨
- CPU, 메모리 사용량
- 디스크 I/O
- 재시작 횟수

## 작동 방식

### EKS 설정
1. IAM Policy 생성 (CloudWatch Logs 쓰기 권한)
2. IAM Role 생성 및 IRSA 연결
3. CloudWatch Agent DaemonSet 배포
4. FluentBit DaemonSet 배포
5. 자동으로 메트릭 수집 시작
6. CloudWatch 콘솔에서 대시보드 확인

### ECS 설정
1. ECS 클러스터 설정에서 Container Insights 활성화
2. 태스크 정의에 필요한 권한 추가
3. 자동으로 메트릭 및 로그 수집
4. CloudWatch 콘솔에서 대시보드 확인

### 메트릭 생성 흐름
1. CloudWatch Agent가 컨테이너 런타임에서 메트릭 수집
2. EMF 형식의 Performance Log Event 생성
3. CloudWatch Logs로 전송
4. CloudWatch가 자동으로 메트릭 추출 및 집계
5. 클러스터, 노드, Pod, 서비스 레벨로 집계
6. 대시보드 및 Metrics 섹션에서 조회 가능

## 주요 활용 사례

**성능 문제 진단**
CPU 스파이크, 메모리 누수, 네트워크 병목 현상을 실시간으로 감지하며, 로그와 메트릭을 연관 분석하여 근본 원인을 빠르게 파악한다.

**용량 계획**
클러스터 및 노드의 평균 리소스 사용률을 추적하며, Pod별 리소스 사용 패턴을 분석하여 적절한 Request/Limit 값을 결정하고, 노드 스케일링 전략을 수립한다.

**애플리케이션 장애 대응**
컨테이너 재시작 패턴을 모니터링하여 불안정한 서비스를 식별하며, 태스크 상태 전환을 분석하여 배포 실패 원인을 추적하고, 서비스 이벤트 로그와 성능 메트릭을 연관하여 인프라 영향을 파악한다.

**비용 최적화**
과도하게 프로비저닝된 Pod를 식별하여 리소스 요청을 조정하며, 유휴 리소스를 감지하여 클러스터 크기를 최적화하고, 스팟 인스턴스 사용 시 안정성을 모니터링한다.

**보안 및 컴플라이언스**
비정상적인 네트워크 트래픽 패턴을 감지하며, 컨테이너 이벤트 로그를 중앙 집중 저장하여 감사 추적을 수행하고, 알람으로 보안 이상 징후를 실시간 알림한다.

## 통합 서비스

- Amazon EKS, ECS, Fargate: 모니터링 대상
- CloudWatch Logs: 로그 저장소
- CloudWatch Alarms: 임계값 기반 알림
- AWS X-Ray: 분산 추적 통합
- Amazon SNS: 알람 알림 전송
- AWS Systems Manager: 파라미터 스토어 연동
- AWS Lambda: 이벤트 기반 자동화

## Container Insights vs CloudWatch 기본 메트릭

| 항목 | Container Insights | 기본 메트릭 |
|------|-------------------|-----------|
| 대상 | 컨테이너 환경 전용 | 모든 AWS 리소스 |
| 세분화 | Pod, 컨테이너 레벨 | 서비스 레벨 |
| 로그 통합 | 자동 연관 분석 | 수동 연결 |
| 대시보드 | 사전 구성 | 수동 생성 |
| 네트워크 메트릭 | Pod 단위 수집 | 제한적 |
| 비용 | 추가 로그 비용 | 기본 메트릭 무료 |

## 향상된 관찰성 (Enhanced Observability)

2024년 12월 ECS에 추가된 기능으로, 더욱 세밀한 모니터링을 제공한다.

**주요 개선 사항**
- 태스크 및 컨테이너 레벨의 세분화된 성능 메트릭
- 메트릭, 로그, 이벤트 자동 연관
- 시각적 드릴다운으로 근본 원인 분석
- AWS 모범 사례 기반 선별된 대시보드
- Application Signals 통합

## 모범 사례

**적절한 수집 주기 설정**
기본 60초 간격으로 시작하며, 세밀한 모니터링이 필요하면 30초로 조정하고, 비용과 성능의 균형을 고려한다.

**로그 보존 기간 관리**
CloudWatch Logs 보존 기간을 설정하여 비용을 제어하며, 장기 보관은 S3로 아카이브한다.

**알람 설정**
CPU 사용률 80% 초과, 메모리 사용률 90% 초과, 컨테이너 재시작 횟수 급증 시 알람을 설정한다.

**네임스페이스 필터링**
kube-system 같은 시스템 네임스페이스를 제외하여 불필요한 데이터 수집을 줄인다.

**메트릭 집계 활용**
서비스 레벨 평균 메트릭으로 전체 상태를 파악하고, 이상 징후 발견 시 Pod 레벨로 드릴다운한다.

**태그 활용**
일관된 태깅 전략으로 환경(dev, prod), 팀, 프로젝트별 필터링을 지원한다.

**로그 쿼리 최적화**
CloudWatch Logs Insights로 정기적인 로그 쿼리를 생성하여 반복 작업을 자동화한다.

## 제약 사항

**추가 비용**
CloudWatch Logs 저장 비용, 커스텀 메트릭 비용, API 호출 비용이 발생한다.

**지연 시간**
메트릭이 대시보드에 표시되기까지 수분 소요되며, 실시간은 아니다.

**데이터 보존**
메트릭은 15개월, 로그는 보존 기간 설정에 따라 보관되며, 이후 자동 삭제된다.

**고카디널리티 제한**
EMF는 최대 100개 차원을 지원하며, 초과 시 일부 메트릭이 손실될 수 있다.

## 비용

- CloudWatch Logs 저장: $0.50/GB
- 커스텀 메트릭: $0.30/메트릭/월 (처음 10,000개)
- CloudWatch Agent: 무료 (EC2 비용만)
- API 호출: $0.01/1,000 요청

일반적으로 중소규모 클러스터는 월 $50~$200, 대규모 클러스터는 월 $500 이상 발생한다.

## 요약

CloudWatch Container Insights는 ECS, EKS, Fargate 컨테이너 환경의 성능 메트릭과 로그를 자동 수집하는 CloudWatch 기능으로, CloudWatch Agent와 FluentBit을 DaemonSet으로 배포하여 데이터를 수집하며, Embedded Metric Format으로 고카디널리티 데이터를 저장하고, 클러스터부터 컨테이너까지 계층별 메트릭을 집계하며, 사전 구성된 대시보드로 드릴다운 분석을 지원하고, 로그와 메트릭을 자동 연관하여 근본 원인 파악을 가속화하며, 성능 문제 진단, 용량 계획, 비용 최적화에 활용되고, 향상된 관찰성 기능으로 더욱 세밀한 모니터링을 제공한다.
