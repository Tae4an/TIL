# AWS Step Functions란?

## 개념 정의
AWS Step Functions는 여러 AWS 서비스(Lambda, ECS, SNS, Glue 등)를 시각적 워크플로우로 오케스트레이션하는 서버리스 서비스로, JSON 기반 Amazon States Language로 복잡한 비즈니스 로직을 상태 머신(State Machine)으로 정의하고, 각 단계의 실행·재시도·오류 처리를 자동으로 관리한다.

쉽게 말해 "여러 Lambda 함수나 AWS 서비스를 순서대로·병렬로·조건에 따라 실행하고, 실패하면 자동 재시도하며, 실행 과정을 시각적으로 보여주는 서버리스 워크플로우 엔진"이다.

## 핵심 특징

### 시각적 워크플로우
드래그 앤 드롭 또는 JSON 정의로 워크플로우를 설계하고, AWS 콘솔에서 실행 과정을 실시간으로 시각화하며, 각 단계의 입력·출력·오류를 추적할 수 있다.

### 서버리스 오케스트레이션
인프라 관리 불필요하며, Lambda·ECS·SNS·DynamoDB·Glue·EMR·SageMaker 등 200개 이상 AWS 서비스를 직접 호출하고, API Gateway나 EventBridge로 워크플로우를 트리거한다.

### 내장 오류 처리·재시도
각 Task 단계에서 재시도 횟수·백오프 전략·오류 유형별 분기를 정의하고, Catch 블록으로 특정 오류를 처리하며, 실패 시 자동으로 보상 트랜잭션이나 알림을 실행한다.

### 상태 관리
워크플로우 실행 간 상태를 자동 저장하고, 최대 1년간 장기 실행 워크플로우를 지원하며, 수동 승인 단계를 포함할 수 있다.

## 주요 구성 요소

### 1. State Machine (상태 머신)
워크플로우 전체를 정의하는 JSON 문서로, Amazon States Language(ASL)로 작성한다.

**예시 구조**
```json
{
  "Comment": "주문 처리 워크플로우",
  "StartAt": "ValidateOrder",
  "States": {
    "ValidateOrder": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:us-east-1:123456789012:function:ValidateOrder",
      "Next": "ProcessPayment"
    },
    "ProcessPayment": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:us-east-1:123456789012:function:ProcessPayment",
      "Next": "SendConfirmation"
    },
    "SendConfirmation": {
      "Type": "Task",
      "Resource": "arn:aws:states:::sns:publish",
      "End": true
    }
  }
}
```

### 2. State (상태)
워크플로우의 각 단계를 나타내며, 8가지 State 타입이 존재한다.

#### Task State
Lambda 함수 호출, ECS 작업 실행, SNS 발행 등 실제 작업을 수행한다.

#### Choice State
조건 분기 로직을 구현하며, 입력 데이터 기반으로 다음 State를 선택한다.

**예시**: 주문 금액이 100달러 이상이면 승인 필요, 미만이면 자동 승인

#### Parallel State
여러 브랜치를 병렬로 실행하고, 모든 브랜치가 완료될 때까지 대기한다.

**예시**: 재고 확인과 결제 처리를 동시에 실행

#### Map State
배열의 각 항목에 대해 동일한 작업을 병렬로 수행한다.

**예시**: S3에 업로드된 1,000개 이미지를 각각 Lambda로 리사이징

#### Wait State
지정된 시간만큼 워크플로우를 일시 중지한다.

**예시**: 이메일 발송 후 1시간 대기

#### Pass State
입력을 출력으로 그대로 전달하거나 데이터 변환에 사용한다.

#### Succeed State
워크플로우를 성공으로 종료한다.

#### Fail State
워크플로우를 실패로 종료하고 오류 메시지를 기록한다.

### 3. Workflow Types (워크플로우 유형)

#### Standard Workflow
- 최대 실행 시간: 1년
- 최대 25,000개 이벤트 히스토리
- Exactly-once 실행 보장
- 장기 실행·사람 승인 필요 워크플로우에 적합
- 비용: 상태 전환당 $0.025 / 1,000회

