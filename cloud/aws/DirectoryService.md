# AWS Directory Service란?

## 개념 정의
AWS Directory Service는 AWS에서 제공하는 **완전 관리형 Active Directory(AD) 서비스**다. Microsoft Active Directory를 AWS 클라우드에서 사용하거나, 온프레미스 AD와 AWS 서비스를 통합하여 사용자·그룹·디바이스·권한을 중앙에서 관리할 수 있다. Windows 워크로드, EC2, RDS, WorkSpaces 등 AD 기반 애플리케이션과 AWS 서비스를 원활하게 연동할 수 있도록 설계됐다.

쉽게 말해 "AWS 클라우드에서 Microsoft Active Directory를 쉽게 구축·관리하거나 기존 온프레미스 AD와 연결하는 관리형 디렉터리 플랫폼"이다.

## 핵심 특징

**완전 관리형 인프라**
- AD 인프라 구축·패치·백업·복구·업데이트를 AWS가 자동 관리
- 고가용성을 위해 다중 AZ에 도메인 컨트롤러 자동 배포
- 도메인 컨트롤러 장애 시 자동 교체·복구

**다양한 배포 옵션**
- AWS Managed Microsoft AD: AWS에서 실제 Windows Server 기반 AD 도메인 구축
- AD Connector: 온프레미스 AD를 AWS 서비스에 연결하는 프록시 역할
- Simple AD: 기본적인 AD 기능만 제공하는 경량 Samba 기반 디렉터리

**하이브리드 환경 지원**
- 온프레미스 AD와 신뢰 관계(Trust Relationship) 구성 가능
- 온프레미스와 클라우드 간 통합 디렉터리 경험 제공
- Multi-Region 복제로 글로벌 워크로드 지원

**AWS 서비스 완벽 통합**
- EC2 인스턴스 도메인 가입, RDS SQL Server AD 인증
- Amazon WorkSpaces, AppStream, QuickSight 등 엔터프라이즈 서비스 SSO
- AWS Organizations와 연동해 여러 계정에 디렉터리 공유

**보안 및 규정 준수**
- 저장 시 EBS 암호화, 전송 시 LDAPS(LDAP over SSL/TLS) 지원
- 세밀한 보안 설정(프로토콜, 암호화 방식 제어)
- CloudTrail, CloudWatch 통합 감사·모니터링

## 주요 배포 옵션

**AWS Managed Microsoft AD**
- 실제 Windows Server 2019 기반 Microsoft Active Directory 도메인
- Enterprise(최대 50만 개 객체), Standard(최대 5만 개 객체) 에디션
- 스키마 확장, GPO(그룹 정책), 트러스트, LDAPS, MFA 등 전체 AD 기능 지원
- 온프레미스 AD와 신뢰 관계 구성으로 하이브리드 환경 구현

**AD Connector**
- 기존 온프레미스 AD를 AWS 서비스에 연결하는 프록시(디렉터리 데이터 저장 안 함)
- VPN 또는 Direct Connect로 온프레미스 연결 필요
- AWS 서비스 인증만 필요하고 클라우드에 AD 복제 불필요한 경우 적합
- 비용 효율적(데이터 복제·저장 비용 없음)

**Simple AD**
- Samba 4 기반 경량 디렉터리(Microsoft AD 호환 기능 제공)
- 소규모 환경(최대 5천 개 사용자), 기본 AD 기능만 필요한 경우
- 트러스트, MFA, 스키마 확장 등 고급 기능 미지원
- 가장 저렴한 옵션

## 전통적 AD 운영 vs AWS Directory Service 비교

| 특징 | AWS Directory Service | 온프레미스 AD 운영 |
|------|----------------------|-------------------|
| 인프라 관리 | AWS 자동 관리 | 직접 서버·네트워크 관리 |
| 고가용성 | 다중 AZ 자동 배포 | 수동 DR 구성 필요 |
| 스케일링 | 도메인 컨트롤러 추가 클릭 | 서버 증설·구성 필요 |
| 백업·복구 | 자동 일일 스냅샷 | 수동 백업·복구 |
| AWS 통합 | 네이티브 연동 | 복잡한 구성 필요 |
| 비용 | 사용량 기반 과금 | 하드웨어·라이선스 비용 |

## 주요 기능

**도메인 컨트롤러 관리**
- 최소 2개 도메인 컨트롤러(다중 AZ)로 고가용성 보장
- 추가 도메인 컨트롤러 배포로 성능·가용성 향상
- 자동 장애 복구 및 IP 주소 유지

**신뢰 관계(Trust Relationships)**
- 온프레미스 AD와 양방향/단방향 포레스트 트러스트 구성
- 멀티 포레스트 아키텍처 지원
- 통합 인증·리소스 접근 제어

