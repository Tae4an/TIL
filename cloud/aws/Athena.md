# Amazon Athena란?

## 개념 정의
Amazon Athena는 Amazon S3에 저장된 데이터를 표준 SQL을 사용해 직접 분석할 수 있는 서버리스 대화형 쿼리 서비스다. 데이터를 별도의 데이터베이스로 로드하거나 복잡한 ETL 파이프라인을 구축할 필요 없이, S3 버킷의 데이터에 대해 즉시 SQL 쿼리를 실행할 수 있으며, 인프라 관리가 전혀 필요 없고 쿼리한 데이터 스캔량에 대해서만 비용을 지불한다.

쉽게 말해 "S3에 저장된 로그, CSV, JSON, Parquet 파일 등을 데이터베이스에 넣지 않고도 SQL로 바로 분석할 수 있는 서버리스 빅데이터 분석 도구"다.

## 핵심 특징

### 서버리스 아키텍처

**인프라 관리 불필요**
- 서버 프로비저닝·구성·관리 불필요
- 소프트웨어 업데이트·패치 자동
- 용량 계획·확장 걱정 없음
- 콘솔 로그인 후 즉시 쿼리 실행

**자동 확장**
- 쿼리를 자동으로 병렬 실행
- 데이터셋 크기와 사용자 수에 따라 자동 스케일
- 대량 쿼리도 빠른 응답 시간
- 여러 시설의 컴퓨팅 리소스 활용

### S3 직접 쿼리

**데이터 이동 불필요**
- S3 버킷의 데이터를 그대로 쿼리
- ETL 없이 즉시 분석 가능
- 데이터 레이크 아키텍처에 최적
- S3를 단일 데이터 소스로 활용

**표준 SQL 지원**
- ANSI SQL 표준 사용
- Presto 엔진 기반
- 익숙한 SQL 문법으로 학습 곡선 낮음
- DDL, DML, DQL 쿼리 모두 지원

### 다양한 데이터 포맷 지원

**정형·반정형 데이터**
- CSV, TSV, JSON
- Apache Parquet (컬럼형)
- Apache ORC (컬럼형)
- Apache Avro
- 로그 파일 (Apache 로그, CloudFront 로그)

**압축 포맷**
- GZIP, Snappy, LZO, Brotli

### 고가용성·내구성

**안정적인 실행**
- 여러 가용 영역의 컴퓨팅 리소스 활용
- 특정 시설 장애 시 자동 라우팅
- S3의 99.999999999% 내구성 활용
- 다중 시설·다중 디바이스 중복 저장

### 비용 효율성

**종량제 요금**
- 스캔한 데이터 TB당 과금 (약 $5/TB)
- 쿼리 실행하지 않으면 과금 없음
- S3 저장 비용만 추가 (S3 요금제)
- 실패한 쿼리는 과금 안 됨

**비용 절감 방법**
- 컬럼형 포맷(Parquet, ORC) 사용: 30~90% 절감
- 데이터 압축
- 파티셔닝으로 스캔 범위 축소

## 작동 방식

### 기본 플로우

**1. 데이터 준비**
- S3 버킷에 데이터 업로드
- 원하는 폴더 구조로 구성
- 파티셔닝 전략 적용 (선택)

**2. 테이블 정의**
- AWS Glue Data Catalog에 테이블 스키마 등록
- 콘솔 DDL 마법사 또는 `CREATE TABLE` 문 사용
- 컬럼명, 데이터 타입, S3 위치 지정

**3. 쿼리 실행**
- Athena 콘솔 쿼리 에디터에서 SQL 작성
- 또는 JDBC/ODBC, CLI, SDK, API 사용
- 쿼리 제출

**4. 병렬 처리**
- Athena가 쿼리를 병렬 실행
- S3에서 필요한 데이터만 스캔
- 여러 노드에서 동시 처리

**5. 결과 반환**
- 대부분 수 초 내 결과 반환
- 콘솔에 결과 표시
- S3에 쿼리 결과 자동 저장

### 예시: CloudTrail 로그 분석

