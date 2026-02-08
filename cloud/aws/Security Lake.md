# Amazon Security Lake란?

## 개념 정의

Amazon Security Lake는 AWS 환경, SaaS, 온프레미스, 서드파티 등 여러 소스에서 발생하는 **보안 로그·이벤트 데이터를 자동으로 수집**하고, 계정의 **전용 S3 기반 데이터 레이크**에 **표준 형식(OCSF, Apache Parquet)**으로 중앙 집중화해 주는 완전관리형 보안 데이터 레이크 서비스다.

쉽게 말해, "CloudTrail, VPC Flow Logs, Security Hub, WAF 로그 같은 온갖 보안 로그를 한 곳에 모으고, 통일된 스키마로 바꿔서 SIEM·분석 도구가 쓰기 쉽게 만들어주는 보안 데이터 중앙 저장소"다.

## 핵심 기능

- **자동 수집·통합**  
  CloudTrail, VPC Flow Logs, Route 53 Resolver, Security Hub, WAF v2, EKS 감사 로그 등 AWS 네이티브 소스와, 서드파티·커스텀 소스의 보안 데이터를 자동으로 수집한다.

- **OCSF(Open Cybersecurity Schema Framework) 표준화**  
  서로 다른 포맷의 로그를 OCSF라는 오픈 표준 스키마로 자동 변환해, 소스가 달라도 일관된 구조로 분석·검색할 수 있게 한다.

- **Apache Parquet 저장**  
  고압축·컬럼 기반 Parquet 포맷으로 저장하여 스토리지 비용을 줄이고, Athena·Glue·Redshift 같은 분석 도구에서 빠르게 쿼리할 수 있다.

- **멀티 계정·멀티 리전 관리**  
  Organizations의 여러 계정과 리전에서 로그를 중앙 Security Lake 계정으로 모아 전체 조직의 보안 데이터를 통합 관리한다.

- **구독자(Subscriber) 기반 데이터 공유**  
  SIEM(Splunk, QRadar 등), 보안 분석 도구, 내부 보안팀, AWS 서비스(Amazon Security Lake와 연계하는 서드파티)에게 데이터 접근 권한을 부여하고, 새 데이터 추가 시 SQS로 자동 알림한다.

- **데이터 수명주기 관리**  
  보존 기간, 복제 설정을 커스터마이징해 비용 최적화와 규정 준수 요구사항을 충족한다.

## 지원 AWS 소스

- AWS CloudTrail (관리·데이터 이벤트)
- Amazon VPC Flow Logs
- AWS Security Hub Findings
- Route 53 Resolver 쿼리 로그
- AWS WAF v2 로그
- Amazon EKS 감사 로그
- 커스텀 소스(OCSF·Parquet 포맷으로 직접 작성)

## OCSF(Open Cybersecurity Schema Framework)란?

AWS와 사이버보안 파트너들이 공동 개발한 **오픈소스 표준 보안 이벤트 스키마**로, CloudTrail의 IAM 로그, VPC Flow Logs의 네트워크 로그, 서드파티 EDR 로그처럼 서로 다른 형식의 로그를 **공통 구조**(이벤트 클래스, 타임스탬프, 소스, 대상, 액션 등)로 변환해 준다. 덕분에 SIEM·분석 도구가 소스별 커스텀 파싱 없이 일관된 쿼리와 규칙으로 분석할 수 있다.

## 주요 활용 사례

- **위협 탐지·조사**  
  여러 소스의 보안 로그를 OCSF 스키마로 통합 분석해, 의심 활동·공격 패턴을 실시간 탐지하고, 과거 인시던트의 근본 원인을 조사한다.

- **인시던트 대응**  
  중앙화된 데이터 레이크에서 관련 로그를 빠르게 검색·상관 분석해, 인시던트 타임라인 재구성 및 신속한 대응·복구를 수행한다.

- **규정 준수·감사**  
  PCI DSS, HIPAA, SOC 2 같은 표준 요구사항에 맞춰 보안 로그를 장기 보관하고, 표준화된 형식으로 감사 리포트를 생성한다.

- **SIEM·SOC 통합**  
  Splunk, QRadar, Elastic 같은 SIEM에 Security Lake 데이터를 구독시켜, 외부 보안팀이나 SOC가 AWS 로그를 통합 분석하고, 알람·대시보드를 구성한다.

- **보안 분석·위협 헌팅**  
  Athena, QuickSight, Redshift로 Security Lake 데이터를 쿼리해, 이상 패턴·트렌드 분석, 보안 지표 시각화, 능동적 위협 헌팅을 수행한다.

- **멀티 클라우드·하이브리드 환경 통합**  
  AWS 외에도 Azure, GCP, 온프레미스 방화벽·IDS/IPS 로그를 커스텀 소스로 Security Lake에 넣어, 전사 보안 데이터를 단일 레이크에서 관리한다.
