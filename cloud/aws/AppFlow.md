# Amazon AppFlow란?

## 개념 정의
Amazon AppFlow는 코드 작성 없이 SaaS(Software-as-a-Service) 애플리케이션과 AWS 서비스 간에 데이터를 안전하게 양방향으로 전송하고 통합하는 완전 관리형 통합 서비스다. Salesforce, SAP, Google Analytics, Slack, ServiceNow 같은 외부 SaaS와 S3, Redshift 같은 AWS 서비스를 클릭 몇 번으로 연결하며, 데이터 전송을 "플로우(Flow)"라는 단위로 관리한다.

쉽게 말해 "Salesforce 같은 SaaS 앱과 AWS를 코드 없이 드래그 앤 드롭으로 연결해서 데이터를 자동으로 주고받게 하는 서비스"다.

## 핵심 특징

**노코드(No-Code) 통합**
- 시각적 인터페이스로 몇 분 만에 데이터 플로우 생성
- API 통합 코드 작성 불필요
- 인증·데이터 변환·API 관리 자동 처리
- 비개발자도 데이터 통합 가능

**사전 구축된 커넥터(Pre-built Connectors)**
- 50개 이상의 SaaS 애플리케이션 네이티브 지원
- Salesforce, SAP, Google Analytics, Slack, ServiceNow, Zendesk, Marketo 등
- AWS 서비스 통합: S3, Redshift, EventBridge, Lookout for Metrics
- 커넥터가 인증·API 호출·데이터 형식 처리

**양방향 데이터 전송**
- SaaS → AWS (데이터 수집)
- AWS → SaaS (역방향 ETL, 데이터 활성화)
- 예: S3에 저장된 ML 모델 예측 결과를 Salesforce로 전송

**대규모 데이터 전송**
- 플로우당 최대 100GB 데이터 처리
- 한 번에 수백만 개 레코드 전송
- 시스템 리소스 프로비저닝 불필요

**데이터 변환 및 필터링**
- 전송 중 데이터 변환
- 필드 매핑, 계산, 연결, 분할
- 조건부 필터링으로 필요한 데이터만 전송
- JSON Path 표현식 지원

**유연한 스케줄링**
- **온디맨드(On-demand)**: 수동 실행
- **스케줄(On schedule)**: Cron 표현식으로 주기적 실행
- **이벤트 기반(Event-driven)**: CDC(Change Data Capture), Platform Events

**보안**
- 전송 중·저장 시 모두 암호화
- AWS PrivateLink 지원 (인터넷 우회, 프라이빗 전송)
- IAM 기반 접근 제어
- VPC 엔드포인트로 프라이빗 네트워크 전송

**데이터 카탈로그 통합**
- S3로 전송된 데이터를 AWS Glue Data Catalog에 자동 등록
- Athena, Redshift Spectrum으로 즉시 쿼리 가능
- 메타데이터 자동 관리

## 지원 소스 및 목적지

### SaaS 애플리케이션 (소스/목적지)

**CRM & 영업**
- Salesforce (Sales Cloud, Service Cloud)
- SAP OData
- Zendesk Sell

**마케팅 & 광고**
- Google Analytics 4
- Facebook Ads
- Marketo
- Pardot

**고객 지원**
- ServiceNow
- Zendesk Support
- Slack

**재무 & ERP**
- SAP S/4HANA
- QuickBooks Online
- Infor Nexus

**데이터 & 분석**
- Google Ads
- Snowflake
- Datadog
- Amplitude

**협업**
- Slack
- Asana
- Jira

### AWS 서비스 (목적지)

**스토리지 & 데이터 레이크**
- Amazon S3
- Amazon Redshift
- Amazon Timestream

**분석 & 모니터링**
- Amazon EventBridge
- Amazon Lookout for Metrics

**머신러닝**
- Amazon SageMaker (Data Wrangler 통합)

**기타**
- Upsolver (실시간 데이터 레이크)
- Salesforce (역방향 ETL)

## 주요 구성 요소

### 1. 플로우(Flow)

**정의**
- 데이터 전송 작업의 기본 단위
- 소스 → 목적지 매핑 + 변환 규칙 + 스케줄

**플로우 상태**
- **Active**: 활성화되어 실행 가능
- **Draft**: 초안 상태
- **Errored**: 오류 발생
- **Deprecated**: 더 이상 사용 안 함

**플로우 실행**
- 플로우당 최대 100GB 처리
- 전체 전송(Full Transfer) 또는 증분 전송(Incremental Transfer)

