# Amazon EMR이란?

## 개념 정의
Amazon EMR(Elastic MapReduce)은 Apache Hadoop, Apache Spark, Presto, Apache Hive와 같은 오픈소스 빅데이터 프레임워크를 사용하여 대규모 데이터를 빠르고 비용 효율적으로 처리·분석할 수 있는 AWS의 완전 관리형 클러스터 플랫폼이다. 수백~수천 대의 EC2 인스턴스 또는 컨테이너를 자동으로 프로비저닝·관리·모니터링하여 페타바이트(PB) 규모의 데이터를 분산 처리하며, 컴퓨팅과 스토리지를 분리해 각각 독립적으로 확장할 수 있다.

쉽게 말해 "Apache Spark, Hadoop 같은 빅데이터 도구를 클릭 몇 번으로 수백 대 서버 클러스터에 설치·구성·운영할 수 있는 관리형 빅데이터 플랫폼"이다.

## 핵심 특징

### 완전 관리형 서비스

**클러스터 자동 관리**
- EC2 인스턴스 프로비저닝 자동화
- 오픈소스 프레임워크 설치·구성 자동
- 클러스터 모니터링 및 상태 관리
- 실패한 작업 자동 재시도
- 성능 저하 인스턴스 자동 교체

**최신 오픈소스 버전 제공**
- Spark, Hadoop, Hive, Presto 등 최신 안정 버전
- 버그 수정 및 보안 패치 자동 적용
- 버전 간 호환성 관리
- 업데이트 부담 최소화

### 탄력적 확장 (Elastic)

**컴퓨팅·스토리지 분리**
- 컴퓨팅: EC2 인스턴스 클러스터
- 스토리지: S3 (EMRFS 사용)
- 각각 독립적으로 확장 가능
- S3의 티어드 스토리지 활용

**Auto Scaling**
- 클러스터 사용률에 따라 인스턴스 자동 추가·제거
- EMR Managed Scaling: AWS가 자동으로 최적 크기 결정
- CloudWatch 메트릭 기반 스케일링
- 사용한 만큼만 비용 지불

**동적 리소스 조정**
- 1대부터 수천 대까지 확장
- 수초 내 컴퓨팅 리소스 증감
- 워크로드 특성에 맞춘 인스턴스 타입 선택

### 비용 효율성

**온디맨드·스팟·예약 인스턴스 지원**
- 온디맨드: 표준 요금
- 스팟 인스턴스: 최대 90% 절감 (일시적 워크로드)
- 예약 인스턴스: 최대 75% 절감 (장기 실행)
- 온프레미스 대비 절반 이하 비용

**클러스터 실행 시간만 과금**
- 클러스터 종료 시 과금 중단
- 일시적 클러스터로 비용 최적화
- 스토리지는 S3에 저장해 비용 절감

### 유연성

**다양한 오픈소스 프레임워크 지원**
- Apache Spark (인메모리 처리, ML)
- Apache Hadoop (MapReduce, YARN)
- Apache Hive (SQL 쿼리)
- Presto (대화형 SQL)
- Apache HBase (NoSQL 데이터베이스)
- Apache Flink (스트림 처리)
- TensorFlow, MXNet (머신러닝)

**커스터마이징**
- 커스텀 AMI로 클러스터 시작
- Bootstrap Actions로 초기화 스크립트 실행
- 실행 중인 클러스터에서 애플리케이션 재구성
- Docker 컨테이너로 의존성 관리

### 보안

**네트워크 보안**
- VPC 내에서 클러스터 실행
- EC2 보안 그룹 자동 구성
- 프라이빗 서브넷 배치 가능
- PrivateLink로 안전한 접근

**데이터 암호화**
- 저장 시 암호화 (S3 SSE, EBS 암호화)
- 전송 중 암호화 (TLS/SSL)
- 클라이언트 측 암호화 지원
- AWS KMS 통합

**인증·권한**
- Kerberos 강력 인증
- IAM 역할 기반 접근 제어
- Lake Formation 세밀한 데이터 접근 제어
- Apache Ranger 통합

### 고가용성·안정성

**자동 모니터링**
- CloudWatch로 클러스터 메트릭 수집
- 비정상 인스턴스 자동 교체
- 실패한 태스크 자동 재시도
- 고가용성 자동 페일오버

## 주요 구성 요소

### EMR 클러스터 아키텍처

**마스터 노드 (Master Node)**
- 클러스터 관리·조정
- YARN ResourceManager 실행
- HDFS NameNode 실행
- 작업 상태 추적
- 단일 또는 다중 마스터 (HA)

