# AWS Transfer Family란?

## 개념 정의
AWS Transfer Family는 SFTP, FTPS, FTP, AS2 프로토콜을 사용해 Amazon S3 또는 Amazon EFS로 파일을 안전하게 전송할 수 있는 완전 관리형 파일 전송 서비스다. 기존의 파일 전송 기반 워크플로를 AWS로 마이그레이션할 때, 최종 사용자의 클라이언트·스크립트·설정을 그대로 유지하면서 서버 인프라 관리 부담을 제거한다.

쉽게 말해 "기업의 SFTP/FTP 서버를 AWS가 완전히 관리해주고, 파일은 S3나 EFS에 저장되는 서버리스 파일 전송 서비스"다.

## 핵심 특징

**완전 관리형 서버**
- SFTP/FTPS/FTP 서버 인프라 프로비저닝·패치·관리 불필요
- 고가용성 자동 보장 (최대 3개 가용 영역)
- 자동 스케일링으로 수천 명 동시 접속 지원
- 서버 소프트웨어 업데이트 자동 적용

**다중 프로토콜 지원**
- SFTP (Secure File Transfer Protocol): SSH 기반 보안 전송
- FTPS (File Transfer Protocol Secure): SSL/TLS 암호화 FTP
- FTP (File Transfer Protocol): 표준 FTP
- AS2 (Applicability Statement 2): B2B 문서 교환 표준
- 웹 브라우저 기반 전송 (Managed File Transfer Portal)

**AWS 스토리지 통합**
- Amazon S3: 객체 스토리지로 파일 저장
- Amazon EFS: 파일 시스템으로 파일 저장
- 사용자는 기존 SFTP 클라이언트로 접속하지만 실제 데이터는 S3/EFS에 저장

**기존 워크플로 유지**
- 사용자 자격 증명·스크립트·설정 변경 불필요
- 기존 SFTP 클라이언트(FileZilla, WinSCP, Cyberduck) 그대로 사용
- 호스트명만 Transfer Family 엔드포인트로 변경

**다양한 인증 방식**
- SSH 키 기반 인증 (SFTP)
- 사용자명/비밀번호 인증
- AWS Managed Microsoft AD (Active Directory)
- 커스텀 인증 (Lambda, API Gateway)
- Okta, Ping Identity 같은 외부 IdP 통합 가능

**세밀한 접근 제어**
- IAM 정책으로 사용자별 S3/EFS 접근 권한 제어
- 논리적 홈 디렉토리 (사용자마다 다른 경로)
- 세션 정책으로 런타임 권한 제어
- IP 화이트리스트

**보안**
- 전송 중 암호화 (SSH, TLS)
- 저장 시 암호화 (S3 SSE, EFS 암호화)
- VPC 엔드포인트로 프라이빗 전송 (인터넷 우회)
- CloudTrail로 모든 활동 로깅

**관리형 워크플로 (MFTW)**
- 파일 업로드 후 자동 처리
- 스캔, 압축, 암호화, 태깅, 이동 등
- 서버리스 자동화

## 지원 프로토콜

### 1. SFTP (Secure File Transfer Protocol)

**개념**
- SSH 기반 보안 파일 전송
- 기본 포트: 22
- 가장 많이 사용되는 프로토콜

**인증**
- SSH 키 페어 (공개 키/개인 키)
- 사용자명/비밀번호 (커스텀 IdP)

**특징**
- 전송 중 암호화
- 파일 목록, 업로드, 다운로드, 삭제, 이름 변경

### 2. FTPS (File Transfer Protocol Secure)

**개념**
- SSL/TLS로 암호화된 FTP
- 기본 포트: 21 (제어), 990 (보안)

**모드**
- Explicit FTPS: 명시적 보안 협상
- Implicit FTPS: 암시적 보안

**인증**
- 사용자명/비밀번호
- 인증서 기반

### 3. FTP (File Transfer Protocol)

**개념**
- 표준 파일 전송 프로토콜
- 암호화 없음 (보안 취약)
- 기본 포트: 21

**사용 시나리오**
- 레거시 시스템과의 호환성
- VPC 내부 프라이빗 전송
- 보안 민감도 낮은 데이터

### 4. AS2 (Applicability Statement 2)

**개념**
- B2B 전자 문서 교환 표준 (EDI)
- HTTP/HTTPS 기반
- 디지털 서명 및 암호화

**특징**
- MDN (Message Disposition Notification) 수신 확인
- 비동기 전송
- 규정 준수 (EDI 표준)

### 5. 웹 브라우저 기반 전송

**Managed File Transfer Portal**
- 웹 UI로 파일 업로드/다운로드
- 클라이언트 소프트웨어 불필요
- 외부 파트너와의 간단한 파일 공유

## 주요 구성 요소

### 1. Transfer Family 서버

**정의**
- 파일 전송 엔드포인트
- 프로토콜 활성화 (SFTP, FTPS, FTP, AS2)

