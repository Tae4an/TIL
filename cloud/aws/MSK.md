# Amazon MSK란?

## 개념 정의
Amazon MSK(Managed Streaming for Apache Kafka)는 Apache Kafka를 사용해 실시간 스트리밍 데이터를 수집·처리·분석하는 완전 관리형 서비스다. AWS가 Kafka 클러스터의 프로비저닝, 설정, 패치, 확장, 고가용성 관리를 자동으로 처리하며, 사용자는 Kafka 인프라 운영 부담 없이 스트리밍 애플리케이션 개발에만 집중할 수 있다.

쉽게 말해 "Apache Kafka를 AWS가 완전히 관리해주는 서비스로, 클릭 몇 번으로 Kafka 클러스터를 생성하고 실시간 데이터 스트리밍 파이프라인을 구축할 수 있다"다.

## 핵심 특징

**완전 관리형(Fully Managed)**
- Kafka 클러스터 및 ZooKeeper 노드 자동 프로비저닝·구성·관리
- 서버 패치, 업그레이드, 장애 복구 자동 처리
- 수동 인프라 관리·설정 불필요
- Apache Kafka 전문가 없이도 운영 가능

**오픈소스 Apache Kafka 그대로 사용**
- 벤더 락인(Lock-in) 없음
- 기존 Kafka API, 도구, 플러그인 그대로 호환
- Kafka 애플리케이션 코드 변경 불필요
- 커뮤니티 에코시스템 활용 가능

**고가용성(High Availability)**
- 기본적으로 3개 가용 영역(Multi-AZ)에 클러스터 분산
- 브로커 장애 시 자동 감지 및 교체
- 애플리케이션 다운타임 없이 복구
- 99.9% SLA 제공

**고도의 보안**
- VPC 네트워크 격리 (프라이빗 서브넷 배치)
- IAM 기반 제어 플레인 권한 관리
- 저장 시 암호화 (AWS KMS)
- 전송 중 암호화 (TLS)
- 인증: TLS 인증서, SASL/SCRAM, IAM
- 데이터 플레인 권한: Apache Kafka ACL

**확장성**
- 브로커 노드 추가로 수평 확장
- 스토리지 자동 확장
- 처리량(Throughput) 증가에 따라 동적 조정
- 페타바이트 규모까지 확장 가능

**AWS 서비스 통합**
- Kinesis Data Analytics, Lambda, S3, Redshift, EMR, Glue 등과 네이티브 통합
- EventBridge, CloudWatch, CloudTrail 모니터링·로깅
- MSK Connect로 외부 시스템 연결 (Kafka Connect)

**관찰성(Observability)**
- CloudWatch로 Kafka 주요 메트릭 자동 수집
- 프로듀서·컨슈머·브로커 성능 모니터링
- 알람 설정으로 이상 징후 감지

## Apache Kafka 기본 개념

### Kafka란?
- 분산 스트리밍 플랫폼
- 실시간 데이터 파이프라인 및 스트리밍 애플리케이션 구축
- 고처리량, 낮은 지연, 확장성, 내구성

### 핵심 구성 요소

**Producer (프로듀서)**
- 데이터를 Kafka Topic으로 전송하는 애플리케이션
- 예: 웹 서버, IoT 센서, 로그 수집기

**Consumer (컨슈머)**
- Topic에서 데이터를 읽는 애플리케이션
- Consumer Group으로 병렬 처리 가능

**Topic (토픽)**
- 메시지가 저장되는 카테고리·피드
- 여러 파티션으로 분산 저장
- 예: `user-activity`, `order-events`, `sensor-data`

**Partition (파티션)**
- Topic을 여러 브로커에 분산 저장하는 단위
- 병렬 처리 및 확장성 제공
- 파티션 수 증가 → 처리량 증가

**Broker (브로커)**
- Kafka 서버 노드
- 데이터를 저장하고 프로듀서·컨슈머 요청 처리
- 클러스터는 여러 브로커로 구성

