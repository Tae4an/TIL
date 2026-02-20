# AWS Fault Tolerance란?

## 개념 정의

Fault Tolerance(내결함성)는 시스템의 일부 구성 요소에 장애가 발생하더라도 **전체 서비스가 중단 없이 계속 정상 작동하는 능력**이다. 단순히 장애 후 빠르게 복구하는 것을 넘어, **장애가 발생했다는 사실 자체를 사용자가 인식하지 못할 정도로 중단 없이 운영**되는 것을 목표로 한다. 중복(Redundancy) 구성과 자동화를 통해 구현하며, 하나의 구성 요소가 실패해도 예비 구성 요소가 100% 작업을 이어받는 구조다.

쉽게 말해, "서버 한 대가 죽어도, DB가 장애를 겪어도, 사용자는 아무것도 못 느끼게 시스템이 알아서 계속 돌아가는 것"이다.

## Fault Tolerance vs High Availability

이 두 개념은 자주 혼용되지만 수준이 다르다.

| 항목 | High Availability (고가용성) | Fault Tolerance (내결함성) |
|------|----------------------------|--------------------------|
| 목표 | 다운타임 최소화 (빠른 복구) | 다운타임 Zero (중단 없는 운영) |
| 장애 시 동작 | 수초~수분의 짧은 중단 후 복구 | 사용자가 인식 불가능한 수준으로 무중단 |
| 데이터 손실 | 일부 가능성 있음 | 없음 (실시간 복제 기반) |
| 비용 | 상대적으로 낮음 | 높음 (완전한 이중화 필요) |
| 예시 | Multi-AZ RDS (장애 시 수초 Failover) | Aurora (자동 실시간 복제, 무중단) |

## AWS Fault Tolerance 핵심 구성 요소

### Auto Scaling Group (ASG)
- EC2 인스턴스 수를 자동 조정
- Health Check로 비정상 인스턴스 감지 시 자동 교체
- Multi-AZ로 인스턴스 분산 배포
- 트래픽 급증·인스턴스 장애에도 서비스 지속

### Elastic Load Balancer (ELB)
- 여러 EC2·컨테이너·Lambda에 트래픽 자동 분산
- Health Check로 비정상 대상 자동 제외, 정상 대상에만 트래픽 전달
- ASG와 조합 시 강력한 내결함성 구현

### Amazon RDS Multi-AZ
- Primary DB 장애 시 Standby로 자동 Failover
- 동기 복제로 데이터 손실 없음
- Aurora는 6개 복사본을 3개 AZ에 분산 저장하여 더 강력한 내결함성 제공

### Amazon S3
- 기본적으로 단일 리전 내 다중 AZ에 데이터 자동 복제
- 내구성 99.999999999%(11 nines)
- 단일 구성 요소 장애에 독립적으로 데이터 보존

### Amazon SQS
- 메시지를 큐에 저장하여 Producer와 Consumer를 분리
- Consumer 서비스 장애 시에도 메시지 유실 없이 큐에 대기
- Visibility Timeout으로 처리 실패 시 자동 재처리

### Amazon Route 53
- Health Check 기반 자동 DNS Failover
- Primary 엔드포인트 장애 시 Secondary로 자동 전환
- Multi-Region 내결함성 구현의 핵심

### AWS Lambda
- 서버리스 특성상 인프라 장애에 독립적
- 자동 리트라이 (비동기 호출 시 최대 2회)
- 여러 AZ에 자동 분산 실행

### Amazon DynamoDB
- 데이터를 3개 AZ에 자동 복제
- Global Tables로 멀티 리전 내결함성
- 서버리스로 인프라 장애에 독립적

## Fault Tolerance 설계 패턴

### 1. 이중화 (Redundancy)
- 모든 단일 장애 지점(Single Point of Failure, SPOF) 제거
- EC2 → Multi-AZ ASG
- DB → Multi-AZ RDS 또는 Aurora
- 네트워크 → 다중 경로, 다중 NAT Gateway

### 2. 장애 격리 (Fault Isolation)
- AZ 격리: 각 AZ를 독립적인 장애 도메인으로 취급
- 셀 기반 아키텍처: 전체 사용자를 N개 셀로 분할하여 장애 전파 방지
- 마이크로서비스 분리: 서비스 간 강한 의존성 제거, 독립 배포

### 3. 장애 감지·자동 복구
- CloudWatch Alarms로 이상 지표 탐지
- Auto Scaling Health Check로 비정상 인스턴스 자동 교체
- Route 53 Health Check로 자동 DNS Failover

### 4. 데이터 복제
- S3 Cross-Region Replication: 리전 간 데이터 복제
- RDS Read Replica → 리전 간 복제
- DynamoDB Global Tables: 멀티 리전 실시간 복제

### 5. 비동기 처리 (Queue 기반 분리)
- SQS, SNS, EventBridge로 Producer·Consumer 분리
- 하위 서비스 장애 시 메시지 큐에서 대기, 서비스 복구 후 처리 재개
- 카스케이딩 장애(Cascading Failure) 방지

### 6. Circuit Breaker 패턴
- 하위 서비스 장애 시 연속 호출 차단, 빠른 실패(Fail Fast) 반환
- 장애 서비스에 대한 과부하 방지
- AWS App Mesh, ALB Retry Policy, Lambda Destinations 등으로 구현

## Trusted Advisor와 내결함성

AWS Trusted Advisor는 내결함성 관련 자동 점검 항목을 제공한다.

- ELB 뒤에 EC2 인스턴스가 1개만 있는 경우 경고
- Multi-AZ를 사용하지 않는 RDS 인스턴스 탐지
- 단일 AZ에만 배포된 Auto Scaling Group 탐지
- EBS 스냅샷 미생성 볼륨 경고

## 정리

AWS Fault Tolerance(내결함성)는 일부 구성 요소 장애 시에도 사용자가 중단을 인식하지 못하는 수준으로 서비스가 계속 운영되는 능력으로, High Availability보다 높은 수준의 이중화와 자동화를 요구하며, Auto Scaling, ELB, RDS Multi-AZ, S3, SQS, Route 53, DynamoDB 등 AWS 핵심 서비스로 구현하고, 이중화·장애 격리·자동 감지·복구·비동기 처리·Circuit Breaker 패턴을 조합하여 단일 장애 지점을 제거하고, 장애 발생 시 자동으로 예비 구성 요소가 작업을 이어받아 무중단 서비스를 달성한다.