**엔드포인트 유형**

**Public (퍼블릭)**
- 인터넷에서 직접 접근
- AWS 제공 퍼블릭 IP
- 간단한 설정
- 비용: 서버 시간당 과금

**VPC (Virtual Private Cloud)**
- VPC 내부에서만 접근
- Direct Connect, VPN으로 온프레미스 연결
- 프라이빗 IP 사용
- 보안 강화

**VPC_ENDPOINT**
- VPC 엔드포인트를 통한 프라이빗 접근
- 인터넷 게이트웨이 불필요
- 탄력적 IP 연결 가능

### 2. 사용자 (User)

**정의**
- Transfer Family 서버에 접속하는 계정
- IAM 역할과 연결되어 S3/EFS 접근 권한 부여

**사용자 설정**
- 사용자명
- SSH 공개 키 (SFTP)
- 비밀번호 (FTPS, FTP, 커스텀 IdP)
- 홈 디렉토리 (S3 버킷 또는 EFS 경로)
- IAM 역할 (S3/EFS 접근 권한)
- 세션 정책 (추가 제한)

**홈 디렉토리**
- 논리적 홈 디렉토리: 사용자가 `/`로 보이지만 실제는 `s3://bucket/user1/`
- 사용자별 격리된 공간

### 3. IAM 역할

**역할**
- Transfer Family 서버가 S3/EFS에 접근할 때 사용
- 사용자별로 다른 IAM 역할 할당 가능

**예시 정책**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["s3:ListBucket"],
      "Resource": ["arn:aws:s3:::my-bucket"]
    },
    {
      "Effect": "Allow",
      "Action": ["s3:GetObject", "s3:PutObject"],
      "Resource": ["arn:aws:s3:::my-bucket/user1/*"]
    }
  ]
}
```

### 4. 인증 제공자 (Identity Provider)

**서비스 관리형 (Service-managed)**
- Transfer Family에서 직접 사용자 관리
- SSH 키 저장
- 간단한 설정

**AWS Managed Microsoft AD**
- Active Directory 통합
- 기존 AD 사용자로 접속

**커스텀 (Lambda, API Gateway)**
- Lambda 함수로 커스텀 인증 로직 구현
- 외부 데이터베이스, Okta, Ping Identity 연동 가능
- 비밀번호 인증 구현

### 5. 관리형 워크플로 (Managed Workflows)

**정의**
- 파일 업로드 후 자동 실행되는 워크플로
- 서버리스 자동화

**워크플로 단계**
- 복사(Copy): 파일을 다른 위치로 복사
- 태그(Tag): 파일에 태그 추가
- 삭제(Delete): 원본 파일 삭제
- 커스텀(Custom): Lambda 함수 실행 (스캔, 압축, 암호화 등)

**실행 조건**
- 파일 업로드 완료 시
- 특정 폴더에 업로드 시

## 사용 방법

### 1. SFTP 서버 생성 (콘솔)

```
AWS Console → Transfer Family → "Create server"

1. 프로토콜 선택: SFTP
2. 엔드포인트 유형: Public or VPC
3. 인증 제공자: Service-managed
4. 로깅 역할: CloudWatch Logs 역할 선택
5. 보안 정책: TransferSecurityPolicy-2020-06 (권장)
6. 태그 추가 (선택)
7. 생성
```

### 2. S3 버킷 및 IAM 역할 생성

```bash
# S3 버킷 생성
aws s3 mb s3://my-sftp-bucket

# IAM 역할 생성 (정책은 위 예시 참조)
aws iam create-role --role-name TransferFamilyS3Access ...
```

### 3. 사용자 추가

```
Transfer Family 콘솔 → 서버 선택 → Users → "Add user"

1. 사용자명: john
2. IAM 역할: TransferFamilyS3Access
3. 홈 디렉토리: s3://my-sftp-bucket/john/
4. SSH 공개 키 추가 (SFTP)
5. 저장
```

### 4. 클라이언트에서 접속

```bash
# SFTP 접속 (SSH 키 사용)
sftp -i ~/.ssh/id_rsa john@s-1234567890abcdef0.server.transfer.us-east-1.amazonaws.com

# 파일 업로드
put localfile.txt

# 파일 다운로드
get remotefile.txt

# 종료
quit
```

### 5. 커스텀 호스트명 설정 (선택)

```
Route 53에서 CNAME 레코드 생성
  sftp.example.com → s-1234567890abcdef0.server.transfer.us-east-1.amazonaws.com

Transfer Family 서버에 커스텀 호스트명 연결
  콘솔 → 서버 → Edit → Custom hostname: sftp.example.com
