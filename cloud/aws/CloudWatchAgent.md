# AWS CloudWatch Agent란?

## 개념 정의
AWS CloudWatch Agent는 AWS에서 제공하는 **통합형 시스템/애플리케이션 모니터링 및 로그 수집 에이전트**다. EC2, 온프레미스 서버, 컨테이너 등 다양한 환경에서 메모리·디스크·프로세스·커스텀 지표부터 다양한 종류의 로그까지 CloudWatch로 자동 전송/수집할 수 있도록 설계된 도구다.

쉽게 말해 “AWS 환경에서 상세 모니터링·로그 수집을 통합 관리하는 표준 에이전트”로, 운영 관리의 자동화와 확장성을 크게 높여주는 필수 구성 요소다.

## 핵심 특징

**상세·커스텀 지표 수집**
- EC2 기본 지표(출력, 네트워크 등) 외에도 메모리, 디스크, 파일 시스템, 프로세스 등 상세 시스템 지표 실시간 수집 및 전송
- JSON 설정 파일로 다양한 커스텀 지표(애플리케이션 내부 메트릭, Docker 컨테이너 지표 등) 수집 가능

**다양한 로그 관리**
- 시스템 로그, 애플리케이션 로그, Docker 로그 등 다양한 로그 소스를 CloudWatch Logs로 직접 전송
- 로그 패턴 분석, 검색, 알림 설정 등 로그 기반 운영 자동화

**통합 설치/관리 지원**
- AWS Systems Manager(SSM) 연동으로 에이전트의 원격 설치, 동시 구성 변경, 배포 자동화
- SSM 명령 또는 EC2 User Data, AWS 콘솔, CLI, CloudFormation 등 다양한 설치·배포 옵션

**운영 자동화와 확장성**
- 수십~수백 대 서버/인스턴스에 에이전트 설치·구성 일괄 관리
- 다양한 OS(Amazon Linux, Ubuntu, Windows 등) 및 하이브리드 환경 지원

## 주요 구성 요소

**CloudWatch Agent**
- OS별 바이너리로 제공되는 통합 모니터링 에이전트(모든 AWS 및 온프레미스 서버 지원)
- JSON config 파일 기반으로 지표·로그 수집 범위 설정(쉽고 유연하게 변경)

**SSM 연동**
- AWS Systems Manager Parameter Store에 저장된 config를 불러 자동 배포 가능
- SSM Run Command, State Manager, Automation 등으로 설치·업데이트·구성 변경 일괄 처리

**모니터링 지표/로그**
- 기본 지표(EC2 기본 항목)
- 상세 지표(메모리, 디스크 등 시스템 레벨)
- 커스텀 지표(Application, Container, Script 등)
- 로그 그룹(Log Group) 단위로 CloudWatch Logs 전송

## 기존 방식 vs CloudWatch Agent 비교

| 특징     | CloudWatch Agent   | 기존 방식/수동 스크립트   |
|----------|--------------------|--------------------------|
| 상세 지표 | 다수 자동 수집     | 직접 스크립트 작성 필요   |
| 로그 관리 | 다양한 로그 자동화 | 외부 툴이나 수동 처리     |
| 배포 관리 | SSM·콘솔·CLI 지원  | OS·환경별 개별 설정       |
| 확장성    | 대규모 설치/관리    | 개별 서버 관리            |
| 베스트 프랙티스 | 공식 권장       | 관리 불편·불일치 위험     |

## 주요 기능

**상세 시스템 모니터링**
- 메모리 사용률, CPU, 디스크 I/O 및 파일시스템 세부 지표
- 프로세스·애플리케이션별 커스텀 메트릭 수집, Alarm·Dashboard 설정

**통합 로그 수집**
- 시스템 로그, Web/DB 등 애플리케이션 로그, Docker/Container 로그 필터 및 전송
- 로그 필터, 패턴 매칭, 알림 직접 연동

**배포 및 구성 자동화**
- SSM Parameter Store 기반 config 관리
- Run Command, State Manager, Automation을 통한 일괄 배포·업데이트

**Hybrid 환경 지원**
- 온프레미스 및 멀티 클라우드 서버에도 동일 방식으로 설치·운영 가능
- 운영 환경별 config 템플릿 분리 관리

## 주요 활용 사례

- EC2 및 온프레미스 서버의 상세 모니터링(메모리, 디스크 등)
- 애플리케이션·DB·컨테이너 로그 통합 수집 및 분석
- 대규모 서버 환경의 모니터링·로그 자동화 및 일관성 유지
- SSM과 결합하여 운영/구성 변경의 자동화와 배포 일괄 처리
- 장애 대응, 용량 관리, 운영 리포트 및 감사용 데이터 수집

## 기본 워크플로

### 1. CloudWatch Agent 설치
```bash
# SSM을 통한 설치 예시
aws ssm send-command \
  --instance-ids "i-0123456789abcdef0" \
  --document-name "AmazonCloudWatch-ManageAgent" \
  --parameters '{"action":["Install"]}'
```
- 직접 패키지 다운로드(EC2 User Data, yum/apt 등)도 가능

### 2. 설정 파일 등록 및 배포
```bash
# SSM Parameter Store 등록 예시
aws ssm put-parameter \
  --name "AmazonCloudWatch-config" \
  --type "String" \
  --value "$(cat config.json)"
```
- config JSON에 지표 및 로그 수집 설정 후 에이전트 자동 배포·적용

### 3. 로그 및 지표 CloudWatch로 전송
- CloudWatch Logs 및 Metrics에 자동 표시
- 메트릭 Alarm, Dashboard 자동화 활용

## 비용 구조

- CloudWatch Agent 사용 자체는 무료(설치·관리 포함)
- CloudWatch Logs, Metrics 등 AWS 표준 요금(GB당 로그 전송 비용, 추가 메트릭 수집 비용) 적용
- SSM 연동 시 일부 고급 작업에 시간당/API당 요금 부과

## 보안 모범 사례

- SSM 및 Agent에 필요한 최소 IAM 권한만 부여
- Parameter Store, 및 CloudWatch 로그 그룹 암호화(KMS 등)
- 서버/인스턴스별 config 관리로 민감 정보 노출 방지
- 로그 접근 및 모니터링 권한을 역할별 분리 관리

CloudWatch Agent는 AWS 모니터링·로그 수집의 통합 표준으로, 운영 자동화·확장성·보안 등 현대적인 클라우드 인프라 운영에 필수적인 베스트 프랙티스다.