**ZooKeeper**
- Kafka 클러스터 메타데이터 및 상태 관리
- 브로커 선출, 구성 관리
- MSK가 자동으로 ZooKeeper 관리

## MSK 클러스터 유형

### 1. MSK Provisioned (프로비저닝형)

**특징**
- 브로커 인스턴스 타입·개수 직접 선택
- 예측 가능한 워크로드에 적합
- 세밀한 제어 및 커스터마이징 가능

**브로커 인스턴스 타입**
- `kafka.t3.small`: 개발·테스트
- `kafka.m5.large`, `kafka.m5.xlarge`: 일반 워크로드
- `kafka.m5.4xlarge`, `kafka.m5.12xlarge`: 고처리량

**스토리지**
- EBS 볼륨 (범용 SSD, 프로비저닝된 IOPS)
- 브로커당 최대 16TB
- 자동 스토리지 확장 활성화 가능

### 2. MSK Serverless

**특징**
- 브로커·스토리지 용량 관리 불필요
- 트래픽에 따라 자동 확장·축소
- 사용한 만큼만 과금 (클러스터 시간당 과금 없음)
- 간단한 설정, 운영 부담 최소화

**제한 사항**
- 일부 고급 Kafka 기능 제한
- 커스텀 구성 제한적
- 프로비저닝형보다 비용 높을 수 있음 (고부하 시)

**적합한 경우**
- 트래픽이 불규칙한 워크로드
- 빠른 시작·프로토타입
- 운영 간소화 우선

## 주요 기능

### 1. 자동 복구

**자동 장애 감지**
- 브로커 노드 상태 지속 모니터링
- 하드웨어·소프트웨어 장애 자동 감지

**자동 교체**
- 장애 노드를 새 노드로 자동 교체
- 다운타임 없이 복구
- 데이터 손실 방지 (복제본 유지)

### 2. 자동 패치 및 업그레이드

**Kafka 버전 업그레이드**
- 클릭 한 번으로 최신 Kafka 버전으로 업그레이드
- 롤링 업그레이드로 서비스 중단 최소화
- 보안 패치 자동 적용

### 3. 멀티 AZ 복제

**기본 3개 AZ 배포**
- 브로커가 3개 가용 영역에 분산
- AZ 장애 시에도 서비스 지속
- 데이터 중복 저장 (복제 계수 기본 3)

### 4. MSK Connect (Kafka Connect)

**완전 관리형 Kafka Connect**
- 외부 시스템과 Kafka 간 데이터 통합
- 커넥터로 데이터 소싱·전달 자동화

**지원 커넥터**
- Source Connector: MySQL, PostgreSQL, MongoDB, S3 등 → Kafka
- Sink Connector: Kafka → S3, Redshift, Elasticsearch, Snowflake 등

**노코드 통합**
- 커넥터 플러그인 업로드
- 설정 파일로 데이터 파이프라인 구성
- 코드 작성 불필요

### 5. MSK Replicator

**크로스 리전·크로스 계정 복제**
- Kafka Topic을 다른 리전·계정의 MSK 클러스터로 복제
- 재해 복구(DR) 및 지리적 분산
- 완전 관리형 (커스텀 코드 불필요)

### 6. 보안 기능

**네트워크 격리**
- VPC 내 프라이빗 서브넷 배치
- 보안 그룹으로 트래픽 제어
- 인터넷 게이트웨이 불필요

**암호화**
- 저장 시 암호화: KMS 자동 암호화
- 전송 중 암호화: TLS 1.2 이상
- 브로커 간 통신도 TLS 암호화 가능

**인증 및 권한 부여**
- IAM 인증 (AWS 자격 증명)
- TLS 인증서 기반
- SASL/SCRAM (사용자명·비밀번호, Secrets Manager 저장)
- Apache Kafka ACL로 Topic 접근 제어

### 7. 모니터링 및 로깅

**CloudWatch 통합**
- 브로커 CPU, 메모리, 디스크, 네트워크 메트릭
- Topic별 메시지 입출력 속도
- 컨슈머 랙(Consumer Lag) 모니터링