**코어 노드 (Core Node)**
- 데이터 저장 (HDFS)
- 태스크 실행 (YARN NodeManager)
- 최소 1개 이상 필요
- 스토리지·컴퓨팅 역할 모두 수행

**태스크 노드 (Task Node, 선택 사항)**
- 태스크 실행만 담당 (데이터 저장 안 함)
- 컴퓨팅 용량 추가
- 스팟 인스턴스로 실행하기 적합
- 동적으로 추가·제거 가능

### 스토리지 옵션

**HDFS (Hadoop Distributed File System)**
- 클러스터 내부 분산 스토리지
- 코어 노드의 로컬 디스크 사용
- 빠른 I/O 성능
- 클러스터 종료 시 데이터 손실
- 일시적 데이터·중간 결과 저장

**EMRFS (EMR File System)**
- S3를 직접 읽기·쓰기
- 클러스터와 독립적인 영구 스토리지
- 무제한 확장 가능
- 비용 효율적
- 클러스터 종료 후에도 데이터 유지
- 대부분의 워크로드 권장

**로컬 파일 시스템**
- EC2 인스턴스 스토어
- 일시적 데이터·캐시용

### 지원 애플리케이션 프레임워크

**Apache Spark**
- 인메모리 데이터 처리
- Hadoop MapReduce보다 최대 100배 빠름
- 배치 처리, 스트림 처리, ML, 그래프 분석
- Spark SQL, Spark Streaming, MLlib, GraphX
- EMR Runtime for Spark: 표준 Spark 대비 3배 이상 빠름

**Apache Hadoop**
- MapReduce 분산 처리 프레임워크
- YARN 리소스 관리
- HDFS 분산 스토리지
- 대규모 배치 작업

**Apache Hive**
- SQL 기반 데이터 웨어하우스
- HiveQL로 S3, HDFS 데이터 쿼리
- AWS Glue Data Catalog 통합
- ETL 작업 수행

**Presto**
- 대화형 SQL 쿼리 엔진
- 수 초 내 쿼리 결과 반환
- S3, HDFS, RDS, Cassandra 등 다양한 소스 조인
- ANSI SQL 지원

**Apache HBase**
- NoSQL 컬럼형 데이터베이스
- 실시간 읽기·쓰기
- 수십억 행, 수백만 컬럼 스케일
- S3 백업 지원

**Apache Flink**
- 스트림 처리 프레임워크
- 실시간 이벤트 처리
- 정확히 한 번(Exactly-once) 시맨틱
- 낮은 지연 시간

**TensorFlow, MXNet, PyTorch**
- 딥러닝·머신러닝 프레임워크
- GPU 인스턴스 지원
- 분산 학습

## EMR 배포 옵션

### 1. EMR on EC2 (전통적 방식)

**특징**
- EC2 인스턴스 클러스터
- 마스터·코어·태스크 노드 구성
- HDFS 또는 EMRFS 스토리지
- 가장 일반적인 배포 방식

**적합한 경우**
- HDFS 사용 필요
- 인스턴스 타입 세밀 제어
- 커스텀 AMI 사용

### 2. EMR on EKS (Elastic Kubernetes Service)

**특징**
- Kubernetes 클러스터에서 Spark 작업 실행
- 동일 EKS 클러스터에서 다른 애플리케이션과 공존
- 컨테이너 기반 작업 격리
- Kubernetes 네이티브 도구 사용

**적합한 경우**
- 이미 EKS 사용 중
- 여러 팀이 동일 클러스터 공유
- Kubernetes 기반 DevOps 파이프라인

### 3. EMR Serverless

**특징**
- 서버·클러스터 관리 불필요
- 작업 제출 시 자동으로 리소스 프로비저닝
- 초 단위 과금 (vCPU, 메모리, 스토리지)
- 사용하지 않을 때 비용 없음

**적합한 경우**
- 간헐적 작업
- 인프라 관리 최소화
- 빠른 시작 필요

## 주요 활용 사례

### 1. ETL (Extract, Transform, Load)

**시나리오**
- S3의 원시 로그·트랜잭션 데이터를 정제·변환
- Parquet, ORC 같은 최적화된 포맷으로 변환
- 데이터 웨어하우스·데이터 레이크 구축

**방법**
- Spark 또는 Hive로 ETL 작업 작성
- S3에서 데이터 읽기
- 변환·집계·필터링 수행
- 결과를 S3에 저장
- Redshift, Athena로 분석

