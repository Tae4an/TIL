# AWS IAM Identity Center란?

## 개념 정의

AWS IAM Identity Center(구 AWS SSO)는 조직 내 직원 계정(Workforce Identity)을 중앙에서 관리하고, **여러 AWS 계정과 SaaS·기업 애플리케이션에 SSO(싱글 사인온)로 접근**하게 해주는 서비스다. 하나의 로그인으로 여러 AWS 계정과 애플리케이션에 접근할 수 있게 해 주고, 사용자·그룹·권한을 한 곳에서 관리할 수 있다.

쉽게 말해, “회사 직원용 계정 체계를 한 번만 만들고, 거기서 여러 AWS 계정과 앱(QuickSight, Grafana, Salesforce 등)에 로그인·권한을 통합 관리하는 중앙 ID/SSO 서비스”다.

## 핵심 기능

- **중앙 집중식 사용자·그룹 관리**  
  IAM Identity Center 디렉터리에서 직접 사용자·그룹을 생성하거나, 회사 디렉터리/IdP(Azure AD, Okta, AD, Google Workspace 등)와 연동해 자동 동기화.

- **싱글 사인온(SSO)**  
  한 번 로그인하면 여러 AWS 계정 콘솔, CLI/SDK, 그리고 SAML/OIDC 기반 비즈니스 애플리케이션에 재로그인 없이 접근 가능.

- **멀티 계정 권한 관리 (Permission Sets)**  
  권한 세트(하나 이상의 IAM 정책을 묶은 단위)를 정의해 여러 AWS 계정의 사용자·그룹에 할당, 계정마다 IAM 사용자 생성 없이 역할 기반 접근 제어 구현.

- **애플리케이션 통합**  
  Amazon Q, QuickSight, Managed Grafana, SageMaker Studio, Systems Manager 등 AWS 관리형 앱과, SAML/OIDC 지원 SaaS·사내 앱에 대한 접근을 포털 하나에서 제공.

- **MFA·보안 정책 연동**  
  외부 IdP의 MFA 정책 및 보안 정책과 연계하거나, AWS 내에서 MFA를 요구해 보안 강화.

## 주요 활용 사례

- **멀티 계정 AWS 환경 운영**  
  수십·수백 개 AWS 계정을 AWS Organizations로 관리하면서, 직원 계정은 IAM Identity Center에서 한 번만 만들고, 각 계정에 필요한 권한 세트만 할당.

- **기업 SSO 통합**  
  기존 AD/Azure AD/Okta 등을 이미 쓰고 있는 조직에서, 해당 IdP를 IAM Identity Center와 연결해 회사 계정으로 AWS 및 관련 애플리케이션에 로그인.

- **역할 기반 접근 제어(RBAC)**  
  개발자, 운영자, 보안, 데이터 분석가 등 역할별 권한 세트를 정의해 여러 계정에 공통 적용, 권한 관리 표준화.

- **감사·온보딩/오프보딩 단순화**  
  입·퇴사 시 Identity Center에서 계정/그룹만 추가·삭제하면, 연결된 모든 계정·애플리케이션의 접근 권한이 일괄 반영되어 휴먼 에러와 잔여 권한을 줄임.