**CloudTrail 감사**
- 모든 API 호출 로깅
- 클러스터 생성·수정·삭제 추적

**Open Monitoring (Prometheus)**
- Prometheus JMX Exporter 지원
- Grafana 대시보드 연동

## 사용 방법

### MSK 클러스터 생성 (콘솔)

```
1. AWS Console → Amazon MSK → "클러스터 생성" 클릭

2. 클러스터 유형 선택
   - Provisioned or Serverless

3. 클러스터 이름 및 Kafka 버전
   - 이름: my-kafka-cluster
   - Apache Kafka 버전: 3.5.1 (최신)

4. 네트워킹 (Provisioned)
   - VPC 선택
   - 서브넷: 3개 AZ에 각각 1개씩 (프라이빗 서브넷 권장)
   - 보안 그룹: Kafka 포트(9092, 9094) 허용

5. 브로커 구성 (Provisioned)
   - 브로커 인스턴스 타입: kafka.m5.large
   - 브로커 개수: 3 (AZ당 1개)
   - 스토리지: 100GB per broker

6. 보안
   - 암호화: TLS 전송 중 암호화 활성화
   - 인증: IAM 또는 SASL/SCRAM
   - KMS 키 선택 (저장 시 암호화)

7. 모니터링
   - CloudWatch 기본 모니터링 활성화
   - Open Monitoring (Prometheus) 선택 사항

8. 생성 완료
   - 클러스터 상태가 "Active"될 때까지 대기 (15~30분)
```

### Topic 생성 및 데이터 전송

```bash
# 클라이언트 머신 (EC2) 준비
# Kafka 바이너리 다운로드
wget https://archive.apache.org/dist/kafka/3.5.1/kafka_2.13-3.5.1.tgz
tar -xzf kafka_2.13-3.5.1.tgz
cd kafka_2.13-3.5.1

# MSK 클러스터 부트스트랩 서버 확인
# Console → MSK 클러스터 → "클라이언트 정보 보기"
# 예: b-1.mykafka.abc123.c2.kafka.us-east-1.amazonaws.com:9092

# Topic 생성
bin/kafka-topics.sh --create \
  --topic my-topic \
  --bootstrap-server <부트스트랩-서버> \
  --replication-factor 3 \
  --partitions 3

# Producer로 메시지 전송
bin/kafka-console-producer.sh \
  --topic my-topic \
  --bootstrap-server <부트스트랩-서버>
> Hello MSK
> This is a test message

# Consumer로 메시지 수신
bin/kafka-console-consumer.sh \
  --topic my-topic \
  --from-beginning \
  --bootstrap-server <부트스트랩-서버>
```

### IAM 인증 사용 (Python Boto3)

```python
from kafka import KafkaProducer
from aws_msk_iam_sasl_signer import MSKAuthTokenProvider

def oauth_cb():
    token, expiry_ms = MSKAuthTokenProvider.generate_auth_token('us-east-1')
    return token

producer = KafkaProducer(
    bootstrap_servers=['<부트스트랩-서버>'],
    security_protocol='SASL_SSL',
    sasl_mechanism='OAUTHBEARER',
    sasl_oauth_token_provider=oauth_cb,
    value_serializer=lambda v: v.encode('utf-8')
)

producer.send('my-topic', 'Hello from IAM auth')
producer.flush()
```

## 주요 활용 사례

**실시간 로그 분석**
- 애플리케이션·서버 로그를 Kafka로 수집
- Kinesis Data Analytics, EMR, Flink로 실시간 분석
- 이상 탐지, 성능 모니터링

**클릭스트림 분석**
- 웹·모바일 사용자 행동 실시간 추적
- ML 모델로 개인화 추천
- A/B 테스트 실시간 결과 분석

**IoT 데이터 수집**
- 센서·디바이스 데이터를 Kafka로 스트리밍
- Lambda로 실시간 처리
- S3 데이터 레이크로 장기 보관

