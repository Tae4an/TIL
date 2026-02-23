# AWS SSM Parameter Store

## 개념 정의

AWS SSM Parameter Store(Systems Manager Parameter Store)는 **애플리케이션 구성 데이터와 비밀 값을 안전하게 저장·관리하는 중앙 집중식 계층형 키-값 저장소**다. DB 접속 정보, API 키, 라이선스 코드, AMI ID 같은 값을 소스코드에 하드코딩하지 않고 Parameter Store에 저장한 뒤 이름으로 참조할 수 있다. 기본적으로 **무료(Standard Tier)** 로 사용 가능하여 비용 부담 없이 환경변수 관리 용도로 널리 활용된다.

쉽게 말해, "코드에 직접 박아놓던 `.env` 파일의 내용을 AWS에서 안전하게 중앙 관리하는 서비스"다.

## 파라미터 타입 3가지

- **String**: 일반 평문 텍스트 값 저장 (예: 서버 주소, 환경 이름, AMI ID 등 비민감 데이터)
- **StringList**: 쉼표로 구분된 문자열 목록 저장 (예: `us-east-1,us-west-2,ap-northeast-2`)
- **SecureString**: **AWS KMS로 암호화하여 저장**하는 민감 데이터 (예: DB 패스워드, API 시크릿, 토큰) — 값 조회 시 자동 복호화, 콘솔에서 `****`로 마스킹 표시

## Standard vs Advanced Tier

| 항목 | Standard | Advanced |
|------|----------|----------|
| 파라미터 수 | 계정당 최대 10,000개 | 최대 100,000개 |
| 파라미터 값 크기 | 최대 4KB | 최대 8KB |
| 파라미터 정책 | 미지원 | 만료(Expiration)·알림(Notification) 정책 지원 |
| 비용 | 무료 | 파라미터당 $0.05/월 |
| 고급 처리량 | 기본 처리량만 지원 | 높은 처리량 옵션 지원 |

## 계층형 구조 (Hierarchical Naming)

파라미터 이름에 `/`를 활용해 경로처럼 계층 구조로 구성할 수 있다.

```
/myapp/prod/db/password
/myapp/prod/db/host
/myapp/staging/db/password
/myapp/staging/db/host
```

- 경로 단위로 권한을 제어할 수 있어 환경별(prod/staging/dev) 접근 분리에 유용
- `GetParametersByPath` API로 특정 경로 하위 파라미터를 한 번에 조회 가능

## 주요 특징

- **버전 관리**: 파라미터 값을 변경할 때마다 자동으로 버전 기록 저장, 이전 버전 값 조회 가능
- **IAM 통합**: IAM 정책으로 특정 사용자·역할이 접근 가능한 파라미터 경로를 세밀하게 제어
- **KMS 통합**: SecureString 타입에 AWS 관리형 KMS 키 또는 고객 관리형 KMS 키로 암호화
- **CloudTrail 통합**: 파라미터 조회·변경 모든 작업이 CloudTrail에 기록되어 감사(Audit) 가능
- **EventBridge 통합**: 파라미터 변경 이벤트를 트리거로 자동화 파이프라인 구성 가능

## AWS 서비스와의 연동

- **ECS / EKS**: Task Definition에서 `valueFrom`으로 Parameter Store ARN 참조하여 컨테이너 환경변수 주입
- **Lambda**: 실행 시 SDK로 파라미터 조회하거나, Lambda Extension을 이용해 캐싱 후 참조
- **CloudFormation**: 스택 배포 시 `{{resolve:ssm:/path/to/param}}` 문법으로 파라미터 값 참조
- **EC2 User Data / SSM Run Command**: 스크립트 내 `ssm:parameter-name` 형식으로 참조
- **AWS CodePipeline / CodeBuild**: 빌드·배포 파이프라인에서 민감한 환경변수를 Parameter Store에서 주입
- **Terraform**: `aws_ssm_parameter` 리소스로 파라미터 생성·관리, `data "aws_ssm_parameter"`로 조회

## 사용 예시 (AWS CLI)

```bash
# 파라미터 저장 (평문)
aws ssm put-parameter \
  --name "/myapp/prod/db/host" \
  --value "mydb.cluster.us-east-1.rds.amazonaws.com" \
  --type String

# 파라미터 저장 (암호화)
aws ssm put-parameter \
  --name "/myapp/prod/db/password" \
  --value "sup3rS3cr3t!" \
  --type SecureString

# 파라미터 조회 (복호화 포함)
aws ssm get-parameter \
  --name "/myapp/prod/db/password" \
  --with-decryption

# 경로 하위 전체 조회
aws ssm get-parameters-by-path \
  --path "/myapp/prod" \
  --with-decryption
```

## SSM Parameter Store vs Secrets Manager

| 항목 | SSM Parameter Store | AWS Secrets Manager |
|------|--------------------|--------------------|
| 기본 비용 | **무료** (Standard Tier) | 시크릿당 약 $0.40/월 + API 호출 비용 |
| 암호화 | 선택적 (SecureString) | 항상 암호화 |
| 자동 시크릿 로테이션 | 미지원 | 지원 (Lambda 기반 자동 교체) |
| 비밀번호 자동 생성 | 미지원 | 지원 |
| 크로스 계정 접근 | 미지원 | 지원 |
| 값 크기 | 최대 4KB (Standard) | 최대 65KB |
| 계층형 구조 | 지원 | 지원 |
| 주요 용도 | 환경변수, 설정값, 비민감+민감 혼용 | DB 자격증명 등 고급 시크릿 관리 |

**선택 기준**: 일반 환경변수·설정값 중심이면 Parameter Store(무료), DB 자격증명 자동 교체·크로스 계정 공유가 필요하면 Secrets Manager가 적합하다.
