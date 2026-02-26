# AWS AssumeRole이란?

## 개념 정의

AssumeRole은 AWS STS(Security Token Service)가 제공하는 기능으로, **다른 IAM Role의 권한을 일시적으로 위임·수임하기 위한 메커니즘**이다.  
하나의 주체(IAM User, IAM Role, OIDC/SAML 주체 등)가 STS를 통해 다른 Role을 “가정(assume)”하여, 해당 Role에 부여된 권한으로 일정 시간 동안 AWS 리소스에 접근할 수 있게 해준다.  

쉽게 말하면 “필요할 때만 잠깐 다른 권한 세트(역할)로 갈아타서 작업하고, 세션이 끝나면 원래 상태로 돌아오는 구조”라고 볼 수 있음.  

***

## 핵심 특징

**임시 보안 자격 증명 발급**

- STS `AssumeRole` 호출 시 AccessKeyId, SecretAccessKey, SessionToken 세트가 생성된다.  
- 이 자격 증명은 설정한 세션 기간 동안만 유효하며, 만료 후에는 자동 폐기되는 임시 자격 증명임.  

**역할 기반 권한 위임**

- 대상 Role에 어떤 권한을 줄지는 IAM 정책으로 정의한다.  
- “누가 이 Role을 Assume할 수 있는가”는 Role의 **신뢰 정책(Trust Policy)** 로 제어.  
- 실제 API 권한은 “Role 권한 정책(필수) + 세션 정책(선택)”의 교집합으로 결정된다.  

**교차 계정(Cross‑Account) 및 동일 계정 모두 지원**

- 다른 AWS 계정의 Role을 Assume하여 그 계정 리소스에 접근하는 패턴 지원.  
- 동일 계정 내에서도 권한 분리/승급 용도로 별도 Role을 만들어 필요 시 Assume하는 구조 사용.  

**보안 강화 및 최소 권한 설계**

- Access Key처럼 장기적으로 살아 있는 자격 증명이 아니라, **만료 시간이 있는 토큰**만 사용한다.  
- 작업 단위로 필요한 Role만 Assume하여, 최소 권한 원칙(Least Privilege) 구현에 적합함.  

***

## 주요 구성 요소

**IAM Role**

- “어떤 작업을 할 수 있는가”를 정의하는 권한 컨테이너.  
- 직접 로그인하는 계정이 아니라, 필요한 시점에 Assume해서 사용하는 역할(역할 기반 접근 제어의 단위).  

**Trust Policy(신뢰 정책)**

- 이 Role을 **어떤 주체(Principal)가 Assume할 수 있는지**를 정의하는 정책.  
- 예: 특정 계정의 IAM Role, 사용자, SAML 제공자, OIDC 주체(GitHub Actions, EKS ServiceAccount 등).  

**STS AssumeRole API**

- `AssumeRole`, `AssumeRoleWithSAML`, `AssumeRoleWithWebIdentity` 등.  
- 호출 시 Role ARN, 세션 이름, 세션 기간, (선택) 세션 정책을 전달한다.  
- 응답으로 임시 자격 증명과 만료 시간이 포함됨.  

**임시 자격 증명(Temporary Credentials)**

- AccessKeyId / SecretAccessKey / SessionToken 세트로 구성.  
- 환경 변수, 프로파일, SDK 설정 등에 주입하여 해당 Role의 권한으로 API 호출에 사용.  

***

## 동작 방식

1. **Role 준비**  
   - 대상 계정/환경에 IAM Role 생성.  
   - Role에 권한 정책(예: S3 ReadOnly, CloudWatch Logs Write 등) 연결.  
   - Trust Policy에 “어떤 주체가 이 Role을 Assume할 수 있는지” 정의.  

2. **AssumeRole 호출**  
   - 호출 주체(사용자/Role/OIDC 주체)가 STS의 `AssumeRole` API 또는 `aws sts assume-role` 명령 실행.  
   - 파라미터로 Role ARN, 세션 이름, 세션 기간(예: 900초~1시간 이상, Role 설정에 따라 상한) 등을 전달.  

3. **임시 자격 증명 발급**  
   - STS가 Trust Policy 확인 후 허용 시, 임시 자격 증명을 반환.  
   - 반환 값에는 AccessKeyId, SecretAccessKey, SessionToken, Expiration(만료 시각)이 포함된다.  

4. **임시 자격 증명으로 작업 수행**  
   - SDK/CLI/애플리케이션에서 이 자격 증명을 사용해 S3, EC2, DynamoDB 등 서비스 호출.  
   - 이때 권한은 해당 Role에 정의된 정책(± 세션 정책)에 따라 제한됨.  