**이벤트 소싱**
- 마이크로서비스 간 이벤트 기반 통신
- 상태 변화를 이벤트로 기록
- CQRS(Command Query Responsibility Segregation) 패턴 구현

**변경 데이터 캡처(CDC)**
- 데이터베이스 변경 사항을 실시간으로 Kafka에 스트리밍
- Debezium 같은 CDC 도구 사용
- 데이터 레이크, 분석 시스템으로 전파

**실시간 ETL**
- 데이터 소스 → Kafka → 변환 → S3/Redshift
- MSK Connect로 자동화
- 배치 처리 대신 실시간 데이터 통합

## 비용

### Provisioned 클러스터

**브로커 인스턴스 시간**
- 인스턴스 타입·개수별 시간당 과금
- 예: kafka.m5.large × 3 브로커 ≈ $0.21/hour × 3 = $0.63/hour ≈ $450/month

**스토리지**
- EBS 볼륨 GB당 월별 과금
- 예: 300GB (100GB × 3 브로커) × $0.10/GB ≈ $30/month

**데이터 전송**
- 인바운드: 무료
- 아웃바운드: 인터넷으로 GB당 과금
- AZ 간 전송: GB당 과금

### Serverless

**클러스터 시간**
- 시간당 기본 요금

**파티션 시간**
- 파티션 개수 × 시간당 과금

**스토리지**
- 저장된 데이터 GB당 월별 과금

**데이터 전송**
- 입출력 GB당 과금

### 비용 최적화

- 적절한 인스턴스 타입 선택
- 불필요한 파티션·Topic 정리
- 데이터 보존 기간 최적화 (오래된 데이터 삭제)
- 압축 활성화 (스토리지 절감)

## MSK vs EC2 직접 설치 Kafka

| 항목 | MSK | EC2 Kafka |
|------|-----|-----------|
| 관리 | 완전 관리형 | 수동 관리 |
| 설정 | 클릭 몇 번 | 복잡한 수동 설정 |
| 패치·업그레이드 | 자동 | 수동 |
| 고가용성 | 자동 (Multi-AZ) | 수동 구성 |
| 모니터링 | CloudWatch 통합 | 별도 구축 |
| 비용 | 관리형 프리미엄 | 저렴 (인스턴스만) |
| 운영 부담 | 낮음 | 높음 |

## 모범 사례

**적절한 파티션 수 설정**
- 처리량에 따라 파티션 수 조정
- 파티션 수 = 목표 처리량 / 파티션당 처리량
- 너무 많으면 오버헤드 증가

**복제 계수(Replication Factor) 3**
- 기본값 3 유지 (Multi-AZ 대응)
- 데이터 내구성 보장

**브로커 인스턴스 타입 선택**
- 처리량·메시지 크기에 따라 선택
- 프로덕션은 m5.large 이상 권장

**보안 강화**
- VPC 프라이빗 서브넷 배치
- TLS 암호화 활성화
- IAM 인증 사용

**모니터링 활성화**
- CloudWatch 알람 설정
- Consumer Lag 모니터링 (처리 지연 감지)
- 브로커 리소스 사용률 추적

**데이터 보존 기간 최적화**
- 기본 7일 → 필요한 기간으로 조정
- 오래된 데이터 자동 삭제로 스토리지 절감

**압축 활성화**
- Topic 레벨 압축 (gzip, snappy, lz4)
- 네트워크·스토리지 절감

## 요약

Amazon MSK는 Apache Kafka를 완전 관리형으로 제공하는 AWS 서비스로, 클러스터 프로비저닝·패치·고가용성·보안을 자동으로 처리해 사용자는 스트리밍 애플리케이션 개발에만 집중할 수 있다. 오픈소스 Kafka API를 그대로 사용해 벤더 락인 없이 실시간 로그 분석, 클릭스트림, IoT 데이터 수집, 이벤트 소싱, CDC, 실시간 ETL 등 다양한 시나리오에 활용되며, Multi-AZ 고가용성, VPC 격리, TLS/IAM 인증, CloudWatch 통합으로 엔터프라이즈급 보안과 관찰성을 제공한다.