### 2. 소스(Source)

**정의**
- 데이터를 가져올 출발지
- SaaS 애플리케이션 또는 AWS 서비스

**소스 설정**
- 커넥터 선택 (예: Salesforce)
- 인증 (OAuth 2.0, API 키 등)
- 객체 선택 (예: Salesforce의 Account, Contact)
- 필드 선택

### 3. 목적지(Destination)

**정의**
- 데이터를 전송할 도착지
- AWS 서비스 또는 SaaS 애플리케이션

**목적지 설정**
- S3 버킷, Redshift 테이블, Salesforce 객체 등
- 파일 형식 (Parquet, CSV, JSON)
- 파티셔닝 설정 (연도/월/일 등)

### 4. 트리거(Trigger)

**온디맨드(On-demand)**
- 수동으로 플로우 실행
- 테스트·일회성 전송에 적합

**스케줄(On schedule)**
- 주기적 자동 실행
- Cron 표현식: 매시간, 매일, 매주, 매월
- **전체 전송(Full)**: 매번 모든 데이터
- **증분 전송(Incremental)**: 마지막 실행 이후 추가·변경된 데이터만

**이벤트 기반(Event-driven)**
- 특정 이벤트 발생 시 자동 실행
- **CDC (Change Data Capture)**: 데이터 변경 감지 시 즉시 전송
- **Platform Events**: Salesforce Platform Event 같은 실시간 이벤트

### 5. 데이터 변환

**필드 매핑**
- 소스 필드 → 목적지 필드 매핑
- 예: Salesforce의 `FirstName` → S3의 `first_name`

**변환 함수**
- 연결(Concatenate): 여러 필드 결합
- 분할(Split): 필드를 여러 개로 나누기
- 마스킹(Mask): 민감한 데이터 마스킹
- 조건부 로직(Conditional): IF-THEN 규칙
- 계산(Math): 숫자 계산

**필터링**
- 조건부 필터로 특정 레코드만 전송
- 예: Status = 'Active'인 고객만 전송

### 6. 데이터 검증

**자동 검증**
- 전송된 데이터의 무결성 자동 검증
- 레코드 수 일치 확인
- 필수 필드 검증

**오류 처리**
- 실패한 레코드 자동 재시도
- 오류 로그 CloudWatch로 전송

## 작동 방식

### 기본 플로우 생성 과정

1. **소스 연결**
```
AppFlow 콘솔 → Create flow
  → 소스 선택 (예: Salesforce)
  → OAuth 인증
  → 객체 선택 (예: Account)
```

2. **목적지 설정**
```
목적지 선택 (예: S3)
  → S3 버킷 선택
  → 파일 형식 (Parquet)
  → 파티셔닝 (연도/월/일)
```

3. **데이터 매핑**
```
필드 매핑 설정
  → 자동 매핑 또는 수동 매핑
  → 변환 함수 적용 (선택)
```

4. **트리거 설정**
```
트리거 유형 선택
  → 온디맨드 or 스케줄 or 이벤트
  → 증분 전송 활성화 (선택)
```

5. **검토 및 실행**
```
설정 검토
  → 플로우 생성
  → 테스트 실행
```

### 실행 예시: Salesforce → S3

1. 사용자가 플로우 실행 (수동 또는 스케줄)
2. AppFlow가 Salesforce API 호출
3. Salesforce에서 Account 데이터 읽기
4. 필드 변환 적용 (이름 변경, 계산 등)
5. Parquet 형식으로 변환
6. S3 버킷에 저장 (`s3://bucket/year=2025/month=12/day=30/data.parquet`)
7. AWS Glue Data Catalog에 자동 등록 (선택)
8. CloudWatch에 실행 로그 기록

## 주요 활용 사례

**고객 360도 뷰**
- Salesforce + Zendesk + Google Analytics 데이터를 S3로 통합
- Redshift에서 고객 여정 전체 분석
- 마케팅·영업·지원 데이터 통합

**SaaS 데이터 분석**
- SaaS 애플리케이션 데이터를 S3 데이터 레이크로 수집
- Athena, QuickSight로 비즈니스 인텔리전스
- 데이터 사일로 제거

**ML 모델 훈련**
- Salesforce, ServiceNow 데이터를 S3로 수집
- SageMaker Data Wrangler로 데이터 준비
- 모델 훈련 및 배포

