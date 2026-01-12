# Amazon EventBridge란?

## 개념 정의
Amazon EventBridge는 AWS 서비스, SaaS 애플리케이션, 사용자 정의 애플리케이션에서 생성된 이벤트를 실시간으로 수집하고, 규칙(Rules)을 기반으로 필터링한 뒤 Lambda, SQS, SNS, Step Functions 등 다양한 대상(Targets)으로 라우팅하는 서버리스 이벤트 버스 서비스다. 느슨하게 결합된 이벤트 기반 아키텍처를 구축하여 애플리케이션 통합, 자동화, 실시간 처리를 가능하게 한다.

쉽게 말해 "여러 시스템에서 발생하는 이벤트(예: EC2 시작, S3 파일 업로드)를 중앙에서 받아 조건에 맞는 이벤트만 골라 원하는 서비스로 자동 전달해주는 서버리스 이벤트 라우터"다.

## 핵심 특징

### 서버리스 이벤트 버스

**인프라 관리 불필요**
- 서버 프로비저닝·관리 불필요
- 자동 확장
- 고가용성·내구성 자동 보장
- 사용한 이벤트 수만큼만 비용 지불

**이벤트 기반 아키텍처**
- 느슨한 결합(Loose Coupling)
- 이벤트 생산자(Producer)와 소비자(Consumer) 분리
- 각 컴포넌트 독립 배포·확장

### 다양한 이벤트 소스 통합

**AWS 서비스 이벤트**
- 100개 이상 AWS 서비스에서 이벤트 자동 발생
- EC2, S3, Lambda, RDS, ECS, DynamoDB 등
- AWS 상태 변화를 실시간 감지

**SaaS 파트너 이벤트**
- Salesforce, Zendesk, Shopify, Auth0, PagerDuty 등
- 외부 서비스와 직접 통합
- 커스텀 코드 없이 연동

**사용자 정의 애플리케이션**
- 자체 애플리케이션에서 이벤트 발행
- PutEvents API로 이벤트 전송
- 도메인 이벤트 정의

### 강력한 이벤트 필터링

**콘텐츠 기반 필터링**
- 이벤트 패턴(Event Pattern)으로 세밀한 필터링
- JSON 경로 매칭
- 특정 필드 값, 범위, 존재 여부 확인
- 복잡한 조건 조합

**예시: EC2 인스턴스가 'running' 상태로 변경될 때만**
```json
{
  "source": ["aws.ec2"],
  "detail-type": ["EC2 Instance State-change Notification"],
  "detail": {
    "state": ["running"]
  }
}
```

### 풍부한 대상(Target) 지원

**20개 이상 AWS 서비스**
- Lambda: 함수 실행
- Step Functions: 워크플로우 시작
- SQS: 큐에 메시지 전송
- SNS: 토픽으로 발행
- Kinesis Data Streams/Firehose: 스트리밍
- ECS Task: 컨테이너 실행
- Systems Manager Run Command: 명령 실행
- CodePipeline: 파이프라인 트리거

**API Destinations**
- HTTP 엔드포인트로 이벤트 전송
- 온프레미스 시스템 통합
- 서드파티 웹훅
- 속도 제한·인증 관리

**다른 Event Bus**
- 같은 계정의 다른 버스
- 다른 계정·리전의 버스
- 크로스 계정·크로스 리전 라우팅

## 주요 구성 요소

### 1. Event Bus (이벤트 버스)

**개념**
이벤트를 수신하고 규칙에 따라 대상으로 전달하는 라우터다.

**유형**

#### Default Event Bus
- 모든 AWS 계정에 자동 생성
- AWS 서비스 이벤트 수신
- CloudWatch Events와 호환

#### Custom Event Bus
- 사용자가 생성하는 커스텀 버스
- 자체 애플리케이션 이벤트
- SaaS 파트너 이벤트
- 도메인별·팀별 분리

#### Partner Event Bus
- SaaS 파트너가 이벤트 전송
- Salesforce, Zendesk 등
- 파트너 이벤트 소스 활성화 시 자동 생성

### 2. Events (이벤트)

**구조**
JSON 형식의 데이터 객체로, 시스템 상태 변화를 나타낸다.