**시나리오**
- S3에 저장된 CloudTrail 로그에서 특정 IAM 사용자의 활동 조회

**1. 테이블 생성**
```sql
CREATE EXTERNAL TABLE cloudtrail_logs (
  eventversion STRING,
  useridentity STRUCT<
    type:STRING,
    principalid:STRING,
    arn:STRING,
    accountid:STRING,
    username:STRING>,
  eventtime STRING,
  eventname STRING,
  awsregion STRING,
  sourceipaddress STRING
)
PARTITIONED BY (year STRING, month STRING, day STRING)
ROW FORMAT SERDE 'com.amazon.emr.hive.serde.CloudTrailSerde'
STORED AS INPUTFORMAT 'com.amazon.emr.cloudtrail.CloudTrailInputFormat'
OUTPUTFORMAT 'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat'
LOCATION 's3://my-cloudtrail-bucket/AWSLogs/123456789012/CloudTrail/';
```

**2. 파티션 추가**
```sql
ALTER TABLE cloudtrail_logs ADD PARTITION (year='2026', month='01', day='08')
LOCATION 's3://my-cloudtrail-bucket/AWSLogs/123456789012/CloudTrail/2026/01/08/';
```

**3. 쿼리 실행**
```sql
SELECT eventtime, eventname, sourceipaddress
FROM cloudtrail_logs
WHERE useridentity.username = 'admin-user'
  AND year = '2026' AND month = '01' AND day = '08'
ORDER BY eventtime DESC;
```

## 주요 구성 요소

### 1. AWS Glue Data Catalog

**역할**
- Athena의 메타데이터 저장소
- 테이블 스키마, 파티션 정보 저장
- 데이터베이스·테이블 관리

**Glue Crawler**
- S3 데이터를 자동 검색
- 스키마 자동 추론
- 테이블·파티션 자동 생성
- 정기 스케줄 실행 가능

### 2. 쿼리 에디터

**기능**
- 웹 기반 SQL 에디터
- 쿼리 작성·실행·결과 조회
- 쿼리 이력 관리
- 저장된 쿼리 재사용

### 3. 쿼리 결과 저장소

**S3 버킷**
- 모든 쿼리 결과 자동 저장
- CSV 형식으로 저장
- 메타데이터 파일 포함
- 다운로드·공유 가능

### 4. 데이터 소스 커넥터 (Federated Query)

**Lambda 기반 커넥터**
- S3 외 다양한 데이터 소스 쿼리
- RDS, Aurora, Redshift, DynamoDB
- ElastiCache, DocumentDB, HBase
- 온프레미스 데이터베이스 (MySQL, PostgreSQL, SQL Server)
- 여러 소스 조인 가능

**작동 방식**
- 데이터 소스별 Lambda 함수 배포
- Athena Query Federation SDK 사용
- 100줄 미만 코드로 커넥터 구축 가능

### 5. 워크그룹 (Workgroups)

**기능**
- 쿼리 실행 단위 격리
- 사용자·팀·애플리케이션별 구분
- 비용 추적·제어
- 쿼리 결과 위치 지정
- 쿼리 제한·타임아웃 설정

### 6. JDBC/ODBC 드라이버

**BI 도구 연동**
- Tableau, Power BI, Looker, Qlik 연결
- SQL 클라이언트 (DBeaver, SQL Workbench) 연결
- 프로그래밍 언어 SDK (Python, Java, Node.js)

## 주요 활용 사례

### 1. 로그 분석

**AWS 서비스 로그**
- VPC Flow Logs: 네트워크 트래픽 분석
- ELB Access Logs: 로드 밸런서 요청 패턴
- CloudFront Logs: CDN 성능·사용량
- CloudTrail Logs: API 호출·감사 추적
- S3 Access Logs: 버킷 접근 이력

**애플리케이션 로그**
- 웹 서버 로그 (Apache, Nginx)
- 애플리케이션 에러 로그
- 사용자 행동 로그