#### Express Workflow
- 최대 실행 시간: 5분
- 고처리량·짧은 실행에 최적화
- At-least-once 실행 (중복 가능)
- IoT 데이터 처리, 스트리밍 분석에 적합
- 비용: 실행 횟수·실행 시간 기반 (Standard보다 저렴)

### 4. Integration Patterns (통합 패턴)

#### Request-Response (기본)
서비스를 호출하고 HTTP 응답을 받으면 즉시 다음 단계로 진행한다.

#### Run a Job (.sync)
작업이 완료될 때까지 대기한다.

**예시**: Glue Job, ECS Task, Batch Job 완료 대기

#### Wait for Callback (.waitForTaskToken)
외부 시스템이나 사람의 승인을 대기한다.

**예시**: 이메일로 승인 요청 → 사용자가 승인 링크 클릭 → 워크플로우 재개

## 데이터 흐름 제어

### InputPath
State에 전달할 입력의 일부만 선택한다.

### Parameters
State에 전달할 입력을 재구성한다.

### ResultPath
State 실행 결과를 입력의 특정 경로에 추가한다.

### ResultSelector
State 결과를 원하는 구조로 변환한다.

### OutputPath
State 출력의 일부만 다음 State로 전달한다.

## 주요 활용 사례

### ETL 파이프라인
S3에 파일 업로드 → Glue Crawler 실행 → Glue ETL Job 실행 → Athena 쿼리 → 결과를 Redshift에 적재하는 전체 과정을 자동화한다.

### 주문 처리 워크플로우
주문 검증 → 재고 확인 → 결제 처리 → 배송 요청 → 이메일 발송을 순차적으로 실행하고, 실패 시 보상 트랜잭션(재고 복구, 결제 취소)을 수행한다.

### 머신러닝 파이프라인
데이터 전처리 → SageMaker 모델 훈련 → 모델 평가 → 정확도 80% 이상이면 배포, 미만이면 재훈련하는 워크플로우를 구성한다.

### 사람 승인 프로세스
휴가 신청 → 관리자에게 이메일 발송 → 승인 대기 (.waitForTaskToken) → 승인 시 HR 시스템 업데이트, 거부 시 신청자에게 알림을 보낸다.

### 병렬 데이터 처리
S3에 업로드된 수천 개 이미지를 Map State로 병렬 처리(리사이징, 워터마크 추가, 메타데이터 추출)하고, 결과를 DynamoDB에 저장한다.

### 마이크로서비스 오케스트레이션
여러 Lambda 함수(사용자 서비스, 주문 서비스, 결제 서비스)를 순서대로 호출하고, 각 서비스 간 상태를 관리하며, 전체 트랜잭션을 추적한다.

## Step Functions vs Lambda
| 항목 | Step Functions | Lambda 단독 |
|------|---------------|------------|
| 오케스트레이션 | 여러 Lambda·서비스 조율 | 단일 함수 실행 |
| 실행 시간 | 최대 1년 | 최대 15분 |
| 재시도·오류 처리 | 선언적 정의 | 코드로 구현 필요 |
| 상태 관리 | 자동 저장 | 외부 DB 필요 |
| 시각화 | 실시간 시각화 | 로그 확인 필요 |
| 적합 사례 | 복잡한 워크플로우 | 단일 작업 |

## 비용
- **Standard Workflow**: 상태 전환당 $0.025 / 1,000회
- **Express Workflow**: 실행 횟수·실행 시간 기반 (더 저렴)
- **무료 티어**: 월 4,000 Standard 상태 전환

## 요약
AWS Step Functions는 Lambda·ECS·SNS·Glue·SageMaker 등 여러 AWS 서비스를 JSON 기반 상태 머신으로 오케스트레이션하는 서버리스 워크플로우 엔진으로, Task·Choice·Parallel·Map·Wait 등 8가지 State 타입으로 복잡한 비즈니스 로직을 정의하고, Standard Workflow(최대 1년 실행)와 Express Workflow(최대 5분, 고처리량)를 제공하며, Request-Response·.sync·.waitForTaskToken 통합 패턴으로 동기·비동기·수동 승인 작업을 지원하고, InputPath·Parameters·ResultPath·OutputPath로 데이터 흐름을 제어하며, ETL 파이프라인·주문 처리·ML 파이프라인·사람 승인·병렬 처리·마이크로서비스 오케스트레이션에 활용된다.
