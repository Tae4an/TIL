# AWS App2Container란?

## 개념 정의

AWS App2Container(A2C)는 온프레미스·VM·EC2에서 실행 중인 **기존 .NET 및 Java 애플리케이션을 코드 변경 없이 컨테이너화**해서 Amazon ECS, EKS, App Runner에 배포할 수 있게 도와주는 **명령줄(CLI) 마이그레이션 도구**다. 애플리케이션을 자동으로 검색하고, 종속성을 분석하며, Docker 이미지, ECS 작업 정의, Kubernetes YAML, CloudFormation 템플릿, CI/CD 파이프라인까지 자동으로 생성해 준다.

쉽게 말해, "IIS에서 돌아가는 .NET이나 Tomcat에서 돌아가는 Java 앱을 CLI 몇 줄로 Docker 컨테이너로 만들고, ECS/EKS에 배포하는 데 필요한 모든 파일을 자동 생성해주는 컨테이너 전환 도구"다.

## 핵심 기능

- **애플리케이션 인벤토리(Discovery)**  
  서버에서 실행 중인 ASP.NET(IIS) 및 Java 애플리케이션(Tomcat, JBoss, WebSphere, WebLogic 등)을 자동 검색하고 목록화한다.

- **종속성 분석**  
  애플리케이션의 프로세스, 네트워크 포트, 협력 프로세스 등 런타임 종속성을 자동으로 분석해 컨테이너에 포함할 항목을 식별한다.

- **컨테이너 이미지 생성**  
  애플리케이션 파일·종속성을 패키징한 Docker 이미지를 자동 생성하고, Amazon ECR에 푸시한다.

- **배포 아티팩트 자동 생성**  
  ECS 작업 정의, EKS Kubernetes YAML, CloudFormation 템플릿을 자동 생성해, AWS 모범 사례(보안·확장성)에 따라 배포할 수 있게 한다.

- **CI/CD 파이프라인 생성**  
  AWS CodePipeline, CodeBuild, CodeDeploy 기반 CI/CD 파이프라인을 자동 생성하거나, 기존 Jenkins, Azure DevOps 등에 통합할 수 있는 아티팩트를 제공한다.

- **Windows 인증 지원**  
  Windows 인증을 사용하는 IIS 애플리케이션의 경우, gMSA(그룹 관리 서비스 계정), AD 보안 그룹, 도메인 조인 ECS 노드까지 자동 설정한다.

## 지원 애플리케이션

### .NET (Windows)
- ASP.NET (.NET Framework 3.5 이상)
- IIS 7.5 이상에서 실행되는 웹 애플리케이션

### Java (Linux)
- Apache Tomcat
- JBoss
- Spring Boot
- IBM WebSphere
- Oracle WebLogic
- 일반 Java 애플리케이션

## 작동 방식

1. **초기화**: `app2container init` 명령으로 AWS 자격 증명, S3 버킷 등 설정
2. **인벤토리**: `app2container inventory` 명령으로 서버의 애플리케이션 목록 생성
3. **분석**: `app2container analyze` 명령으로 특정 애플리케이션의 종속성·포트·프로세스 분석
4. **추출·컨테이너화**: `app2container containerize` 명령으로 Docker 이미지 생성
5. **배포 아티팩트 생성**: ECS/EKS 배포 파일, CloudFormation 템플릿, CI/CD 파이프라인 생성
6. **배포**: `app2container generate app-deployment` 명령으로 ECS/EKS에 자동 배포

## 주요 활용 사례

- **레거시 애플리케이션 현대화**  
  오래된 .NET Framework/Java 애플리케이션을 컨테이너로 전환해, 모던 CI/CD, 오토 스케일링, MSA 아키텍처로 전환하는 첫 단계로 활용한다.

- **온프레미스 → AWS 마이그레이션**  
  물리 서버·VM에서 실행되는 애플리케이션을 코드 수정 없이 리프트 앤 시프트 방식으로 ECS/EKS로 이전한다.

- **운영 효율화**  
  여러 서버에 분산된 애플리케이션을 컨테이너로 통합해, 단일 CI/CD 파이프라인과 관리 도구로 운영 오버헤드를 줄인다.

- **빠른 컨테이너화 PoC**  
  컨테이너 전환 가능성을 빠르게 검증하고, 마이그레이션 범위·비용·시간을 산정하는 PoC에 활용한다.

- **Windows 애플리케이션 컨테이너화**  
  Windows Server에서 실행되는 .NET Framework 앱을 Windows 컨테이너(ECS on Windows, EKS on Windows)로 전환한다.

## 비용

AWS App2Container 자체는 **무료**다. ECR, ECS, EKS, S3, CodePipeline 등 사용하는 AWS 서비스에 대한 표준 요금만 부과된다.

## 정리

AWS App2Container는 온프레미스·VM·EC2에서 실행 중인 .NET 및 Java 애플리케이션을 코드 변경 없이 컨테이너화하는 CLI 도구로, 애플리케이션 검색·종속성 분석·Docker 이미지 생성·ECS/EKS 배포 아티팩트·CI/CD 파이프라인을 자동으로 생성하며, IIS의 ASP.NET과 Tomcat·JBoss·WebSphere·WebLogic 등의 Java 앱을 지원하고, Windows 인증·gMSA·도메인 조인까지 자동 설정하며, 레거시 애플리케이션 현대화, 온프레미스 → AWS 마이그레이션, 운영 효율화, PoC에 활용되고, 도구 자체는 무료이며 사용하는 AWS 서비스 비용만 부과된다.
