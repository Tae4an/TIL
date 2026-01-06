# AWS STS란?

## 개념 정의
AWS STS(Security Token Service, 보안 토큰 서비스)는 AWS 리소스에 접근하는 사용자나 애플리케이션에게 임시 보안 자격 증명을 발급하는 웹 서비스다. 장기 자격 증명(IAM 사용자의 영구 Access Key) 대신 짧은 수명의 임시 자격 증명을 제공해 보안 위험을 최소화하고, 크로스 계정 접근·페더레이션·최소 권한 원칙을 쉽게 구현할 수 있다.

쉽게 말해 "AWS 리소스에 접근할 때 영구 비밀번호 대신 15분~12시간 동안만 유효한 임시 티켓을 발급해주는 서비스"다.

## 핵심 특징

**임시 자격 증명 발급**
- 유효 기간이 있는 단기 자격 증명
- 최소 15분 ~ 최대 36시간 (역할에 따라 12시간 또는 36시간)
- 만료 후 자동 무효화
- 갱신 필요 시 새 토큰 요청

**보안 강화**
- 장기 자격 증명 노출 위험 제거
- 자격 증명 유출 시 피해 최소화 (시간 제한)
- 최소 권한 원칙 적용 용이
- MFA(다중 인증) 강제 가능

**크로스 계정 접근**
- 다른 AWS 계정의 리소스에 안전하게 접근
- 신뢰 관계 기반 역할 위임
- 계정 간 권한 공유 간소화

**페더레이션(Federation)**
- 외부 자격 증명 공급자(IdP)와 통합
- Active Directory, SAML 2.0, OpenID Connect
- 기업 자격 증명으로 AWS 접근

**감사 및 추적**
- CloudTrail로 모든 STS 요청 로깅
- 누가 언제 어떤 역할을 맡았는지 추적
- 컴플라이언스 요구사항 충족

## 임시 보안 자격 증명 구성 요소

STS가 발급하는 임시 자격 증명은 3가지 요소로 구성된다.

**1. Access Key ID**
- 공개 식별자 (사용자명과 유사)
- AWS가 요청자를 인식하는 데 사용
- 예: `ASIAYUN3EWXKO3V2LQMI`

**2. Secret Access Key**
- 비밀 키 (비밀번호와 유사)
- Access Key ID와 함께 인증에 사용
- 예: `TOoszw...` (암호화된 문자열)

**3. Session Token**
- 임시 자격 증명 고유의 추가 보안 계층
- 모든 API 요청에 포함 필수
- MFA 활성화 시 특히 중요
- 예: `IQoJb3JpZ2lu...qEKGDlkEwrZZ`

**유효 기간(Expiration)**
- 자격 증명의 만료 시간
- 예: `2026-01-06T03:40:50+00:00`

## STS 작동 방식

### 기본 플로우

**1. 요청 제출(Request Submission)**
- IAM 사용자, 역할, 페더레이션 사용자, AWS 서비스가 STS에 임시 자격 증명 요청
- AWS CLI, SDK, Management Console, API 호출 사용

**2. 인증 및 권한 부여(Authentication & Authorization)**
- STS가 요청자의 신원 확인
- IAM 정책 및 권한 평가
- 요청자가 해당 작업 권한이 있는지 검증

**3. 자격 증명 발급(Credential Issuance)**
- 인증·권한 확인 성공 시 임시 자격 증명 생성
- Access Key ID, Secret Access Key, Session Token 반환
- 유효 기간 설정

**4. 신뢰 정책 확인(Trust Policy Evaluation, 역할 위임 시)**
- 역할을 맡을 때 신뢰 정책(Trust Policy) 검증
- 요청자가 신뢰 정책에 명시되어 있는지 확인
- 명시되지 않으면 요청 거부

**5. 리소스 접근(Resource Access)**
- 발급받은 임시 자격 증명으로 AWS 서비스 호출
- Session Token을 모든 API 요청에 포함
- 유효 기간 내에만 사용 가능

### 예시: AssumeRole 플로우

```
사용자(IAM User) → STS에 AssumeRole 요청 → 신뢰 정책 확인 → 임시 자격 증명 발급 → S3 접근
```

## 주요 STS API 작업

### 1. AssumeRole

**용도**
- IAM 역할을 임시로 맡아 권한 획득
- 크로스 계정 접근 또는 권한 상승

**사용 시나리오**
- 개발 계정에서 프로덕션 계정의 S3 접근
- 관리자가 임시로 높은 권한 획득
- Lambda가 다른 계정의 리소스 접근

**CLI 예시**
```bash
aws sts assume-role \
  --role-arn arn:aws:iam::111111111111:role/RoleB \
  --role-session-name session1
```

**응답**
```json
{
  "Credentials": {
    "AccessKeyId": "ASIAYUN3EWXKO3V2LQMI",
    "SecretAccessKey": "TOoszw....",
    "SessionToken": "IQoJb3JpZ2lu...qEKGDlkEwrZZ",
    "Expiration": "2026-01-06T03:40:50+00:00"
  },
  "AssumedRoleUser": {
    "AssumedRoleId": "AROAYUN3EWXKDQQGZ7EX3:session1",
    "Arn": "arn:aws:sts::111111111111:assumed-role/RoleB/session1"
  }
}
```