**예시 쿼리: VPC Flow Logs에서 거부된 트래픽 조회**
```sql
SELECT srcaddr, dstaddr, dstport, action, COUNT(*) as count
FROM vpc_flow_logs
WHERE action = 'REJECT'
  AND date = '2026-01-08'
GROUP BY srcaddr, dstaddr, dstport, action
ORDER BY count DESC
LIMIT 10;
```

### 2. 비즈니스 인텔리전스·분석

**시나리오**
- 판매 데이터 분석
- 고객 행동 패턴 분석
- 제품 성과 리포트

**방법**
- S3에 트랜잭션 데이터 저장 (CSV, Parquet)
- Athena로 집계·분석 쿼리
- QuickSight로 대시보드 생성

**예시: 월별 매출 분석**
```sql
SELECT 
  DATE_TRUNC('month', order_date) as month,
  SUM(amount) as total_sales,
  COUNT(DISTINCT customer_id) as unique_customers
FROM sales_data
WHERE year = '2025'
GROUP BY DATE_TRUNC('month', order_date)
ORDER BY month;
```

### 3. 데이터 레이크 쿼리

**아키텍처**
```
다양한 소스 → S3 데이터 레이크 (원시 데이터)
                    ↓
              Glue ETL (변환)
                    ↓
            S3 (Parquet 형식)
                    ↓
                 Athena
                    ↓
               QuickSight
```

**장점**
- 데이터 웨어하우스 구축 불필요
- 저렴한 비용
- 유연한 스키마

### 4. 임시 쿼리 (Ad-hoc Query)

**시나리오**
- 일회성 데이터 조회
- 탐색적 데이터 분석 (EDA)
- 데이터 품질 검증

**장점**
- 즉시 실행
- 인프라 불필요
- 사용한 만큼만 비용

### 5. AWS Cost and Usage Report (CUR) 분석

**시나리오**
- AWS 비용·사용량 상세 분석
- 서비스별·계정별 비용 추적
- 비용 최적화 기회 발견

**방법**
- CUR을 S3에 저장
- Athena로 쿼리
- 월별·서비스별·태그별 집계

**예시: 서비스별 월 비용**
```sql
SELECT 
  line_item_product_code,
  SUM(line_item_unblended_cost) AS cost
FROM cur_table
WHERE year = '2026' AND month = '01'
GROUP BY line_item_product_code
ORDER BY cost DESC;
```

### 6. 보안·컴플라이언스

**감사 추적**
- CloudTrail 로그 분석
- 의심스러운 활동 탐지
- 규정 준수 리포트

**예시: 루트 계정 사용 탐지**
```sql
SELECT eventtime, eventname, sourceipaddress
FROM cloudtrail_logs
WHERE useridentity.type = 'Root'
  AND year = '2026'
ORDER BY eventtime DESC;
```

### 7. 머신러닝 데이터 준비

**시나리오**
- 대규모 데이터셋 전처리
- 피처 엔지니어링
- 데이터 샘플링

**방법**
- Athena로 SQL 변환
- 결과를 S3에 저장
- SageMaker로 모델 훈련

## 성능 최적화

### 1. 파티셔닝 (Partitioning)

**개념**
- 데이터를 논리적 그룹으로 분할
- 스캔 범위 축소
- 쿼리 성능 향상·비용 절감

**파티션 키 선택**
- 자주 필터링하는 컬럼 선택
- 날짜(년/월/일), 지역, 카테고리 등
- 카디널리티 적절한 컬럼

**예시: 날짜별 파티셔닝**
```
s3://bucket/data/year=2026/month=01/day=08/
```

**쿼리 시 파티션 활용**
```sql
SELECT * FROM logs
WHERE year='2026' AND month='01' AND day='08';
```

### 2. 컬럼형 포맷 사용

**Parquet, ORC 권장**
- 컬럼별 압축·인코딩
- 필요한 컬럼만 읽기
- 30~90% 비용 절감
- 쿼리 성능 대폭 향상

**CSV vs Parquet 비교**
- CSV: 전체 행 스캔 필요
- Parquet: 필요한 컬럼만 읽음

### 3. 데이터 압축

**지원 압축 포맷**
- GZIP: 높은 압축률
- Snappy: 빠른 압축·해제
- ZSTD: 균형잡힌 성능

