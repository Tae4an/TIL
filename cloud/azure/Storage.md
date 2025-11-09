# Azure Storage란?

## 개념 정의
Azure Storage는 Microsoft Azure에서 제공하는 **확장성, 내구성, 보안성이 뛰어난 클라우드 데이터 저장소 서비스의 통합 플랫폼**이다. 다양한 유형의 데이터를 저장·관리할 수 있는 여러 스토리지 유형(Blob, File, Queue, Table, Disk 등)을 하나의 서비스로 제공한다.

쉽게 말하면 “모든 용도에 맞게 파일·객체·메시지·NoSQL·디스크까지 저장하고 관리하게 해주는 Azure의 대표 스토리지 허브”다.

## 핵심 특징

**다양한 저장 유형 지원**
- Blob Storage: 비정형 데이터(이미지, 동영상, 백업, IoT 등) 저장에 최적화된 객체(Blob) 스토리지
- File Storage: 파일 서버/네트워크 공유와 유사하게 SMB 프로토콜로 파일 접근 가능
- Queue Storage: 비동기 메시지 큐잉 기능으로 서버/마이크로서비스 간 통신 최적화
- Table Storage: 단순/유연한 NoSQL 키-값 테이블 데이터 저장, 빠른 질의 응답
- Disk Storage: VM용 고성능 관리형 디스크, OS/데이터 볼륨 등 인프라 블록 스토리지

**무한 확장성과 고내구성**
- 데이터 크기에 제한 없는 페타바이트 이상 확장 가능
- Azure 내부 3중 또는 지역 간 복제로 99.999999999% 내구성 제공

**보안 및 규정 준수**
- 저장·전송 시 암호화(자동, 사용자 관리 키 BYOK 지원)
- 네트워크 제어(N-Wall, VNet 통합, 프라이빗 엔드포인트)
- RBAC, SAS(공유 엑세스 서명) 등 접근 및 권한 제어

**비용 효율성 및 계층화**
- 사용빈도별 스토리지 계층(핫/쿨/아카이브) 선택으로 저장비 최적화
- 라이프사이클 관리, 자동 전환 정책 지원

**글로벌 서비스 연동성**
- 모든 Azure 리전에서 동일 기능 제공(데이터 레이크, CDN, 정적 웹 호스팅 등과 통합)
- SDK, REST API, CLI, 포털에서 일관된 개발·운영 경험

## 주요 구성 요소

- **Storage Account**: 스토리지 리소스의 최상위 컨테이너, Blob/File/Queue/Table/Disk 관리의 기반 단위
- **Container(Blob)/File Share/Queue/Table**: 유형별 저장 공간의 논리적 단위
- **Blob**: 비정형 객체/파일 단일 단위(블록, 페이지, Append Blob)
- **Disk**: VM용 관리형(Managed) 디스크 볼륨
- **SAS/Access Key**: 데이터 접근을 위한 보안 인증 수단

## 기존 온프레미스 스토리지 vs Azure Storage 비교

|         | 온프레미스          | Azure Storage           |
|---------|---------------------|------------------------|
| 확장성  | 하드웨어 한계, 증설 | 무한 확장, 필요시 즉시 |
| 내구성  | 백업/복구 필요      | 99.999999999%내구성    |
| 보안    | 직접 관리           | 자동 암호화, 정책제어  |
| 접근성  | 네트워크 필요       | 전세계 어디서나 접근   |
| 관리    | 직접/복합           | 자가 치유, 자동화      |
| 비용    | 초기 투자/운영비    | 사용량, 계층별 요금제  |

## 주요 기능

- Blob: 대용량 파일, 백업, 로그, 동영상 등의 저장·호스팅·분석·공유
- File: 온프레미스/클라우드 파일 서버/NAS 대체, SMB 프로토콜 지원
- Queue: 분산 서비스 간 메시징/비동기 처리
- Table: 대량의 NoSQL 비정형 데이터, 비즈니스 로그/이벤트 저장 등
- Disk: VM OS/Image/Data 볼륨(프리미엄/표준, SSD/HDD 선택 가능)
- 정적 웹 호스팅: Blob 기반으로 정적 웹사이트 서비스 가능
- 스토리지 계층 전환: 핫(자주), 쿨(가끔), 아카이브(드물게) 계층 자동 이동

## 주요 활용 사례

- 웹, 앱, IoT 데이터 저장 및 백업
- 스트리밍, CDN, 정적 파일 호스팅
- 기업 파일 서버, NAS 대체, 협업 폴더
- 분산 마이크로서비스 메시징
- 로그/이벤트/실시간 분석 데이터 저장
- VM OS/데이터 볼륨·이미지 관리, 스냅샷/복제

## 기본 워크플로

### 1. 스토리지 계정 생성
```bash
az storage account create --name mystorage --resource-group mygroup --location koreacentral
```

### 2. Blob 컨테이너/File Share 등 생성
```bash
# Blob
az storage container create --account-name mystorage --name mycontainer

# File Share
az storage share create --account-name mystorage --name myshare
```

### 3. 데이터 업로드/다운로드
```bash
az storage blob upload --account-name mystorage --container-name mycontainer --file ./logo.png --name logo.png
```

### 4. 보안·권한 관리
- RBAC/Access Key/SAS/네트워크 제한 설정
- 암호화·로그 감시·정책 필요 시 활성화

## 비용 구조

- 저장 용량(GB, TB…) + 계층(핫/쿨/아카이브)별 요금
- 읽기/쓰기 트랜잭션, 데이터 전송, 복제(Geo)별 추가 요금
- Disk, File 등은 IOPS/성능별로 별도 과금

## 보안 모범 사례

- 최소한의 접근 권한 원칙 준수(RBAC, Key/SAS 관리)
- 데이터 전송 및 저장 모두 암호화 활성화
- 네트워크 제한, VNet, Private Endpoint 적극 사용
- 라이프사이클 관리 및 불필요 데이터 자동 삭제 정책 활용
- Access Key/SAS 키 주기적 교체 및 모니터링

Azure Storage는 현대 클라우드 앱/서비스의 데이터 기반을 책임지는 필수 서비스로, 확장성·내구성·보안을 동시에 만족시키는 클라우드 인프라의 중심이다.