**예시**
```python
# PySpark ETL
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("ETL").getOrCreate()

# S3에서 로그 데이터 읽기
raw_logs = spark.read.json("s3://bucket/raw-logs/")

# 변환
cleaned_logs = raw_logs.filter(raw_logs.status == 200) \
                       .select("user_id", "timestamp", "url")

# Parquet 형식으로 저장
cleaned_logs.write.parquet("s3://bucket/cleaned-logs/")
```

### 2. 빅데이터 분석

**시나리오**
- 클릭스트림 분석
- 웹 로그 분석
- 센서·IoT 데이터 분석
- 사용자 행동 패턴 분석

**방법**
- EMR에서 Spark 또는 Presto 실행
- S3의 대규모 데이터셋 쿼리
- 집계·그룹화·조인 수행
- 결과를 대시보드에 시각화

**예시: 클릭스트림 분석**
- 수십억 건의 클릭 이벤트 분석
- 사용자 세그먼트별 전환율 계산
- 광고 효과 측정
- A/B 테스트 결과 분석

### 3. 머신러닝·AI

**시나리오**
- 추천 시스템
- 이상 탐지
- 예측 모델링
- 자연어 처리 (NLP)
- 이미지 분류

**방법**
- Spark MLlib로 ML 파이프라인 구축
- TensorFlow, PyTorch로 딥러닝 모델 훈련
- 분산 학습으로 대규모 데이터셋 처리
- 결과 모델을 S3에 저장
- SageMaker와 통합

**예시: 추천 시스템**
```python
# Spark MLlib ALS (Collaborative Filtering)
from pyspark.ml.recommendation import ALS

als = ALS(userCol="userId", itemCol="movieId", ratingCol="rating")
model = als.fit(ratings_df)

# 사용자에게 영화 추천
recommendations = model.recommendForAllUsers(10)
```

### 4. 실시간 스트림 처리

**시나리오**
- Kinesis, Kafka 스트림 데이터 처리
- 실시간 로그 분석
- 사기 탐지
- IoT 센서 모니터링

**방법**
- Spark Streaming 또는 Flink 사용
- Kinesis Data Streams에서 데이터 수집
- 실시간 집계·필터링·알림
- 결과를 S3, DynamoDB, Redshift에 저장

**예시: 실시간 트위터 감성 분석**
- 트위터 스트림 수집
- NLP로 감성 분석 (긍정/부정)
- 실시간 대시보드 업데이트

### 5. 로그 분석·보안 감사

**시나리오**
- CloudTrail, VPC Flow Logs, ALB 로그 분석
- 보안 이벤트 탐지
- 규정 준수 리포트 생성

**방법**
- S3에 저장된 로그를 EMR로 읽기
- Spark 또는 Hive로 쿼리
- 이상 징후 탐지 (비정상 로그인, 대량 다운로드)
- Security Hub, Splunk로 전송

### 6. 유전체학·과학 연구

**시나리오**
- DNA 시퀀싱 데이터 분석
- 단백질 구조 예측
- 기후 모델링

**방법**
- 대규모 과학 데이터셋 병렬 처리
- 커스텀 바이오인포매틱스 도구 실행
- GPU 인스턴스로 시뮬레이션 가속

## 클러스터 생성 및 작업 실행

### 클러스터 생성 방법

**1. AWS Management Console**
- 웹 UI에서 클릭으로 클러스터 생성
- 간단한 설정 옵션
- 초보자에게 적합

**2. AWS CLI**
```bash
aws emr create-cluster \
  --name "MyCluster" \
  --release-label emr-6.9.0 \
  --applications Name=Spark Name=Hive \
  --ec2-attributes KeyName=mykey,SubnetId=subnet-12345 \
  --instance-type m5.xlarge \
  --instance-count 3 \
  --use-default-roles
```

**3. AWS SDK (Python Boto3)**
```python
import boto3

emr = boto3.client('emr')

response = emr.run_job_flow(
    Name='MySparkCluster',
    ReleaseLabel='emr-6.9.0',
    Instances={
        'MasterInstanceType': 'm5.xlarge',
        'SlaveInstanceType': 'm5.xlarge',
        'InstanceCount': 3,
    },
    Applications=[{'Name': 'Spark'}],
    JobFlowRole='EMR_EC2_DefaultRole',
    ServiceRole='EMR_DefaultRole'
)
```

**4. AWS Step Functions, CloudFormation**
- 인프라 as 코드
- 자동화·재사용 가능

### 작업 제출 방법

**1. EMR Steps**
- 클러스터 생성 시 또는 실행 중 단계 추가
- Spark, Hive, Pig 스크립트 실행