**역방향 ETL (Reverse ETL)**
- S3·Redshift의 분석 결과를 Salesforce로 전송
- ML 예측 점수를 CRM에 업데이트
- 데이터 활성화 (Operational Analytics)

**실시간 이벤트 처리**
- Salesforce Platform Event를 EventBridge로 전송
- Lambda로 실시간 처리
- 이벤트 기반 워크플로 자동화

**규정 준수 및 아카이브**
- SaaS 데이터를 S3 Glacier로 장기 보관
- 감사 로그 자동 수집
- GDPR, HIPAA 요구사항 충족

**비즈니스 인텔리전스**
- 여러 SaaS 소스를 Redshift로 통합
- QuickSight 대시보드 생성
- 경영진 리포팅 자동화

## 비용

**프리 티어**
- 없음 (사용량 기반 과금만)

**과금 모델**
- **플로우 실행당**: 처리된 데이터량 기준
- **가격**: 약 $0.001 per flow run (최소 요금 적용)
- **데이터 전송량**: GB당 과금

**예시 비용**
- 1GB 데이터 전송 플로우 실행: 약 $0.10
- 월 1,000번 실행 (각 1GB): 약 $100/month
- 대용량 전송 시 볼륨 할인 적용

**비용 최적화 팁**
- 증분 전송으로 전송량 최소화
- 필터링으로 불필요한 데이터 제외
- 스케줄 최적화 (너무 잦은 실행 방지)
- 필요한 필드만 선택

## AppFlow vs 기타 통합 도구

| 도구 | 용도 | 특징 |
|------|------|------|
| **AppFlow** | SaaS ↔ AWS 통합 | 노코드, SaaS 특화, 양방향 |
| **AWS Glue** | 범용 ETL | 코드 필요, 유연, 복잡한 변환 |
| **AWS Data Pipeline** | 데이터 이동·변환 | 온프레미스 지원, 복잡한 워크플로 |
| **EventBridge** | 이벤트 라우팅 | 실시간 이벤트, 경량 |
| **Step Functions** | 워크플로 오케스트레이션 | 복잡한 로직, 상태 관리 |

## 모범 사례

**증분 전송 활용**
- 초기 전체 전송 후 증분 전송으로 전환
- 전송량·비용 대폭 절감
- 데이터 최신 상태 유지

**데이터 파티셔닝**
- S3 목적지는 연도/월/일 파티셔닝
- Athena 쿼리 성능 향상
- 비용 절감 (스캔 데이터량 감소)

**오류 모니터링**
- CloudWatch 알람 설정
- 플로우 실패 시 즉시 알림
- 정기적 실행 로그 리뷰

**보안 강화**
- PrivateLink로 프라이빗 전송
- IAM 최소 권한 원칙
- 민감한 데이터 마스킹

**데이터 카탈로그 활용**
- Glue Data Catalog 자동 등록 활성화
- 메타데이터 검색·발견 용이
- 데이터 거버넌스 강화

**플로우 이름 규칙**
- 명확한 네이밍 컨벤션
- 예: `salesforce-accounts-to-s3-daily`
- 관리·추적 용이

**변환 최소화**
- 복잡한 변환은 Glue에서 처리
- AppFlow는 간단한 매핑·필터링만
- 성능·안정성 향상

**테스트 플로우**
- 프로덕션 전 테스트 플로우로 검증
- 작은 데이터셋으로 테스트
- 점진적 확장

## 제약 사항

**플로우당 데이터 크기**
- 최대 100GB per flow run
- 초과 시 여러 플로우로 분할

**커넥터 제한**
- 지원하는 SaaS만 연결 가능
- 커스텀 커넥터는 제한적

**변환 기능**
- 복잡한 변환은 제한적
- Glue, Lambda와 조합 권장

**실시간 성능**
- 준실시간(Near real-time), 완전한 실시간 아님
- CDC는 수초~수분 지연

## 요약

Amazon AppFlow는 Salesforce, SAP, Google Analytics 같은 SaaS 애플리케이션과 S3, Redshift 같은 AWS 서비스를 코드 없이 안전하게 연결하는 완전 관리형 통합 서비스다. 50개 이상의 사전 구축 커넥터, 노코드 시각적 인터페이스, 양방향 데이터 전송, 이벤트 기반 자동화, PrivateLink 보안을 제공하며, 고객 360도 뷰 구축, ML 모델 훈련, 역방향 ETL, 비즈니스 인텔리전스 등 다양한 데이터 통합 시나리오를 간단하게 구현할 수 있다.
