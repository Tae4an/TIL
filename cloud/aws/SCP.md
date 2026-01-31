# Service Control Policy (SCP)란?

## 개념 정의

Service Control Policy (SCP)는 AWS Organizations에서 멤버 계정의 최대 권한 범위를 중앙에서 제어하는 정책이다. 조직 루트, OU(Organizational Unit), 개별 계정 레벨에 연결하여 해당 범위 내 모든 IAM 사용자와 역할이 수행할 수 있는 작업의 상한선을 설정하며, IAM 정책보다 우선순위가 높아 IAM이 허용해도 SCP가 차단하면 작업을 수행할 수 없다.

쉽게 말해 "조직 전체에 걸쳐 '이것만큼은 절대 안 돼'라고 금지하는 최상위 보안 울타리로, 개별 계정이 아무리 권한을 줘도 SCP가 막으면 실행되지 않는 정책"이다.

## 핵심 특징

- IAM 정책보다 상위 권한 제어
- OU 또는 계정 레벨에 적용 (계층적 상속)
- 관리 계정(Management Account)에는 적용 안 됨
- 루트 사용자 포함 모든 주체에 적용
- Allow 또는 Deny 리스트 정의
- Service-Linked Role에는 영향 없음

## 주요 구성 요소

**조직 계층 구조**
- Root (조직 최상위)
- OU (Organizational Unit)
- 계정 (Member Account)
- SCP는 상위에서 하위로 상속됨

**정책 유형**
- Allow List: 명시적으로 허용한 작업만 가능 (기본 FullAWSAccess)
- Deny List: 특정 작업을 명시적으로 차단

**정책 요소 (JSON)**
- Effect: Allow 또는 Deny
- Action: AWS 서비스 작업 (예: s3:DeleteBucket)
- Resource: 대상 리소스 (주로 "*" 사용)
- Condition: 조건부 제약 (IP, 리전 등)

## SCP vs IAM Policy

| 항목 | SCP | IAM Policy |
|------|-----|-----------|
| 적용 범위 | 조직/OU/계정 레벨 | 사용자/역할/그룹 레벨 |
| 역할 | 최대 권한 경계 설정 | 실제 권한 부여 |
| 우선순위 | 최상위 (IAM보다 우선) | SCP 범위 내에서만 작동 |
| 권한 부여 | 권한 부여 안 함 (제한만) | 권한 부여 |
| 명시적 거부 | SCP Deny가 최우선 | IAM Deny도 우선 |
| 관리 계정 | 적용 안 됨 | 적용됨 |

**권한 평가 흐름**
1. SCP가 Deny → 차단 (IAM 무관)
2. SCP가 Allow 없음 → 차단
3. SCP가 Allow → IAM Policy 평가
4. IAM Policy Allow → 허용
5. IAM Policy 없음 → 차단

## 작동 방식

### Allow List 전략 (기본)
1. 조직 생성 시 FullAWSAccess SCP 자동 연결
2. 모든 AWS 서비스 허용
3. 특정 서비스만 허용하려면 커스텀 SCP 생성 후 FullAWSAccess 제거

### Deny List 전략 (권장)
1. FullAWSAccess는 유지
2. 추가로 Deny SCP를 연결하여 특정 작업 차단
3. 관리가 간단하고 실수 위험 낮음

### 계층적 상속
- Root에 연결된 SCP → 모든 OU와 계정에 적용
- OU에 연결된 SCP → 해당 OU와 하위 OU, 계정에 적용
- 계정에 연결된 SCP → 해당 계정만 적용
- 여러 SCP가 적용되면 교집합만 허용 (가장 제한적인 정책 적용)

## 주요 활용 사례

**보안 서비스 보호**
CloudTrail, GuardDuty, Security Hub 비활성화를 차단하여 감사 로그와 보안 모니터링을 강제한다.

```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Deny",
    "Action": [
      "cloudtrail:StopLogging",
      "cloudtrail:DeleteTrail"
    ],
    "Resource": "*"
  }]
}
```

**리전 제한 (Data Residency)**
특정 리전 외 리소스 생성을 차단하여 데이터 주권 요구사항을 충족한다.

```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Deny",
    "NotAction": ["cloudfront:*", "route53:*", "iam:*"],
    "Resource": "*",
    "Condition": {
      "StringNotEquals": {
        "aws:RequestedRegion": ["us-east-1", "eu-west-1"]
      }
    }
  }]
}
```

**루트 사용자 제한**
루트 사용자의 특정 작업을 차단하여 보안을 강화한다.

```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Deny",
    "Action": "*",
    "Resource": "*",
    "Condition": {
      "StringLike": {
        "aws:PrincipalArn": "arn:aws:iam::*:root"
      }
    }
  }]
}
```