**예시: Spark 작업 제출**
```bash
aws emr add-steps \
  --cluster-id j-XXXXXXXXXXXXX \
  --steps Type=Spark,Name="MySparkJob",\
ActionOnFailure=CONTINUE,\
Args=[--deploy-mode,cluster,--master,yarn,\
s3://bucket/my-script.py]
```

**2. EMR Notebooks**
- Jupyter 기반 대화형 노트북
- 클러스터와 독립적 (off-cluster)
- 데이터 탐색·시각화·프로토타이핑

**3. SSH로 마스터 노드 접속**
- 직접 spark-submit, hive 명령 실행
- 디버깅·테스트

**4. Livy REST API**
- 프로그래밍 방식으로 Spark 작업 제출
- 외부 애플리케이션 통합

## 성능 최적화

### 1. 적절한 인스턴스 타입 선택

**컴퓨팅 집약적 워크로드**
- C5, C6i 인스턴스 (고 CPU)
- Spark, Presto 쿼리

**메모리 집약적 워크로드**
- R5, R6i 인스턴스 (고 메모리)
- 대규모 조인, 캐싱

**범용 워크로드**
- M5, M6i 인스턴스
- 균형잡힌 CPU·메모리

**GPU 워크로드**
- P3, G4dn 인스턴스
- 딥러닝, 이미지 처리

### 2. 스팟 인스턴스 활용

**태스크 노드에 스팟 사용**
- 비용 최대 90% 절감
- 중단 허용 가능한 컴퓨팅 작업
- 마스터·코어 노드는 온디맨드 유지

**스팟 인스턴스 베스트 프랙티스**
- 여러 인스턴스 타입 혼합
- 여러 가용 영역 사용
- Graceful 중단 처리

### 3. 데이터 포맷 최적화

**컬럼형 포맷 사용**
- Parquet, ORC
- 압축률 높음 (50~75% 절감)
- 필요한 컬럼만 읽기
- 쿼리 성능 향상

**파티셔닝**
- 날짜, 지역, 카테고리별 분할
- 불필요한 데이터 스캔 방지

**예시: Parquet 변환**
```python
df.write.partitionBy("year", "month").parquet("s3://bucket/data/")
```

### 4. 적절한 병렬 처리 수준

**Spark 파티션 수 조정**
- 너무 적으면: 병렬화 부족
- 너무 많으면: 오버헤드 증가
- 일반적으로 코어 수의 2~3배

```python
df = spark.read.parquet("s3://bucket/data/").repartition(200)
```

### 5. 캐싱 활용

**자주 사용하는 데이터셋 캐시**
```python
df.cache()  # 메모리에 캐시
df.persist(StorageLevel.MEMORY_AND_DISK)  # 메모리+디스크
```

### 6. EMR Managed Scaling 사용

**자동 확장**
- CloudWatch 메트릭 기반
- YARN 메모리·vCore 사용률 모니터링
- 자동으로 노드 추가·제거
- 최적 비용·성능 균형

### 7. S3 최적화

**S3 Select 사용**
- S3에서 필터링 후 데이터 전송
- 네트워크 전송량 감소

**S3DistCp 사용**
- 대규모 데이터 복사·병합
- 소형 파일 통합 (Small Files Problem 해결)

## 비용 최적화

### 1. 일시적 클러스터 (Transient Cluster)

**작업 완료 시 자동 종료**
- 클러스터 생성 → 작업 실행 → 자동 종료
- 유휴 시간 비용 제거
- 데이터는 S3에 영구 저장

**자동 종료 설정**
```bash
--auto-terminate
```

### 2. 장기 실행 클러스터 (Long-Running Cluster)

**여러 작업 재사용**
- 클러스터 시작 시간 절약
- 대화형 분석, 개발 환경
- 유휴 시간 주의 필요

### 3. 스팟 인스턴스 혼합

**Core Node Fleet**
- 온디맨드 + 스팟 혼합
- 비용 절감과 안정성 균형

**Task Node**
- 100% 스팟 인스턴스
- 최대 비용 절감

### 4. Reserved Instances

**장기 실행 워크로드**
- 1년 또는 3년 약정
- 최대 75% 절감

### 5. EMR Serverless

**간헐적 작업**
- 초 단위 과금
- 유휴 비용 없음
- 사용량 기반 비용

### 6. 적절한 클러스터 크기

**과다 프로비저닝 방지**
- 작업 특성에 맞춘 인스턴스 수·타입
- EMR Managed Scaling으로 자동 조정

## 보안 모범 사례