```

## 주요 활용 사례

**B2B 파일 교환**
- 파트너·고객·공급업체와 안전하게 파일 교환
- EDI, 송장, 주문서, 재고 데이터
- AS2 프로토콜로 규정 준수

**데이터 레이크 수집**
- 외부 소스에서 S3 데이터 레이크로 데이터 수집
- SFTP로 업로드 → S3 → Glue/Athena로 분석
- ETL 파이프라인 통합

**레거시 시스템 마이그레이션**
- 온프레미스 SFTP 서버를 AWS로 이전
- 사용자·스크립트 변경 없이 마이그레이션
- 서버 관리 부담 제거

**콘텐츠 배포**
- 미디어·문서 파일을 S3로 수집
- CloudFront로 전 세계 배포
- 웹 애플리케이션에서 사용

**규정 준수 및 감사**
- 금융·의료·정부 기관의 파일 전송
- CloudTrail로 모든 전송 활동 로깅
- VPC 엔드포인트로 프라이빗 전송

**자동화된 파일 처리**
- 업로드 즉시 Lambda로 자동 처리
- 바이러스 스캔, 압축, 암호화
- S3 Event + Lambda 또는 Managed Workflows

## 비용

**서버 사용 시간**
- 프로토콜별 시간당 과금
- SFTP/FTPS/FTP: 약 $0.30 per hour
- AS2: 약 $0.30 per hour
- 서버가 실행 중이면 연결 없어도 과금

**데이터 전송**
- 업로드: 무료
- 다운로드: GB당 과금 (약 $0.04 per GB)

**SFTP 커넥터 (아웃바운드)**
- 사용량 기반 과금

**예시 비용**
- SFTP 서버 24시간 운영: $0.30 × 24 = $7.20/day ≈ $216/month
- 100GB 업로드: 무료
- 100GB 다운로드: $4

**비용 최적화 팁**
- 사용하지 않을 때 서버 중지 (자동화)
- VPC 엔드포인트 유형으로 데이터 전송 비용 절감
- 불필요한 다운로드 최소화

## Transfer Family vs 전통적 SFTP 서버

| 항목 | Transfer Family | 전통적 SFTP 서버 |
|------|----------------|-----------------|
| 관리 | 완전 관리형 (서버리스) | 수동 관리 (패치, 백업 등) |
| 확장성 | 자동 스케일링 | 수동 확장 |
| 가용성 | 멀티 AZ 자동 | 수동 구성 |
| 스토리지 | S3, EFS | 로컬 디스크 |
| 비용 | 사용량 기반 | 인스턴스·스토리지 비용 |
| 설정 복잡도 | 낮음 | 높음 |

## 모범 사례

**VPC 엔드포인트 사용**
- 프라이빗 네트워크 전송으로 보안 강화
- Direct Connect/VPN으로 온프레미스 연결
- 인터넷 노출 최소화

**IAM 역할 세밀 제어**
- 사용자별 최소 권한 원칙
- S3 버킷 폴더별 접근 제한
- 세션 정책으로 추가 제한

**SSH 키 로테이션**
- 주기적으로 SSH 키 변경
- 오래된 키 비활성화
- 키 관리 자동화

**CloudWatch 모니터링**
- 전송 성공·실패 알림
- 비정상 접속 패턴 탐지
- 대시보드로 사용량 추적

**CloudTrail 로깅**
- 모든 파일 전송 활동 기록
- 규정 준수 감사
- 보안 사고 조사

**관리형 워크플로 활용**
- 업로드 후 자동 바이러스 스캔
- 자동 압축·암호화
- 다른 S3 버킷으로 자동 이동

**커스텀 호스트명**
- 브랜드 일관성 (sftp.company.com)
- DNS 기반 장애 조치
- 사용자 친화적

**논리적 홈 디렉토리**
- 사용자별 격리된 공간
- 실제 S3 경로 숨김
- 보안 강화

**정기 리뷰**
- 사용하지 않는 사용자 삭제
- 권한 재검토
- 비용 최적화

## 제약 사항

**프로토콜 제한**
- SFTP: SSH 버전 3만 지원
- FTP: Active 모드 미지원 (Passive만)
- 동시 연결 수 제한 (확장 가능)

**파일 크기**
- 단일 파일 최대 5TB (S3)
- EFS 제한 적용

**인증**
- Service-managed는 SSH 키만 지원
- 비밀번호 인증은 커스텀 IdP 필요

## 요약

AWS Transfer Family는 SFTP, FTPS, FTP, AS2 프로토콜을 사용해 S3·EFS로 파일을 안전하게 전송하는 완전 관리형 서버리스 파일 전송 서비스다. 기존 워크플로·클라이언트·자격 증명을 그대로 유지하면서 서버 관리 부담을 제거하고, IAM 기반 세밀한 접근 제어, VPC 프라이빗 전송, 관리형 워크플로를 통한 자동화, CloudTrail 감사 로깅을 제공한다. B2B 파일 교환, 데이터 레이크 수집, 레거시 시스템 마이그레이션, 규정 준수가 필요한 파일 전송 시나리오에 최적화되어 있다.
