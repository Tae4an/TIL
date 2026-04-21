# SSO Federation Access란

## 1. 개념 요약

SSO Federation Access는 **연합(Federation) 기반 SSO를 통해, 서로 다른 도메인·조직이 운영하는 애플리케이션에 외부 IdP(Identity Provider)의 인증을 신뢰해 접근하게 하는 방식** 을 말한다.  
즉, **“사용자는 자기 회사 계정으로만 로그인하고, 그 인증을 가지고 여러 외부 서비스에도 자동으로 들어가는 연합형 SSO 접근 방식”** 이다.

***

## 2. SSO와 Federation의 관계

- **일반 SSO**  
  - 보통 **한 조직·한 도메인 안**에서 여러 시스템 간 로그인 상태를 공유하는 것을 의미한다.  
  - 예: 회사 포털에 한 번 로그인하면 그룹웨어, 메일, 전자결재를 재로그인 없이 사용.

- **Federation(연합 인증)**  
  - **서로 다른 조직·도메인 간**에 신뢰 관계를 맺고, 인증·ID 정보를 표준 프로토콜(SAML, OpenID Connect, WS-Federation 등)로 주고받는 것.  
  - A 조직 IdP가 “이 사용자는 인증된 사용자이며, 이 권한을 가진다”고 서명해 넘기면, B 조직 서비스(SP)가 그 서명을 신뢰한다.

- **Federated SSO / Federation Access**  
  - 사용자는 **자기 조직 IdP에만 로그인**하고, 그 토큰(Assertion/ID Token)을 들고 여러 외부 서비스(SP)에 자동 로그인한다.  
  - 예: 회사 계정(AD/Okta/Entra ID)으로 로그인 → AWS 콘솔, Salesforce, Slack, Jira 등 외부 서비스에 별도 계정·비밀번호 없이 접근.

***

## 3. 기본 동작 흐름

1. 사용자가 외부 서비스(예: Salesforce, AWS 콘솔)에 접속 시도.  
2. 서비스(SP)가 “이 사용자의 인증은 외부 IdP가 담당한다”고 판단하고 **IdP로 리다이렉트**한다.  
3. 사용자는 IdP(Okta, Azure AD, 사내 ADFS 등)에 로그인하거나, 이미 로그인되어 있으면 SSO로 통과한다.  
4. IdP는 **서명된 토큰(SAML Assertion, OIDC ID Token 등)** 을 생성해 서비스(SP)에 전달한다.  
5. 서비스는 토큰의 서명·유효 기간·클레임(이메일, 그룹, 권한 등)을 검증 후 세션을 만들고 접근을 허용한다.

이 과정 전체를 통해 **“페더레이션된(연합된) SSO 액세스”**, 즉 SSO Federation Access가 구현된다.

***

## 4. 어디에 쓰이는가

- **SaaS 통합 로그인**  
  - Okta, Entra ID, Ping 같은 IdP 계정으로 Salesforce, Google Workspace, Slack, Zoom, Jira 등 다양한 SaaS에 한 번에 로그인.

- **클라우드 콘솔 접근**  
  - 사내 IdP 계정으로 AWS, Azure, GCP 콘솔에 접속하고, 클라우드에서는 SAML/OIDC 토큰을 기반으로 임시 Role·권한을 부여.

- **파트너·그룹사 간 시스템 공유**  
  - 그룹사 A의 계정으로 그룹사 B의 포털·주문 시스템 등에 접근(양측 도메인 간 Federation Trust).

- **Zero Trust / 중앙 IAM 구현**  
  - 인증은 중앙 IdP에 집중시키고, 각 서비스는 Federation을 통해 토큰 기반 접근만 허용하여 계정·권한 관리를 일원화.

***

## 5. 일반 SSO vs Federation Access 비교

| 항목 | 일반 SSO | Federation Access (Federated SSO) |
|---|---|---|
| 범위 | 한 조직·한 도메인 내부 | 여러 조직·도메인 간 |
| 인증 주체 | 내부 SSO 서버 | 외부 IdP (조직/파트너/IdaaS) |
| 프로토콜 | 쿠키 기반, 내부 세션 공유 등 | SAML, OpenID Connect, WS-Fed 등 표준 |
| 예시 | 회사 포털 → 그룹웨어·메일 | 회사 계정으로 AWS·Salesforce·파트너 포털 접속 |
| 목적 | 내부 시스템 편의성 | 외부 SaaS·파트너까지 포함한 통합 액세스 |

***

## 6. 한 줄 정리

SSO Federation Access는 **사용자가 한 번 자기 조직 IdP에 로그인하면, SAML/OIDC 같은 표준 연합 프로토콜로 여러 외부 서비스·조직에까지 안전하게 SSO로 접근할 수 있게 해주는 “도메인 간 연합형 SSO 접근 방식”** 이다.