### 1. 네트워크 격리

**프라이빗 서브넷 배치**
- 인터넷에 직접 노출 방지
- NAT Gateway로 아웃바운드 트래픽
- VPC 엔드포인트로 AWS 서비스 접근

**보안 그룹 엄격 설정**
- 필요한 포트만 개방
- 소스 IP 제한

### 2. 데이터 암호화

**저장 시 암호화**
- S3 서버 측 암호화 (SSE-S3, SSE-KMS)
- EBS 볼륨 암호화
- HDFS 암호화

**전송 중 암호화**
- TLS/SSL 활성화
- 노드 간 통신 암호화

### 3. 인증·권한

**IAM 역할 사용**
- EC2 인스턴스 프로파일
- 서비스 역할
- 최소 권한 원칙

**Kerberos 인증**
- 강력한 사용자 인증
- Active Directory 통합

**Lake Formation**
- 테이블·컬럼·행 레벨 접근 제어
- 중앙 집중식 권한 관리

### 4. 감사·모니터링

**CloudTrail 로깅**
- 모든 EMR API 호출 기록

**CloudWatch 모니터링**
- 클러스터 메트릭
- 커스텀 알람 설정

**S3 버킷 로깅**
- 데이터 접근 추적

## EMR vs 다른 빅데이터 솔루션

### EMR vs Athena

| 항목 | EMR | Athena |
|------|-----|--------|
| 아키텍처 | 클러스터 기반 | 서버리스 |
| 용도 | 복잡한 ETL, ML, 배치 처리 | 임시 SQL 쿼리 |
| 쿼리 언어 | Spark, Hive, Presto 등 다양 | SQL만 |
| 성능 | 높음 (대규모 처리) | 중간 (쿼리 속도) |
| 비용 | 클러스터 시간당 | 스캔한 데이터량 |
| 유연성 | 매우 높음 (커스텀 코드) | 제한적 |
| 학습 곡선 | 높음 | 낮음 |

### EMR vs Glue

| 항목 | EMR | AWS Glue |
|------|-----|----------|
| 아키텍처 | EC2 클러스터 관리 필요 | 완전 서버리스 |
| 용도 | 복잡한 빅데이터 처리 | 간단~중간 ETL |
| 커스터마이징 | 매우 높음 | 제한적 |
| 비용 | 클러스터 시간당 | DPU 시간당 |
| 시작 시간 | 수 분 | 수 초 |
| 적합한 경우 | 대규모·복잡한 작업 | 간단한 ETL |

### EMR vs Redshift

| 항목 | EMR | Redshift |
|------|-----|----------|
| 목적 | 빅데이터 처리·분석 | 데이터 웨어하우스·BI |
| 데이터 위치 | S3, HDFS | Redshift 내부 |
| 쿼리 속도 | 중간~빠름 | 매우 빠름 (최적화) |
| SQL vs 프로그래밍 | 둘 다 | SQL 중심 |
| 확장성 | 무제한 (S3) | 수십 PB |
| 적합한 경우 | ETL, ML, 탐색적 분석 | 정기 리포트, 대시보드 |

## 요약

Amazon EMR은 Apache Spark, Hadoop, Presto, Hive 같은 오픈소스 빅데이터 프레임워크를 EC2 클러스터·EKS·서버리스 환경에서 실행하여 페타바이트 규모의 데이터를 분산 처리하는 완전 관리형 플랫폼으로, 인스턴스 프로비저닝·소프트웨어 설치·클러스터 모니터링을 자동화하고 컴퓨팅과 스토리지(S3 EMRFS)를 분리해 각각 독립적으로 확장하며 Auto Scaling으로 사용량에 따라 노드를 자동 추가·제거한다. ETL, 빅데이터 분석, 머신러닝, 실시간 스트림 처리, 로그 분석, 유전체학 연구 등에 활용되며, EMR Runtime for Spark는 표준 Spark 대비 3배 이상 빠르고 온디맨드·스팟·예약 인스턴스 혼합으로 온프레미스 대비 절반 이하 비용을 달성할 수 있다. 성능 최적화를 위해서는 적절한 인스턴스 타입 선택, 태스크 노드에 스팟 인스턴스 활용, Parquet·ORC 같은 컬럼형 포맷 사용, 파티셔닝, EMR Managed Scaling, 캐싱 전략이 필수적이며, 일시적 클러스터로 유휴 비용 제거, 프라이빗 서브넷 배치, 데이터 암호화, IAM 역할·Kerberos·Lake Formation 통합으로 보안을 강화해야 한다.