**장점**
- 스토리지 비용 절감
- 스캔 데이터 크기 감소
- 네트워크 전송 비용 절감

### 4. 적절한 파일 크기 유지

**권장 사항**
- 파일 크기: 128MB ~ 1GB
- 작은 파일 너무 많으면 성능 저하
- 큰 파일로 병합

**Small Files Problem**
- 수천 개의 작은 파일보다 수십 개의 큰 파일이 효율적
- S3 LIST 오버헤드 감소

### 5. WHERE 절 최적화

**파티션 키 사용**
```sql
-- 좋은 예
WHERE year='2026' AND month='01'

-- 나쁜 예
WHERE event_date BETWEEN '2026-01-01' AND '2026-01-31'
```

**필요한 컬럼만 선택**
```sql
-- 좋은 예
SELECT user_id, event_name FROM logs

-- 나쁜 예
SELECT * FROM logs
```

### 6. JOIN 최적화

**작은 테이블을 왼쪽에**
- Athena는 왼쪽 테이블을 메모리에 로드
- 큰 테이블 JOIN 작은 테이블 순서

**예시**
```sql
-- 좋은 예
SELECT *
FROM small_dimension_table d
JOIN large_fact_table f ON d.id = f.dimension_id

-- 나쁜 예
SELECT *
FROM large_fact_table f
JOIN small_dimension_table d ON f.dimension_id = d.id
```

### 7. CTAS (Create Table As Select) 활용

**개념**
- 쿼리 결과를 새 테이블로 저장
- 최적화된 포맷으로 변환
- 자주 사용하는 집계 테이블 생성

**예시**
```sql
CREATE TABLE optimized_sales
WITH (
  format='PARQUET',
  parquet_compression='SNAPPY',
  partitioned_by = ARRAY['year', 'month']
)
AS
SELECT *
FROM raw_sales
WHERE year >= '2025';
```

## Athena vs 다른 AWS 분석 서비스

### Athena vs Redshift

| 항목 | Athena | Redshift |
|------|--------|----------|
| 아키텍처 | 서버리스 | 클러스터 기반 (프로비저닝 필요) |
| 데이터 위치 | S3 (데이터 레이크) | Redshift 내부 스토리지 |
| 쿼리 속도 | 중간 (수 초~분) | 빠름 (밀리초~초) |
| 비용 모델 | 스캔한 데이터량 과금 | 시간당 클러스터 비용 |
| 사용 사례 | 임시 쿼리, 탐색적 분석 | 정기 리포트, 복잡한 BI |
| 데이터 로딩 | 불필요 (S3 직접 쿼리) | COPY 명령으로 로드 필요 |
| 유지보수 | 불필요 | 클러스터 관리 필요 |

### Athena vs EMR

| 항목 | Athena | EMR (Hadoop/Spark) |
|------|--------|-------------------|
| 설정 | 즉시 사용 | 클러스터 구성 필요 |
| 쿼리 언어 | SQL만 | Spark, Hive, Presto, Pig 등 |
| 유연성 | 제한적 | 매우 높음 (커스텀 코드) |
| 비용 | 쿼리당 과금 | 클러스터 시간당 과금 |
| 학습 곡선 | 낮음 (SQL만) | 높음 (분산 처리 지식 필요) |
| 사용 사례 | SQL 쿼리 | 복잡한 데이터 처리, ML |

## 비용 구조

### 쿼리 비용

**표준 가격**
- $5 per TB (스캔한 데이터)
- 최소 10MB 단위 반올림
- 실패한 쿼리·취소한 쿼리 무료

**예시 계산**
- 100GB 데이터 스캔: $0.50
- 1TB 데이터 스캔: $5.00
- 10TB 데이터 스캔: $50.00

### 추가 비용

**S3 스토리지**
- 데이터 저장: S3 요금 (약 $0.023/GB/월)
- 쿼리 결과 저장: S3 요금

**데이터 전송**
- 리전 간 전송 시 전송 비용

**Glue Data Catalog**
- 100만 객체까지 무료
- 초과 시 월 $1/10만 객체