**이벤트 구조 예시**
```json
{
  "version": "0",
  "id": "6a7e8feb-b491-4cf7-a9f1-bf3703467718",
  "detail-type": "EC2 Instance State-change Notification",
  "source": "aws.ec2",
  "account": "123456789012",
  "time": "2026-01-12T08:48:00Z",
  "region": "us-east-1",
  "resources": [
    "arn:aws:ec2:us-east-1:123456789012:instance/i-1234567890abcdef0"
  ],
  "detail": {
    "instance-id": "i-1234567890abcdef0",
    "state": "running"
  }
}
```

**필수 필드**
- `version`: 이벤트 형식 버전
- `id`: 고유 이벤트 ID
- `detail-type`: 이벤트 유형 설명
- `source`: 이벤트 소스 (예: aws.ec2, myapp.orders)
- `account`: AWS 계정 ID
- `time`: 이벤트 타임스탬프
- `region`: 리전
- `resources`: 관련 리소스 ARN 목록
- `detail`: 이벤트 세부 정보 (자유 형식 JSON)

### 3. Rules (규칙)

**개념**
이벤트를 매칭하고 대상으로 라우팅하는 규칙이다.

**구성 요소**

#### Event Pattern (이벤트 패턴)
특정 이벤트를 매칭하는 필터 조건이다.

**예시 1: S3에 객체 생성 시**
```json
{
  "source": ["aws.s3"],
  "detail-type": ["Object Created"],
  "detail": {
    "bucket": {
      "name": ["my-bucket"]
    }
  }
}
```

**예시 2: EC2 인스턴스 종료 시**
```json
{
  "source": ["aws.ec2"],
  "detail-type": ["EC2 Instance State-change Notification"],
  "detail": {
    "state": ["terminated"]
  }
}
```

**예시 3: 사용자 정의 이벤트**
```json
{
  "source": ["myapp.orders"],
  "detail-type": ["Order Placed"],
  "detail": {
    "order-status": ["confirmed"],
    "amount": [{"numeric": [">", 100]}]
  }
}
```

#### Schedule (스케줄)
정기적으로 이벤트를 생성하는 cron 또는 rate 표현식이다.

**Rate 표현식**
```
rate(5 minutes)
rate(1 hour)
rate(1 day)
```

**Cron 표현식**
```
cron(0 12 * * ? *)  # 매일 정오
cron(0 9 ? * MON *)  # 매주 월요일 오전 9시
```

#### Targets (대상)
규칙이 매칭될 때 이벤트를 전송할 대상이다.

**규칙당 최대 5개 대상**
- 동일 이벤트를 여러 대상으로 전송 가능
- 대상별로 다른 Input Transformer 적용 가능

### 4. Schema Registry (스키마 레지스트리)

**기능**
- 이벤트 구조를 정의·저장·관리
- 코드 생성 지원 (Java, Python, TypeScript)
- 버전 관리
- 개발자 간 스키마 공유

**Schema Discovery**
- EventBridge가 자동으로 이벤트 스키마 추론
- 새 이벤트 타입 자동 감지
- 스키마 레지스트리에 자동 등록

### 5. Archive & Replay (아카이브 및 재생)

**Archive**
- 이벤트를 S3에 자동 저장
- 무제한 보관 또는 기간 지정
- 감사·컴플라이언스
- 특정 이벤트 패턴만 아카이브 가능

**Replay**
- 과거 이벤트를 다시 재생
- 특정 시간 범위 선택
- 디버깅·테스트
- 장애 복구

### 6. Pipes (파이프)

**개념**
단일 소스에서 단일 대상으로의 포인트-투-포인트 통합을 위한 서비스다.

**Event Bus vs Pipes**
- Event Bus: 다대다 라우팅 (many-to-many)
- Pipes: 일대일 통합 (point-to-point)

**Pipes 소스**
- DynamoDB Streams
- Kinesis Data Streams
- SQS
- Kafka (MSK, self-managed)

**고급 기능**
- Filtering: 이벤트 필터링
- Transformation: 이벤트 변환
- Enrichment: API Gateway, Lambda로 데이터 보강

## 작동 방식

### 기본 플로우

**1. 이벤트 생성**
- AWS 서비스가 상태 변화 발생 (예: EC2 시작)
- 또는 애플리케이션이 PutEvents API 호출

**2. Event Bus로 전송**
- 이벤트가 Event Bus에 도착
- Default 또는 Custom Event Bus

**3. 규칙 평가**
- EventBridge가 모든 규칙의 이벤트 패턴 확인
- 매칭되는 규칙 식별
- 하나의 이벤트가 여러 규칙에 매칭 가능