### 2. AssumeRoleWithWebIdentity

**용도**
- 웹 신원 공급자(Web IdP)로 인증된 사용자에게 임시 자격 증명 부여
- Google, Facebook, Amazon Cognito 등 소셜 로그인

**사용 시나리오**
- 모바일 앱 사용자가 S3에 파일 업로드
- 웹 앱 사용자가 DynamoDB 접근
- Cognito 사용자 풀 통합

### 3. AssumeRoleWithSAML

**용도**
- SAML 2.0 기반 페더레이션 사용자에게 임시 자격 증명 부여
- 기업 Active Directory, Okta, Azure AD 등

**사용 시나리오**
- 회사 직원이 AD 자격 증명으로 AWS Management Console 접근
- SSO(Single Sign-On) 구현

### 4. GetSessionToken

**용도**
- IAM 사용자 또는 루트 사용자의 장기 자격 증명을 사용해 임시 자격 증명 획득
- MFA(다중 인증) 강제 시 사용

**사용 시나리오**
- MFA 보호된 API 작업 수행
- 민감한 작업 전 추가 인증

**CLI 예시**
```bash
aws sts get-session-token \
  --serial-number arn:aws:iam::123456789012:mfa/user \
  --token-code 123456
```

### 5. GetFederationToken

**용도**
- 페더레이션 사용자(외부 자격 증명)에게 임시 자격 증명 부여
- IAM 사용자의 권한 일부를 위임

**사용 시나리오**
- 외부 파트너에게 제한적 접근 권한 부여
- 임시 사용자에게 특정 S3 버킷 접근 허용

### 6. GetCallerIdentity

**용도**
- 현재 사용 중인 자격 증명의 정보 확인
- 디버깅·검증용

**CLI 예시**
```bash
aws sts get-caller-identity
```

**응답**
```json
{
  "UserId": "AROAYUN3EWXKCUQU5HSO2:eks-role",
  "Account": "111111111111",
  "Arn": "arn:aws:sts::111111111111:assumed-role/admin/instance-id"
}
```

## 주요 활용 사례

### 1. 크로스 계정 접근

**시나리오**
- 개발 계정(Account A)에서 프로덕션 계정(Account B)의 S3 버킷 접근

**방법**
1. Account B에 역할(RoleB) 생성
2. RoleB의 신뢰 정책에 Account A 허용
3. Account A 사용자가 `AssumeRole`로 RoleB 역할 획득
4. 임시 자격 증명으로 Account B의 S3 접근

**장점**
- 계정 간 안전한 리소스 공유
- 장기 자격 증명 공유 불필요

### 2. EC2 인스턴스의 임시 권한

**시나리오**
- EC2 인스턴스가 S3, DynamoDB에 접근

**방법**
1. IAM 역할 생성 (S3, DynamoDB 권한 부여)
2. EC2 인스턴스에 역할 연결
3. EC2 메타데이터 서비스에서 STS가 자동으로 임시 자격 증명 발급
4. 애플리케이션이 AWS SDK로 자동 인증

**장점**
- 하드코딩된 Access Key 불필요
- 자동 자격 증명 갱신

### 3. Lambda 함수의 역할 위임

**시나리오**
- Lambda가 다른 계정의 리소스에 접근

**방법**
1. Lambda 실행 역할 생성
2. 코드에서 `AssumeRole` API 호출
3. 임시 자격 증명으로 다른 계정 리소스 접근

### 4. 페더레이션 사용자 인증

**시나리오**
- 회사 직원이 Active Directory 자격 증명으로 AWS Console 접근

**방법**
1. SAML 2.0 IdP와 AWS 통합
2. 직원이 AD로 로그인
3. IdP가 SAML 어설션 발급
4. AWS가 `AssumeRoleWithSAML`로 임시 자격 증명 발급
5. AWS Management Console 접근

**장점**
- 별도 IAM 사용자 생성 불필요
- 중앙 집중식 자격 증명 관리

### 5. 모바일/웹 앱 사용자 인증

**시나리오**
- 모바일 앱 사용자가 S3에 사진 업로드

**방법**
1. Amazon Cognito 사용자 풀 생성
2. 앱 사용자가 Google/Facebook으로 로그인
3. Cognito가 `AssumeRoleWithWebIdentity` 호출
4. 임시 자격 증명으로 S3 업로드

**장점**
- 앱에 AWS 자격 증명 하드코딩 불필요
- 사용자별 세밀한 권한 제어

### 6. MFA 보호 작업

**시나리오**
- 민감한 작업(루트 사용자 권한, 리소스 삭제) 시 MFA 강제

**방법**
1. IAM 정책에 MFA 조건 추가
2. 사용자가 `GetSessionToken`으로 MFA 코드 입력
3. MFA 인증된 임시 자격 증명 발급
4. 민감한 작업 수행

