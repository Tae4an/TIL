# AWS FSx란?

## 개념 정의

AWS FSx는 서드파티 고성능 파일 시스템을 AWS에서 완전관리형으로 제공하는 서비스다. Windows File Server, Lustre, OpenZFS, NetApp ONTAP 4가지 파일 시스템을 지원하며, 온프레미스 파일 서버를 AWS로 마이그레이션하거나 고성능 워크로드를 실행할 때 사용한다.

쉽게 말해 "유명한 파일 시스템을 AWS가 알아서 관리해주는 서비스로, Windows 환경이나 HPC/ML 같은 고성능 작업에 최적화된 공유 스토리지"다.

## 핵심 기능

- 4가지 파일 시스템 지원 (Windows, Lustre, OpenZFS, ONTAP)
- 완전관리형 (백업, 패치, 장애 복구 자동)
- Multi-AZ 고가용성
- 온프레미스 Active Directory 통합
- S3와 자동 연동 (Lustre)
- 데이터 압축 및 중복 제거

## FSx 종류별 특징

### FSx for Windows File Server

**특징**
- SMB 프로토콜 및 Windows NTFS 지원
- Active Directory 통합
- DFS(분산 파일 시스템) 지원
- VSS(볼륨 섀도 복사) 백업
- 사용자/폴더 단위 할당량 및 ACL
- Multi-AZ 배포로 자동 Failover

**적합 사례**
- Windows 기반 애플리케이션
- 기업용 홈 디렉터리
- 콘텐츠 관리 시스템
- 미디어 워크플로우

**성능**
- SSD/HDD 스토리지 옵션
- 처리량: 64 MiB/s ~ 2 GiB/s per TiB
- Sub-millisecond 지연시간

### FSx for Lustre

**특징**
- 병렬 파일 시스템 (수천 클라이언트 동시 접근)
- S3 버킷과 자동 동기화
- 수백 GB/s 처리량, 밀리초 미만 지연
- POSIX 호환
- 스크래치/영구 배포 타입

**적합 사례**
- 머신러닝 학습 (S3 데이터셋 빠른 로드)
- HPC (금융 모델링, 시뮬레이션, 유전체 분석)
- 빅데이터 분석 (페타바이트급 처리)
- 미디어 렌더링 및 트랜스코딩

**성능**
- 처리량: 수백 GB/s
- IOPS: 수백만
- S3 데이터 자동 로드/언로드

### FSx for OpenZFS

**특징**
- Linux 기반 ZFS 파일 시스템
- NFS 프로토콜 지원
- 스냅샷, 클론, 압축 기본 제공
- 최대 100만 IOPS

**적합 사례**
- Linux/Unix 애플리케이션
- 데이터베이스 워크로드
- 개발/테스트 환경 (빠른 클론)
- 미디어 및 엔터테인먼트

**성능**
- 최대 12.5 GB/s 처리량
- 최대 100만 IOPS
- 마이크로초 지연시간

### FSx for NetApp ONTAP

**특징**
- NetApp ONTAP 스토리지 OS
- NFS, SMB, iSCSI 멀티 프로토콜
- 스냅샷, 클론, 복제, 티어링
- Multi-AZ 배포

**적합 사례**
- 하이브리드 클라우드 (온프레미스 ONTAP과 통합)
- 엔터프라이즈 애플리케이션 (SAP, Oracle)
- 멀티 프로토콜 환경
- 데이터 티어링 (S3로 콜드 데이터 이동)

**성능**
- 최대 36 GB/s 처리량
- 최대 80만 IOPS
- 서브 밀리초 지연시간

## 주요 구성 요소

**파일 시스템**
- 스토리지 용량, IOPS, 처리량 지정
- 단일 AZ 또는 Multi-AZ 선택
- VPC 서브넷에 배치

**백업**
- 자동 일일 백업 또는 사용자 정의 백업
- AWS Backup 통합
- 증분 백업으로 비용 절감

