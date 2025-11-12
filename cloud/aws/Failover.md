# AWS Failover란?

## 개념 정의
AWS에서 Failover(장애 조치)는 **서비스나 리소스에 장애나 오류가 발생했을 때, 자동으로 예비(Secondary) 시스템이나 리전, 리소스로 트래픽을 전환하여 서비스 가용성과 연속성을 유지**하는 설계/운영 방식이다.

쉽게 말하면 “주(Primary) 시스템 장애 시 즉시 예비 시스템으로 자동 전환해 무중단 서비스 운영을 보장하는 AWS 핵심 고가용성 아키텍처”다.

## 핵심 특징

**자동 장애 감지 및 전환(자동화)**
- 헬스체크(Health Check) 기반으로 장애 발생 시 자동 탐지
- 트래픽, DNS, 데이터 등 미리 지정한 예비 리소스 또는 리전으로 자동 전환

**다양한 인프라 단위에서 적용**
- EC2, RDS, Elastic Load Balancer, Route 53, S3 등 대부분의 AWS 서비스에 Failover 설계 가능
- 단일 인스턴스, Multi-AZ, Multi-Region, 백엔드 서비스 등 모든 계층에 적용 가능

**고가용성(HA)·무중단 서비스 구현**
- 장애·점검·트래픽 폭증 시에도 비즈니스 연속성 보장
- 복구 시간(RTO) 최소화, 데이터 손실(RPO) 최소화

## 주요 구성 방법

**1. Route 53 DNS Failover**
- 도메인의 주요 IP, ELB, S3 등의 상태 감시(헬스체크) 설정
- 장애 발생 시 즉시 DNS 레코드를 예비 리소스(다른 리전/서버)로 자동 전환
- 가장 단순·즉각적인 글로벌 영향 범위 Failover

**2. ELB(Elastic Load Balancing) 활용**
- 여러 AZ(가용 영역) 혹은 EC2 인스턴스를 뒤에 두고, 장애·성능 저하 감지 시 자동 트래픽 분산·전환
- 자체적으로 인스턴스 Health Check 수행 후, 정상 인스턴스로만 트래픽 라우팅

**3. RDS Multi-AZ Failover**
- RDS 데이터베이스를 두 개의 AZ에 복제 배포(Primary-Standby)
- Primary에 장애 발생 시 즉시 Standby로 자동 장애 조치·DNS 엔드포인트 변경

**4. S3 Cross-Region Replication 및 Read Replica**
- S3, DynamoDB 등에서 멀티 리전 복제 설정으로, 장애 시 다른 리전 리소스에서 서비스 지속

**5. Application Level & DR(Disaster Recovery)**
- AWS 백엔드/FrontEnd 모두 장애 조치·백업·복구 아키텍처 설계
- Pilot Light, Warm Standby, Multi-Site Active/Active 등 다양한 DR 전략과 결합

## 주요 활용 사례

- 글로벌 웹/앱 서비스에서 리전 간 DNS Failover로 무중단 서비스
- 금융/공공/의료 등 핵심 DB 멀티 AZ 장애 조치, RPO/RTO 극소화 설계
- S3 데이터 정합성·내구성 강화 위해 크로스 리전 복제 + Failover
- Multi-AZ/Region 인프라로 트래픽 폭증 시 자동 확장/장애 대처

## 기본 워크플로 예시

### 1. Route 53 DNS Failover 설정 예시
- 헬스체크 생성(서버/ELB/엔드포인트)
- Failover Policy 적용(A Record/CNAME, Primary/Secondary)
- 장애 발생 시 자동 DNS 레코드 전환

### 2. RDS Multi-AZ 구성
- 신규 DB 생성 시 Multi-AZ 옵션 활성화
- 장애 발생 시 DNS/엔드포인트 자동 전환

### 3. ELB로 AZ/인스턴스 장애 조치
- 여러 AZ에 EC2 배치, 헬스체크 활성화
- 인스턴스·AZ 장애 시 정상 노드만 트래픽 분산

## 비용 구조

- Multi-AZ, 멀티 리전, 복제 등 예비 리소스 인프라도 요금 발생(두 배 이상 계산 필요)
- DNS 헬스체크, ELB, RDS 등 Failover에 연관된 리소스별 비용
- S3, DynamoDB 등 크로스 리전 복제, DR 등 추가 복제·저장·트래픽 비용반영

## 보안 및 운영 모범 사례

- 헬스체크 신뢰도·감시 주기 신중 설정(오탐/오작동 최소화)
- 리전/AZ 단위 장애 시 Data Consistency 확보(복제, Sync, Snapshot)
- Failover 설계 후 실제 장애/복구 시나리오 테스트 필수
- 인프라-as-코드(IaC) 및 자동화 툴로 장애 조치·복구 프로세스 표준화
- 비용/성능/보안 균형 잡힌 DR/Fault-Tolerance 전략 수립

AWS Failover는 고가용성과 안정성을 확보하기 위한 핵심 클라우드 인프라 설계 원칙이자, 비즈니스 연속성과 무중단 서비스의 중심이 되는 필수적인 아키텍처 설계 방법이다.