### 비용 최적화 팁

**1. 컬럼형 포맷 사용**
- Parquet로 전환 시 90% 절감 가능

**2. 파티셔닝**
- 불필요한 파티션 스캔 방지

**3. SELECT * 지양**
- 필요한 컬럼만 선택

**4. 데이터 압축**
- GZIP, Snappy 압축

**5. 쿼리 결과 재사용**
- 결과를 S3에 저장해 재활용

## 보안

### 접근 제어

**IAM 정책**
- Athena API 작업 권한
- S3 버킷 읽기 권한
- Glue Data Catalog 접근 권한

**예시 IAM 정책**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "athena:StartQueryExecution",
        "athena:GetQueryExecution",
        "athena:GetQueryResults"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::my-data-bucket/*",
        "arn:aws:s3:::my-data-bucket"
      ]
    }
  ]
}
```

### 데이터 암호화

**저장 시 암호화**
- S3 서버 측 암호화 (SSE-S3, SSE-KMS)
- 쿼리 결과도 암호화

**전송 중 암호화**
- TLS/SSL 자동 적용

### Lake Formation 통합

**세밀한 접근 제어**
- 테이블·컬럼·행 레벨 권한
- 중앙 집중식 권한 관리
- 감사 로깅

## 제한 사항

### 쿼리 제한

**동시 쿼리**
- 워크그룹당 기본 20개 동시 쿼리
- 서비스 한도 증가 요청 가능

**쿼리 타임아웃**
- 최대 30분

**결과 크기**
- 단일 쿼리 결과: 최대 100GB

### DML 제한

**지원하는 작업**
- SELECT, INSERT INTO, CREATE TABLE AS

**지원하지 않는 작업**
- UPDATE, DELETE (트랜잭션 미지원)
- MERGE (일부 테이블 포맷 제외)

## 모범 사례

### 1. 파티셔닝 전략

**적절한 파티션 키**
- 자주 필터링하는 컬럼
- 날짜, 지역, 카테고리
- 파티션 수: 수천 개 이하 유지

### 2. 데이터 포맷 선택

**Parquet 사용**
- 대부분의 경우 최적
- 스키마 진화 지원
- 효율적인 압축

### 3. 쿼리 최적화

**EXPLAIN 사용**
- 쿼리 실행 계획 확인
- 병목 지점 식별

**쿼리 결과 재사용**
- CTAS로 중간 테이블 생성
- 자주 사용하는 집계 저장

### 4. 워크그룹 활용

**팀별 격리**
- 부서·프로젝트별 워크그룹
- 비용 추적 용이
- 쿼리 제한 설정

### 5. 모니터링

**CloudWatch 메트릭**
- 쿼리 실행 시간
- 스캔 데이터량
- 실패한 쿼리

**CloudTrail 로깅**
- 모든 Athena API 호출 기록
- 감사·컴플라이언스

## 요약

Amazon Athena는 S3에 저장된 데이터를 표준 SQL로 직접 쿼리할 수 있는 서버리스 대화형 분석 서비스로, 인프라 관리 없이 즉시 사용 가능하며 스캔한 데이터량(TB당 약 $5)에 대해서만 비용을 지불한다. Presto 엔진 기반으로 CSV, JSON, Parquet, ORC 등 다양한 포맷을 지원하고, AWS Glue Data Catalog와 통합되어 테이블 스키마를 관리하며, VPC Flow Logs, CloudTrail, ELB 로그 등 AWS 서비스 로그 분석, 비즈니스 인텔리전스, 데이터 레이크 쿼리, AWS 비용 분석, 보안 감사 등 다양한 용도로 활용된다. 성능 최적화를 위해서는 파티셔닝(날짜, 지역별 분할), 컬럼형 포맷(Parquet, ORC), 데이터 압축, 적절한 파일 크기(128MB~1GB), SELECT 절 최적화, CTAS 활용 등의 전략이 필수적이며, Redshift는 정기 BI·복잡한 쿼리에, Athena는 임시 쿼리·탐색적 분석에 적합하다.