**장점**
- 추가 보안 계층
- 권한 남용 방지

## 유효 기간 설정

### 기본 유효 기간

**AssumeRole**
- 기본: 1시간
- 최소: 15분
- 최대: 12시간 (역할 최대 세션 기간 설정에 따름)

**GetSessionToken**
- 기본: 12시간
- 최소: 15분
- 최대: 36시간 (IAM 사용자의 경우)

**AssumeRoleWithWebIdentity / AssumeRoleWithSAML**
- 기본: 1시간
- 최소: 15분
- 최대: 12시간

### 유효 기간 연장 방법

**역할 최대 세션 기간 변경**
- IAM 역할 설정에서 최대 세션 기간 조정
- 최대 12시간까지 설정 가능

**자격 증명 갱신**
- 만료 전에 새로운 STS 요청
- 애플리케이션에서 자동 갱신 로직 구현

## 보안 모범 사례

### 1. 최소 권한 원칙

**IAM 정책 세밀 설정**
- 역할에 필요한 최소 권한만 부여
- 와일드카드(*) 사용 최소화

**세션 정책(Session Policy)**
- `AssumeRole` 호출 시 추가 제한 정책 전달
- 임시 자격 증명의 권한을 더욱 제한

### 2. 외부 ID(External ID) 사용

**크로스 계정 접근 시**
- 신뢰 정책에 External ID 조건 추가
- 혼동된 대리자 문제(Confused Deputy Problem) 방지

**예시**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::123456789012:root"
      },
      "Action": "sts:AssumeRole",
      "Condition": {
        "StringEquals": {
          "sts:ExternalId": "unique-external-id-12345"
        }
      }
    }
  ]
}
```

### 3. MFA 강제

**민감한 역할에 MFA 조건 추가**
```json
{
  "Condition": {
    "Bool": {
      "aws:MultiFactorAuthPresent": "true"
    }
  }
}
```

### 4. 리전별 STS 엔드포인트 사용

**글로벌 vs 리전별**
- 기본: 글로벌 엔드포인트 (`sts.amazonaws.com`)
- 권장: 리전별 엔드포인트 (`sts.ap-northeast-2.amazonaws.com`)

**장점**
- 지연 시간 감소
- 중복성 추가
- 리전별 세션 토큰은 모든 리전에서 유효

### 5. 자격 증명 로테이션

**정기적 갱신**
- 임시 자격 증명은 자동 만료되지만, 갱신 주기 짧게 유지
- 장기 자격 증명(IAM 사용자 Access Key)은 정기 로테이션

### 6. CloudTrail 모니터링

**STS 활동 추적**
- 모든 `AssumeRole`, `GetSessionToken` 요청 로깅
- 비정상 역할 위임 탐지
- 알람 설정 (의심스러운 패턴)

### 7. 신뢰 정책 엄격 관리

**최소 신뢰 범위**
- 신뢰 정책에 특정 계정·역할만 명시
- `arn:aws:iam::*:root` 같은 와일드카드 금지

## STS vs IAM 사용자 장기 자격 증명

| 항목 | STS 임시 자격 증명 | IAM 사용자 장기 자격 증명 |
|------|-------------------|----------------------|
| 유효 기간 | 15분~36시간 | 영구 (수동 삭제 전까지) |
| 보안 위험 | 낮음 (자동 만료) | 높음 (유출 시 지속 위험) |
| 갱신 | 새 요청 필요 | 불필요 |
| 사용 복잡도 | 약간 높음 | 낮음 |
| 권한 관리 | 동적·세밀 | 정적 |
| 페더레이션 | 지원 | 미지원 |
| 크로스 계정 | 용이 | 어려움 |
| 권장 용도 | 대부분의 경우 | 특수한 경우만 (레거시) |

## 비용

**STS 자체 비용**
- STS API 호출은 **무료**
- 임시 자격 증명 발급·사용에 별도 과금 없음

**사용하는 리소스 비용**
- 임시 자격 증명으로 접근하는 AWS 서비스(S3, EC2 등)에 대한 비용만 발생

## 요약

AWS STS(Security Token Service)는 15분~36시간 동안 유효한 임시 보안 자격 증명(Access Key ID, Secret Access Key, Session Token)을 발급하는 서비스로, 장기 자격 증명 노출 위험을 제거하고 크로스 계정 접근·페더레이션·최소 권한 원칙을 쉽게 구현한다. AssumeRole, AssumeRoleWithWebIdentity, AssumeRoleWithSAML, GetSessionToken 등의 API를 통해 IAM 역할 위임, EC2/Lambda 임시 권한, 모바일/웹 앱 인증, 기업 SSO, MFA 보호 작업 등 다양한 시나리오에 활용되며, 최소 권한 원칙, External ID, MFA 강제, 리전별 엔드포인트, CloudTrail 모니터링, 신뢰 정책 엄격 관리를 통해 안전하게 운영할 수 있다.