**4. 대상으로 전송**
- 매칭된 규칙의 대상으로 이벤트 전송
- 규칙당 최대 5개 대상
- 대상별로 동시 전송

**5. 재시도**
- 대상이 실패하면 자동 재시도
- 최대 24시간 재시도
- 실패 시 Dead Letter Queue (DLQ) 전송 가능

### 예시 시나리오: EC2 시작 시 Lambda 실행

**1. 규칙 생성**
```json
{
  "EventPattern": {
    "source": ["aws.ec2"],
    "detail-type": ["EC2 Instance State-change Notification"],
    "detail": {
      "state": ["running"]
    }
  },
  "Targets": [
    {
      "Arn": "arn:aws:lambda:us-east-1:123456789012:function:MyFunction",
      "Id": "1"
    }
  ]
}
```

**2. 이벤트 발생**
- EC2 인스턴스가 'running' 상태로 변경
- EC2가 자동으로 이벤트 발행

**3. 규칙 매칭**
- EventBridge가 이벤트 패턴 확인
- 조건 만족 → 규칙 매칭

**4. Lambda 실행**
- 이벤트 데이터를 Lambda로 전송
- Lambda 함수 실행
- 예: Slack 알림, 로그 기록, 추가 작업

## 주요 활용 사례

### 1. 애플리케이션 통합

**시나리오**
- 마이크로서비스 간 통신
- 이벤트 기반 아키텍처 구축

**예시: 주문 처리 시스템**
```
주문 서비스 → EventBridge → [재고 서비스, 결제 서비스, 알림 서비스]
```

**구현**
```python
# 주문 서비스에서 이벤트 발행
import boto3

eventbridge = boto3.client('events')

response = eventbridge.put_events(
    Entries=[
        {
            'Source': 'myapp.orders',
            'DetailType': 'Order Placed',
            'Detail': json.dumps({
                'order-id': '12345',
                'customer-id': 'C001',
                'amount': 150.00
            })
        }
    ]
)
```

**규칙 1: 재고 서비스 Lambda 트리거**
```json
{
  "source": ["myapp.orders"],
  "detail-type": ["Order Placed"]
}
```

**규칙 2: 금액 100 이상일 때 알림**
```json
{
  "source": ["myapp.orders"],
  "detail-type": ["Order Placed"],
  "detail": {
    "amount": [{"numeric": [">=", 100]}]
  }
}
```

### 2. IT 운영 자동화

**시나리오**
- AWS 리소스 상태 변화 모니터링
- 자동 대응·알림

**예시 1: EC2 인스턴스 태그 자동화**
- 새 EC2 인스턴스 생성 감지
- Lambda로 자동 태그 부여
- 비용 추적·거버넌스

**예시 2: 보안 그룹 변경 알림**
- 보안 그룹 규칙 변경 감지
- SNS로 보안팀 알림
- 변경 내역 S3에 로깅

**예시 3: Auto Remediation**
- S3 버킷이 공개로 변경됨 감지
- Lambda로 자동 차단
- Security Hub에 보고

### 3. 스케줄 작업

**시나리오**
- 정기적인 배치 작업
- 리포트 생성
- 데이터 백업

**예시: 매일 밤 데이터베이스 백업**
```json
{
  "ScheduleExpression": "cron(0 2 * * ? *)",
  "Targets": [
    {
      "Arn": "arn:aws:lambda:us-east-1:123456789012:function:BackupDB",
      "Id": "1"
    }
  ]
}
```

**예시: 5분마다 상태 체크**
```json
{
  "ScheduleExpression": "rate(5 minutes)",
  "Targets": [
    {
      "Arn": "arn:aws:lambda:us-east-1:123456789012:function:HealthCheck",
      "Id": "1"
    }
  ]
}
```

### 4. SaaS 통합

**시나리오**
- Salesforce, Zendesk, Shopify 등과 통합
- 외부 서비스 이벤트에 자동 대응

**예시: Salesforce 신규 리드 생성 시**
- Salesforce에서 이벤트 발행
- EventBridge가 수신
- Lambda로 CRM 업데이트
- SNS로 영업팀 알림

### 5. 데이터 파이프라인

**시나리오**
- S3에 파일 업로드 시 ETL 작업 트리거
- 실시간 데이터 처리

**예시: S3 → Lambda → DynamoDB**
- S3에 JSON 파일 업로드
- EventBridge가 Object Created 이벤트 감지
- Lambda로 데이터 파싱
- DynamoDB에 저장