**고비용 서비스 차단**
개발/테스트 환경에서 비싼 인스턴스 타입을 차단하여 비용을 제어한다.

```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Deny",
    "Action": "ec2:RunInstances",
    "Resource": "arn:aws:ec2:*:*:instance/*",
    "Condition": {
      "StringNotEquals": {
        "ec2:InstanceType": ["t3.micro", "t3.small"]
      }
    }
  }]
}
```

**S3 공개 접근 차단**
모든 계정에서 S3 버킷 공개를 금지하여 데이터 유출을 방지한다.

```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Deny",
    "Action": [
      "s3:PutBucketPublicAccessBlock",
      "s3:PutAccountPublicAccessBlock"
    ],
    "Resource": "*",
    "Condition": {
      "StringNotEquals": {
        "s3:PublicAccessBlockConfiguration": "true"
      }
    }
  }]
}
```

**VPC 변경 차단**
네트워크 팀 외 VPC 변경을 차단하여 네트워크 안정성을 보장한다.

**태그 정책 강제**
특정 태그 없이 리소스 생성을 차단하여 비용 추적과 컴플라이언스를 강제한다.

## 통합 서비스

- AWS Organizations: SCP 관리 플랫폼
- AWS Control Tower: 사전 정의된 Guardrails (SCP 기반)
- AWS CloudTrail: SCP 위반 시도 로깅
- AWS Config: SCP 규정 준수 모니터링
- AWS IAM: SCP와 함께 권한 평가

## 모범 사례

**Deny List 전략 사용**
FullAWSAccess를 유지하고 추가로 Deny SCP를 연결하여 특정 작업만 차단하며, Allow List는 복잡하고 실수 위험이 크다.

**테스트 계정에서 검증**
프로덕션에 적용 전 개발 계정에서 SCP를 테스트하며, 의도치 않은 차단을 확인한다.

**명확한 명명 규칙**
SCP 이름에 목적을 명시하며, 예: "Deny-Root-User", "Restrict-Regions-EU-Only"

**문서화**
각 SCP의 목적과 영향을 문서화하고, 변경 이력을 기록하여 팀 간 공유한다.

**점진적 적용**
처음엔 작은 OU에 적용하고, 안정화 후 점진적으로 확대하며, 한 번에 전체 조직에 적용하지 않는다.

**CloudTrail 모니터링**
SCP 위반 시도를 CloudTrail로 추적하고, 알림을 설정하여 보안 이상 징후를 감지한다.

**Service-Linked Role 예외**
특정 AWS 서비스는 Service-Linked Role이 필요하므로, SCP에서 해당 역할을 예외 처리한다.

**관리 계정 보호**
관리 계정에는 SCP가 적용되지 않으므로, 접근을 최소화하고 MFA를 강제한다.

## 제약 사항

**관리 계정 미적용**
Organizations의 관리 계정은 SCP 영향을 받지 않으므로, 접근 제어를 별도로 강화해야 한다.

**Service-Linked Role 미적용**
AWS 서비스가 자동 생성하는 Service-Linked Role은 SCP를 우회하므로, 서비스 기능이 제한되지 않는다.

**복잡한 디버깅**
SCP와 IAM Policy가 겹치면 권한 문제 디버깅이 어려우므로, IAM Policy Simulator와 CloudTrail을 활용한다.

**정책 크기 제한**
SCP 하나당 최대 5,120자이며, 계정당 최대 5개 SCP 연결 가능하다.

**즉시 적용**
SCP 변경은 즉시 반영되므로, 잘못된 설정 시 즉시 서비스 중단이 발생할 수 있다.

**역추적 불가**
SCP는 과거 작업에 영향을 주지 않으며, 적용 후부터만 효력이 발생한다.

## 비용

SCP 자체는 무료이며, Organizations 사용 비용만 발생한다 (Organizations도 무료). 단, SCP로 인한 서비스 제한으로 추가 계정이나 리소스가 필요하면 비용이 발생할 수 있다.

## 요약

Service Control Policy (SCP)는 AWS Organizations에서 멤버 계정의 최대 권한 범위를 중앙 제어하는 정책으로, IAM 정책보다 우선순위가 높아 IAM이 허용해도 SCP가 차단하면 작업이 불가능하며, 조직 루트, OU, 계정 레벨에 연결하여 계층적으로 상속되고, Allow List 또는 Deny List 전략으로 권한을 제어하며, 루트 사용자 포함 모든 주체에 적용되지만 관리 계정과 Service-Linked Role에는 영향이 없고, 보안 서비스 보호, 리전 제한, 루트 사용자 차단, 고비용 서비스 차단, S3 공개 접근 방지에 활용되며, Deny List 전략, 테스트 검증, 점진적 적용, CloudTrail 모니터링이 권장된다.