5. **세션 만료**  
   - Expiration 시각 이후에는 해당 자격 증명으로 더 이상 호출 불가.  
   - 추가 작업이 필요하면 다시 `AssumeRole` 호출로 새로운 세션 생성.  

***

## 기존 장기 자격 증명 vs AssumeRole 비교

| 항목             | 장기 자격 증명(Access Key)                 | AssumeRole 기반 임시 자격 증명          |
|------------------|--------------------------------------------|----------------------------------------|
| 유효 기간        | 별도 삭제 전까지 사실상 무기한             | 수분~수시간 단위, 자동 만료             |
| 발급 주체        | IAM User                                  | STS(AssumeRole 호출 결과)             |
| 보안 리스크      | 유출 시 장기간 악용 가능                    | 유출돼도 짧은 시간 내 무력화            |
| 권한 관리        | 사용자별 권한 직접 관리 필요                | Role 단위로 권한 설계·재사용            |
| 교차 계정 접근   | 계정마다 별도 키 발급 필요                  | Role + Trust Policy로 안전한 위임       |
| 모범 사례 적합성 | 권장되지 않음(필요 최소 사용)              | 권장 방식 (특히 자동화/CI·CD/멀티계정)  |

***

## 주요 활용 사례

**교차 계정(Cross‑Account) 리소스 접근**

- 계정 A의 운영 도구가 계정 B의 S3 버킷, CloudWatch Logs, DynamoDB 등을 읽거나 쓰는 상황.  
- 계정 B에 Role 생성 → 계정 A를 신뢰 주체로 설정 → 계정 A에서 AssumeRole로 B 계정 리소스 접근.  

**CI/CD 및 자동화 파이프라인 권한 위임**

- GitHub Actions, GitLab CI, Jenkins, CodeBuild 등 외부/내부 파이프라인이 AWS 리소스를 조작해야 하는 경우.  
- OIDC/SAML 주체 기반으로 STS AssumeRole 사용 → 배포 시점에만 짧은 세션으로 권한 사용.  

**운영/보안 계정 분리 및 중앙 통제**

- 로그/보안 계정에서 여러 업무 계정의 CloudTrail/S3/Config 데이터를 수집할 때.  
- 각 업무 계정에 “수집용 Role”을 만들고, 중앙 계정에서 그 Role들을 Assume하여 데이터 수집.  

**콘솔에서 역할 전환(Switch Role)**

- 운영자 한 명이 여러 계정(운영, 스테이징, 보안, 공용 등)을 관리해야 하는 상황.  
- 콘솔의 “역할 전환” 기능으로 각 계정의 Role을 Assume해서, 계정·권한 세트를 손쉽게 전환.  

***

## 기본 워크플로 (CLI 예시)

### 1. AssumeRole 호출

```bash
aws sts assume-role \
  --role-arn arn:aws:iam::123456789012:role/CrossAccountReadOnlyRole \
  --role-session-name my-session \
  --duration-seconds 3600
```

- JSON 응답에 `AccessKeyId`, `SecretAccessKey`, `SessionToken`, `Expiration` 포함.  

### 2. 임시 자격 증명 환경 변수로 설정

```bash
export AWS_ACCESS_KEY_ID=임시_AccessKeyId
export AWS_SECRET_ACCESS_KEY=임시_SecretAccessKey
export AWS_SESSION_TOKEN=임시_SessionToken
```

이후 같은 셸 세션에서 실행하는 `aws s3 ls`, `aws ec2 describe-instances` 등의 명령은  
Assume한 Role의 권한으로 실행됨.  

***

## 보안 모범 사례

- 교차 계정 접근, 자동화·파이프라인, 외부 연동에는 **장기 Access Key 대신 AssumeRole 기반 임시 자격 증명 사용**.  
- Role 권한 정책은 작업 단위로 세분화해서 최소 권한만 부여.  
- Role의 Trust Policy에 허용할 주체를 명확히 제한하고, `sts:ExternalId` 등의 조건을 활용해 오용 방지.  
- 세션 시간(`DurationSeconds`)을 실제 작업에 필요한 최소 수준으로 설정.  
- CloudTrail로 `AssumeRole` 호출 및 임시 자격 증명 사용 내역을 기록·모니터링.  

AssumeRole은 멀티 계정·자동화·외부 연동이 기본값이 된 환경에서, **권한 위임·보안·운영 편의성**을 동시에 만족시키기 위해 사실상 표준처럼 사용되는 핵심 기능이다.
