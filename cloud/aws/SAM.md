# AWS SAM이란?

## 1. 개념 요약

AWS SAM(Serverless Application Model)은 **서버리스 애플리케이션을 정의·빌드·배포하기 위한 오픈소스 IaC(코드형 인프라) 프레임워크**다.  
CloudFormation의 확장판으로, **간단한 YAML 몇 줄로 Lambda·API Gateway·DynamoDB를 정의하면 배포 시 자동으로 CloudFormation 스택으로 변환**해 리소스를 생성한다.

***

## 2. 핵심 구성 요소

- **SAM Template (`template.yaml`)**
  - 서버리스 리소스를 YAML/JSON으로 정의하는 구성 파일
  - CloudFormation보다 훨씬 짧고 직관적인 문법 제공

- **SAM CLI**
  - 로컬 빌드·테스트·디버깅·배포를 담당하는 커맨드라인 도구
  - Docker로 Lambda 로컬 실행 환경을 시뮬레이션

- **SAM 리소스 타입**
  - `AWS::Serverless::Function` → Lambda 함수
  - `AWS::Serverless::Api` → API Gateway
  - `AWS::Serverless::SimpleTable` → DynamoDB 테이블

***

## 3. 주요 CLI 명령어

- **`sam init`** : 프로젝트 초기화 (템플릿·런타임 선택)
- **`sam build`** : 로컬에서 애플리케이션 빌드
- **`sam local invoke`** : Lambda 함수 로컬 실행
- **`sam local start-api`** : API Gateway 로컬 시뮬레이션
- **`sam deploy`** : AWS 클라우드에 배포
- **`sam sync --watch`** : 코드 변경 감지 → 자동 클라우드 동기화

***

## 4. 어디에 쓰이는가

- **서버리스 애플리케이션 개발**
  - Lambda + API Gateway + DynamoDB 조합을 빠르게 구성·배포

- **로컬 개발 환경**
  - 실제 AWS 비용 없이 로컬에서 Lambda 실행·디버깅 가능

- **CI/CD 파이프라인 통합**
  - CodePipeline, GitHub Actions와 연동해 자동 배포 파이프라인 구축

- **이벤트 기반 아키텍처**
  - S3·SNS·SQS·DynamoDB Streams 이벤트 트리거 정의를 간단하게 처리

***

## 5. CloudFormation vs SAM vs CDK

| 항목 | CloudFormation | SAM | CDK |
|---|---|---|---|
| **주요 목적** | 범용 인프라 정의 | 서버리스 특화 IaC | 프로그래밍 언어로 IaC |
| **문법** | 장황한 YAML/JSON | 간결한 YAML (서버리스 단축 구문) | Python·TypeScript 등 코드 |
| **서버리스 최적화** | 직접 작성 필요 | 최적화됨 | 지원하나 범용 목적 |
| **로컬 테스트** | 불가 | SAM CLI로 가능 | 불가 (CDK만으로는) |
| **러닝커브** | 중간 | 낮음 | 높음 |

***

## 6. 한 줄 정리

AWS SAM은 **간단한 YAML 템플릿과 SAM CLI로 서버리스 애플리케이션을 로컬 테스트부터 클라우드 배포까지 한 번에 처리**할 수 있는 "서버리스 전용 IaC 프레임워크"다.