**데이터 복제**
- AZ 간 자동 복제 (Multi-AZ)
- 내구성 99.999999999% (11 9's)

**네트워크**
- VPC 내 ENI로 접근
- 온프레미스와 Direct Connect/VPN 연결
- Security Group으로 접근 제어

## 작동 방식

1. **파일 시스템 생성**: 타입, 용량, 처리량, AZ 선택
2. **네트워크 구성**: VPC, 서브넷, Security Group 설정
3. **마운트**: EC2/ECS/EKS에서 SMB/NFS로 마운트
4. **데이터 접근**: 여러 인스턴스에서 동시 읽기/쓰기
5. **자동 백업**: 설정한 주기로 백업 생성
6. **모니터링**: CloudWatch로 성능, 용량 추적

## FSx vs EFS vs EBS

| 항목 | FSx | EFS | EBS |
|------|-----|-----|-----|
| 유형 | 완전관리형 파일 시스템 | NFS 파일 시스템 | 블록 스토리지 |
| 프로토콜 | SMB, NFS, iSCSI | NFS | 블록 레벨 |
| 성능 | 최대 수백 GB/s | 최대 10 GB/s | 최대 16 GB/s |
| 동시 접근 | 수천 클라이언트 | 수천 인스턴스 | 단일 인스턴스 |
| 사용 사례 | Windows, HPC, ML | Linux 공유 스토리지 | EC2 부팅 볼륨 |
| 가격 | 높음 (고성능) | 중간 | 낮음 |

## 주요 활용 사례

**Windows 애플리케이션 마이그레이션**
- 온프레미스 Windows 파일 서버를 FSx for Windows로 이동
- Active Directory 통합으로 기존 권한 유지
- SMB 프로토콜로 애플리케이션 코드 수정 없음

**머신러닝 학습 가속**
- FSx for Lustre로 S3 데이터를 빠르게 로드
- 수백 GPU 인스턴스에서 동시 데이터 접근
- 학습 시간 단축 (밀리초 지연)

**HPC 워크로드**
- 유전체 분석, 날씨 시뮬레이션, CFD
- 병렬 파일 시스템으로 수천 코어 동시 처리
- 페타바이트급 데이터 처리

**미디어 편집 및 렌더링**
- 여러 편집자가 동시에 4K/8K 영상 작업
- 높은 처리량으로 실시간 렌더링
- VSS 스냅샷으로 버전 관리

**하이브리드 클라우드**
- FSx for ONTAP으로 온프레미스와 데이터 동기화
- Direct Connect로 저지연 연결
- 콜드 데이터를 S3로 자동 티어링

## 통합 서비스

- Amazon EC2, ECS, EKS: 컴퓨팅 리소스 연결
- Amazon S3: 데이터 소스 및 백업 (Lustre, ONTAP)
- AWS Backup: 중앙 백업 관리
- AWS Direct Connect/VPN: 온프레미스 연결
- AWS DataSync: 데이터 마이그레이션
- AWS CloudWatch: 모니터링 및 알림
- AWS IAM: 접근 제어
- Active Directory: 사용자 인증 (Windows)

## 모범 사례

**적절한 파일 시스템 선택**
- Windows 앱 → FSx for Windows
- HPC/ML → FSx for Lustre
- Linux 앱 → FSx for OpenZFS
- 하이브리드/멀티 프로토콜 → FSx for ONTAP

**Multi-AZ 배포**
- 프로덕션 워크로드는 Multi-AZ로 고가용성 확보
- 자동 Failover로 중단 시간 최소화

**처리량 및 IOPS 최적화**
- 워크로드 패턴에 맞춰 용량 및 처리량 설정
- CloudWatch로 성능 모니터링 후 조정

**백업 전략**
- 자동 백업 활성화 (보존 기간 설정)
- 중요 데이터는 AWS Backup으로 중앙 관리
- 온프레미스로 백업 복제 고려

**보안 강화**
- VPC 내 Private Subnet 배치
- Security Group으로 포트 제한 (SMB 445, NFS 2049)
- 전송 중/저장 중 암호화 활성화
- IAM으로 관리 작업 제어

**비용 최적화**
- HDD 스토리지 옵션 검토 (비용 민감 워크로드)
- 데이터 압축 및 중복 제거 활성화
- S3 티어링으로 콜드 데이터 이동 (ONTAP, Lustre)

## 제약 사항

**FSx for Windows**
- Windows 기반 애플리케이션에만 최적화
- Linux에서 SMB 사용 시 성능 제한

**FSx for Lustre**
- POSIX 권한만 지원 (ACL 제한)
- 스크래치 타입은 영구 스토리지 아님

**FSx for OpenZFS**
- Windows 네이티브 지원 없음

**FSx for ONTAP**
- 비용이 가장 높음

## 비용

- 스토리지 용량 GB당 과금
- 처리량 및 IOPS 프로비저닝 비용
- 백업 스토리지 별도 과금
- 데이터 전송 비용
- 타입별 가격 차이 (Lustre > Windows > OpenZFS)

## 요약

AWS FSx는 Windows File Server, Lustre, OpenZFS, NetApp ONTAP 4가지 서드파티 파일 시스템을 완전관리형으로 제공하며, SMB/NFS/iSCSI 멀티 프로토콜을 지원하고, Multi-AZ 배포로 고가용성을 보장하며, S3 연동 및 Active Directory 통합이 가능하고, Windows 앱 마이그레이션, HPC, ML, 미디어 워크로드에 최적화되어 있으며, EFS보다 높은 성능과 다양한 프로토콜을 제공하지만 비용이 높다.
