# AWS SCT란?

## 개념 정의
AWS SCT(Schema Conversion Tool)는 온프레미스나 클라우드의 데이터베이스 스키마와 코드 객체(테이블, 뷰, 저장 프로시저, 함수 등)를 AWS 대상 데이터베이스 형식으로 자동 변환하는 무료 마이그레이션 도구로, 이기종 데이터베이스 마이그레이션 시 수동 변환 작업을 최소화하고 마이그레이션 타당성을 평가한다.

쉽게 말해 "Oracle에서 PostgreSQL로, SQL Server에서 Aurora로 등 다른 종류의 DB로 옮길 때, 테이블·프로시저·함수 등을 자동으로 대상 DB 문법에 맞게 변환해주는 무료 데스크톱 도구"다.

## 핵심 기능

### 자동 스키마 변환
소스 데이터베이스의 테이블, 뷰, 저장 프로시저, 함수, 트리거, 데이터 타입, 제약조건 등을 분석해 대상 데이터베이스 형식으로 자동 변환하며, 변환된 코드를 검토·수정할 수 있다.

### 마이그레이션 평가 보고서
마이그레이션 복잡도를 분석하고, 자동 변환 가능한 항목과 수동 변환 필요 항목을 분류하며, 예상 작업량·위험도·권장 사항을 포함한 평가 보고서를 생성한다.

### Extension Pack
소스 DB의 특정 기능(시퀀스, 패키지 등)이 대상 DB에 없을 때, 이를 에뮬레이션하는 추가 스키마와 코드를 대상 DB에 생성해 호환성을 보장한다.

### 데이터 변환 규칙
데이터 타입 변경, 객체 이름 변경, 스키마 간 객체 이동 등 커스텀 변환 규칙을 정의해 마이그레이션을 세밀하게 제어할 수 있다.

## 지원 데이터베이스

### 소스 데이터베이스
- **상용**: Oracle, Microsoft SQL Server, IBM Db2, Sybase ASE, SAP ASE
- **오픈소스**: MySQL, PostgreSQL, MariaDB
- **데이터 웨어하우스**: Teradata, Greenplum, Netezza, Vertica, Azure Synapse Analytics

### 대상 데이터베이스
- **Amazon RDS**: MySQL, PostgreSQL, MariaDB, Oracle, SQL Server
- **Amazon Aurora**: MySQL-Compatible, PostgreSQL-Compatible
- **Amazon Redshift**: 데이터 웨어하우스
- **Amazon S3**: Parquet 형식 데이터 레이크

## 작동 방식

### 1. 소스 DB 연결
SCT를 설치한 로컬 PC에서 소스 데이터베이스(Oracle, SQL Server 등)에 연결하고 스키마를 분석한다.

### 2. 평가 보고서 생성
마이그레이션 복잡도를 분석해 자동 변환 가능 비율, 수동 작업 필요 항목, 예상 공수를 포함한 보고서를 출력한다.

**예시**: Oracle → PostgreSQL 마이그레이션 시 "테이블 90% 자동 변환, 저장 프로시저 60% 자동 변환, 나머지는 수동 변환 필요" 같은 정보 제공

### 3. 스키마 변환
자동으로 DDL·저장 프로시저·함수를 대상 DB 문법으로 변환하며, 변환 불가 객체는 수동 변환 대상으로 표시한다.

### 4. Extension Pack 생성
소스 DB 고유 기능(Oracle의 시퀀스, 패키지 등)을 대상 DB에서 구현하기 위한 보조 스키마·함수를 생성한다.

### 5. 대상 DB 적용
변환된 스키마와 Extension Pack을 대상 데이터베이스(RDS PostgreSQL, Aurora 등)에 적용한다.

### 6. AWS DMS와 통합
SCT로 스키마를 변환한 후, AWS DMS(Database Migration Service)로 실제 데이터를 복제·이관한다.

## 주요 활용 사례

### 이기종 데이터베이스 마이그레이션
Oracle → PostgreSQL, SQL Server → Aurora MySQL, IBM Db2 → PostgreSQL 등 서로 다른 DB 엔진 간 마이그레이션 시 스키마 변환을 자동화한다.

### 데이터 웨어하우스 현대화
Teradata, Netezza, Greenplum 등 온프레미스 DW를 Amazon Redshift로 마이그레이션할 때 스키마·쿼리를 변환한다.

### 라이선스 비용 절감
상용 DB(Oracle, SQL Server)에서 오픈소스 DB(PostgreSQL, MySQL)로 전환해 연간 수억 원의 라이선스 비용을 절감한다.

### 오프라인 평가
소스 DB에서 메타데이터를 추출해 로컬에서 오프라인으로 평가 보고서를 생성하고, 네트워크 연결 없이 변환 작업을 수행한다.

## SCT vs AWS DMS

| 항목 | AWS SCT | AWS DMS |
|------|---------|---------|
| 역할 | 스키마·코드 변환 | 실제 데이터 복제·이관 |
| 변환 대상 | 테이블, 뷰, 프로시저, 함수 | 테이블 데이터(Row) |
| 설치 위치 | 로컬 PC (Windows, macOS, Linux) | AWS 클라우드 서비스 |
| 비용 | 무료 | 복제 인스턴스 시간당 과금 |
| 사용 시점 | 마이그레이션 전 (스키마 준비) | 마이그레이션 중 (데이터 이관) |
| 통합 | DMS와 함께 사용 권장 | SCT 변환 스키마에 데이터 적재 |

**일반적인 워크플로우**
1. AWS SCT로 스키마·코드 변환 → 대상 DB에 적용
2. AWS DMS로 소스 DB 데이터를 대상 DB로 복제

## 주의 사항

### 수동 변환 필요
모든 객체가 100% 자동 변환되지는 않으며, 복잡한 저장 프로시저·트리거·커스텀 함수는 수동 수정이 필요하다.

### 네트워크 접근성
SCT는 로컬 PC에서 소스·대상 DB 모두에 네트워크 접근이 가능해야 하므로, 방화벽·보안 그룹 설정이 필요하다.

### 평가 보고서 활용
마이그레이션 전 반드시 평가 보고서를 검토해 작업량·위험도를 파악하고 계획을 수립해야 한다.

## 실제 사례

### IBM Db2 → PostgreSQL 마이그레이션
- 5개 스키마, 60개 이상 테이블을 수 분 내 변환
- 90% 자동 변환률로 수동 작업 최소화
- 80% 마이그레이션 리스크 감소

### Oracle → Aurora PostgreSQL
- 수십 TB급 데이터베이스 마이그레이션
- SCT로 스키마 변환, DMS로 데이터 복제
- 연간 수억 원 Oracle 라이선스 비용 절감

## 요약
AWS SCT(Schema Conversion Tool)는 Oracle·SQL Server·Db2·Teradata 등 소스 데이터베이스의 스키마·저장 프로시저·함수를 PostgreSQL·MySQL·Aurora·Redshift 등 대상 데이터베이스 형식으로 자동 변환하는 무료 데스크톱 도구로, 마이그레이션 평가 보고서 생성, Extension Pack으로 소스 DB 고유 기능 에뮬레이션, 변환 규칙 커스터마이징을 지원하며, AWS DMS와 통합해 스키마 변환(SCT) → 데이터 복제(DMS) 순서로 이기종 데이터베이스 마이그레이션을 수행하고, 이를 통해 라이선스 비용 절감과 클라우드 DB 현대화를 달성한다.