**스키마 확장**
- LDIF 파일로 커스텀 객체 클래스·속성 추가
- AD 의존 엔터프라이즈 애플리케이션 마이그레이션 지원

**그룹 정책(GPO)**
- 기존 GPO 도구(GPMC)로 사용자·디바이스 관리
- Windows/Linux EC2 인스턴스에 정책 자동 적용

**Multi-Region 복제**
- 단일 디렉터리를 여러 AWS 리전에 자동 복제
- 글로벌 워크로드에 로컬 디렉터리 접근으로 성능 향상
- 리전 장애 시에도 서비스 연속성 보장

**디렉터리 공유**
- AWS Organizations 통합으로 여러 계정에 디렉터리 공유
- 중앙 집중식 사용자·권한 관리
- 계정별 별도 디렉터리 불필요

**보안 설정**
- 레거시 프로토콜·암호화 방식(RC4, DES, SSL 2.0/3.0) 비활성화
- LDAPS(서버 측 암호화) 지원
- 세밀한 암호 정책 및 계정 잠금 설정

## 주요 활용 사례

**클라우드 마이그레이션**
- 온프레미스 AD 의존 애플리케이션을 AWS로 이전
- 신뢰 관계로 기존 AD와 통합 유지
- 단계적 마이그레이션 전략 지원

**Windows 워크로드 관리**
- EC2 Windows 인스턴스 도메인 가입 자동화
- RDS SQL Server AD 인증 통합
- FSx for Windows File Server와 연동

**엔드 유저 컴퓨팅(EUC)**
- Amazon WorkSpaces 사용자 관리
- AppStream 2.0 애플리케이션 스트리밍
- 통합 SSO 및 MFA

**하이브리드 ID 관리**
- 온프레미스·클라우드 통합 디렉터리 환경
- AWS IAM Identity Center(구 AWS SSO)와 연동
- 페더레이션 기반 AWS 콘솔 접근

## 기본 워크플로

### 1. AWS Managed Microsoft AD 생성
```bash
# AWS 콘솔/CLI로 디렉터리 생성
# 에디션(Standard/Enterprise), VPC, 서브넷 선택
# 자동으로 2개 도메인 컨트롤러 배포
```

### 2. EC2 Windows 인스턴스 도메인 가입
- 디렉터리 DNS IP를 EC2 네트워크 설정에 추가
- PowerShell 또는 GUI로 도메인 가입
- GPO 자동 적용

### 3. 온프레미스 AD와 신뢰 관계 구성
- 조건부 전달자(Conditional Forwarder) 설정
- 양방향 포레스트 트러스트 생성
- 통합 인증 테스트

### 4. AWS 서비스 통합
- RDS SQL Server 인스턴스에 디렉터리 연결
- WorkSpaces 디렉터리 등록
- IAM Identity Center 연동

## 비용 구조

**AWS Managed Microsoft AD**
- Standard: 시간당 약 $0.05(월 $36)
- Enterprise: 시간당 약 $0.146(월 $106)
- 추가 도메인 컨트롤러: 개당 시간당 요금 추가

**AD Connector**
- Small: 시간당 약 $0.05(월 $36)
- Large: 시간당 약 $0.146(월 $106)

**Simple AD**
- Small: 시간당 약 $0.05(월 $36)
- Large: 시간당 약 $0.146(월 $106)

**데이터 전송**
- AWS 리전 간 복제 시 데이터 전송 비용 별도

## 보안 모범 사례

**네트워크 보안**
- VPC 프라이빗 서브넷에 디렉터리 배포
- 보안 그룹으로 필요한 포트만 허용(LDAP, Kerberos, DNS 등)
- VPN/Direct Connect로 온프레미스 안전하게 연결

**접근 제어**
- IAM 정책으로 디렉터리 관리 권한 최소화
- 관리자 계정 MFA 필수 활성화
- 세밀한 위임 권한으로 도메인 관리자 권한 분산

**감사 및 모니터링**
- CloudTrail로 모든 API 호출 기록
- CloudWatch로 도메인 컨트롤러 성능·상태 모니터링
- 로그 스트림 SIEM 통합

**데이터 보호**
- 자동 일일 스냅샷 활성화(35일 보관)
- EBS 볼륨 암호화 기본 활성화
- LDAPS로 전송 중 데이터 암호화

AWS Directory Service는 기업의 Active Directory 워크로드를 클라우드로 현대화하고, 하이브리드 환경에서 통합 ID 관리를 실현하며, 운영 부담을 대폭 줄이는 필수 관리형 디렉터리 서비스다.