### 6. 멀티 계정·멀티 리전 통합

**시나리오**
- 여러 계정의 이벤트를 중앙 집중화
- 크로스 리전 이벤트 전송

**예시: 중앙 모니터링 계정**
```
[계정 A] EventBridge → [중앙 계정] EventBridge → Lambda (알림·로깅)
[계정 B] EventBridge ↗
[계정 C] EventBridge ↗
```

**리소스 정책으로 권한 부여**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowAccountA",
      "Effect": "Allow",
      "Principal": {"AWS": "arn:aws:iam::111111111111:root"},
      "Action": "events:PutEvents",
      "Resource": "arn:aws:events:us-east-1:222222222222:event-bus/central-bus"
    }
  ]
}
```

### 7. 장애 복구 및 디버깅

**시나리오**
- 과거 이벤트 재처리
- 버그 수정 후 재실행

**예시: Archive & Replay 활용**
- 프로덕션 이벤트를 아카이브
- 버그로 인해 일부 주문 처리 실패
- 버그 수정 후 특정 시간 범위 이벤트 재생
- 누락된 주문 재처리

## 이벤트 변환 (Input Transformer)

### 개념
대상으로 전송하기 전에 이벤트 데이터를 변환한다.

### 사용 사례
- 대상이 필요로 하는 형식으로 변환
- 불필요한 데이터 제거
- 데이터 재구성

### 예시: EC2 이벤트를 SNS로 전송

**원본 이벤트**
```json
{
  "detail": {
    "instance-id": "i-1234567890abcdef0",
    "state": "running"
  },
  "time": "2026-01-12T08:48:00Z"
}
```

**Input Paths**
```json
{
  "instance": "$.detail.instance-id",
  "state": "$.detail.state",
  "time": "$.time"
}
```

**Input Template**
```
"Instance <instance> changed to <state> at <time>"
```

**결과**
```
Instance i-1234567890abcdef0 changed to running at 2026-01-12T08:48:00Z
```

## API Destinations

### 개념
HTTP 엔드포인트로 이벤트를 전송하는 기능이다.

### 주요 기능

**인증 지원**
- OAuth (Client Credentials, Authorization Code)
- API Key
- Basic Auth
- Bearer Token

**속도 제한**
- 초당 요청 수 제한
- Rate Limiting으로 API 보호

**재시도**
- 실패 시 자동 재시도
- 지수 백오프

### 사용 사례

**온프레미스 시스템 통합**
```
EventBridge → API Destinations → 회사 내부 API
```

**서드파티 웹훅**
```
EventBridge → API Destinations → Slack Webhook
```

**외부 SaaS 서비스**
```
EventBridge → API Destinations → PagerDuty API
```

## 비용

### 요금 구조

**이벤트 발행**
- 첫 1억 개 이벤트: $1.00 / 백만 이벤트
- 이후: 가격 감소 (볼륨 할인)

**Schema Discovery**
- 월 $0.10 / 수집된 백만 이벤트

**Event Replay**
- $0.02 / GB

**크로스 리전 전송**
- 데이터 전송 비용 발생

### 무료 티어
- 계정당 월 1억 개 이벤트 발행 무료 (영구)

### 예시 계산

**시나리오: 월 5억 개 이벤트**
- 무료 티어: 1억 개 (무료)
- 과금 대상: 4억 개
- 비용: 4억 / 백만 × $1.00 = $400/월

**시나리오: 월 1천만 개 이벤트**
- 무료 티어 내 → 무료

## EventBridge vs SNS vs SQS

### EventBridge vs SNS

| 항목 | EventBridge | SNS |
|------|-----------|-----|
| 용도 | 이벤트 라우팅·필터링 | 메시지 발행·구독 |
| 필터링 | 콘텐츠 기반 세밀 필터링 | 속성 기반 기본 필터링 |
| 대상 | 20+ AWS 서비스, API Destinations | Lambda, SQS, HTTP, Email, SMS |
| 스케줄 | 지원 (cron, rate) | 미지원 |
| SaaS 통합 | 네이티브 지원 | 미지원 |
| Schema Registry | 지원 | 미지원 |
| 사용 사례 | 이벤트 기반 아키텍처 | 알림·브로드캐스트 |

### EventBridge vs SQS

| 항목 | EventBridge | SQS |
|------|-----------|-----|
| 패턴 | 이벤트 버스 (Pub/Sub) | 메시지 큐 (Point-to-Point) |
| 필터링 | 규칙 기반 | 소비자가 처리 |
| 여러 소비자 | 지원 (규칙당 5개 대상) | 미지원 (단일 소비자) |
| 메시지 보관 | 미지원 (즉시 전송) | 최대 14일 |
| 재시도 | 자동 (24시간) | 소비자 책임 |
| 사용 사례 | 이벤트 라우팅 | 작업 큐, 디커플링 |

### 통합 사용 (권장)

**EventBridge + SQS**
- EventBridge로 이벤트 필터링
- SQS로 버퍼링·비동기 처리
- 소비자가 속도 조절

**EventBridge + SNS**
- EventBridge로 복잡한 라우팅
- SNS로 브로드캐스트

## 모범 사례

### 1. Event Bus 설계

**도메인별 분리**
- 주문 버스, 결제 버스, 재고 버스
- 관심사 분리
- 팀별 독립 관리

**Default vs Custom**
- AWS 서비스 이벤트: Default Event Bus
- 애플리케이션 이벤트: Custom Event Bus

### 2. 이벤트 명명 규칙

**일관된 Source 네이밍**
```
myapp.orders
myapp.payments
myapp.inventory
```

**명확한 Detail-Type**
```
Order Placed
Payment Processed
Inventory Updated
```

### 3. 이벤트 스키마 버전 관리

**Schema Registry 활용**
- 이벤트 구조 문서화
- 버전 관리
- 하위 호환성 유지

### 4. 재시도 및 DLQ 설정

**재시도 정책**
- 최대 재시도 횟수 설정
- 재시도 간격 (지수 백오프)

**Dead Letter Queue**
- 실패한 이벤트를 SQS로 전송
- 나중에 수동 처리·분석

### 5. 보안

**최소 권한 원칙**
- IAM 역할로 대상 권한 관리
- 리소스 정책으로 크로스 계정 접근 제어

**암호화**
- 이벤트 데이터 암호화 (AWS KMS)

### 6. 모니터링

**CloudWatch 메트릭**
- 규칙별 호출 횟수
- 실패한 호출
- 대상 전송 시간

**CloudWatch Logs**
- 이벤트 로깅 (디버깅용)

### 7. 비용 최적화

**이벤트 필터링**
- 불필요한 이벤트 전송 방지
- 세밀한 이벤트 패턴

**무료 티어 활용**
- 월 1억 개까지 무료

## 제한 사항

### 이벤트 크기
- 최대 256KB
- 대용량 데이터는 S3에 저장 후 참조 전달

### 규칙당 대상 수
- 최대 5개
- 더 필요하면 SNS 토픽으로 브로드캐스트

### 규칙 수
- Event Bus당 기본 300개
- 최대 2,000개까지 자동 승인 요청 가능

### 처리량
- PutEvents: 초당 수천 건
- 리전별 제한 (할당량 증가 요청 가능)

### 크로스 리전 지연
- 네트워크 지연 발생
- 실시간성 요구 시 동일 리전 사용

## 요약

Amazon EventBridge는 AWS 서비스, SaaS 애플리케이션, 사용자 정의 애플리케이션에서 발생하는 이벤트를 서버리스 이벤트 버스로 수집하고, 규칙(Event Pattern 또는 Schedule)으로 필터링한 뒤 Lambda, Step Functions, SQS, SNS, ECS Task 등 최대 5개 대상으로 라우팅하여 느슨하게 결합된 이벤트 기반 아키텍처를 구축하는 서비스다. Default Event Bus는 AWS 서비스 이벤트를, Custom Event Bus는 애플리케이션·SaaS 이벤트를 처리하며, Schema Registry로 이벤트 구조 관리, Archive & Replay로 과거 이벤트 재처리, API Destinations로 HTTP 엔드포인트 통합, Input Transformer로 이벤트 변환, Pipes로 포인트-투-포인트 통합을 지원한다. 애플리케이션 통합, IT 운영 자동화, 스케줄 작업, SaaS 통합, 데이터 파이프라인, 멀티 계정·멀티 리전 이벤트 집중화, 장애 복구에 활용되며, 월 1억 개 이벤트까지 무료이고 이후 백만 개당 $1로 과금되며, 도메인별 Event Bus 분리, 일관된 명명 규칙, Schema Registry 활용, DLQ 설정, CloudWatch 모니터링이 모범 사례다.
