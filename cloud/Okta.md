# Okta란?
## 1. 개념 요약
Okta는 **클라우드 기반 IAM(Identity and Access Management) 플랫폼**으로, 기업의 사용자 인증·권한 부여·라이프사이클 관리를 한 곳에서 통합 처리한다.
쉽게 말해, **"직원·고객이 어떤 앱에 누가 어떻게 접근할 수 있는지를 중앙에서 관리하는 ID 허브"** 라고 보면 된다.

***
## 2. 핵심 기능
- **SSO (Single Sign-On)**
  - 한 번 로그인으로 AWS, Salesforce, Slack, Office 365 등 수천 개 앱에 접근
  - 비밀번호 관리 부담 감소, 사용자 경험 향상

- **MFA (Multi-Factor Authentication)**
  - Okta Verify, FIDO2, 생체인식(TouchID·FaceID·Windows Hello) 등 다양한 인증 수단 지원
  - 적응형 MFA로 위험도에 따라 추가 인증 강도 자동 조정

- **사용자 프로비저닝·디프로비저닝**
  - 입사 시 앱 계정 자동 생성, 퇴사 시 자동 비활성화
  - HR 시스템(Workday 등)과 연동해 라이프사이클 자동화

- **SAML·OIDC·SCIM 지원**
  - 표준 프로토콜 기반으로 거의 모든 앱·서비스와 연동 가능
  - AWS IAM Identity Center와 SAML·SCIM으로 네이티브 연동

- **Universal Directory**
  - AD·LDAP·소셜 계정·외부 IDP를 하나의 디렉터리로 통합 관리

***
## 3. 어디에 쓰이는가
- **직원 IAM (Workforce Identity)**
  - 사내 임직원이 업무 앱에 안전하게 접근하도록 SSO·MFA 적용

- **고객 IAM (Customer Identity - CIAM)**
  - 서비스 가입자의 로그인·소셜 로그인·비밀번호 관리를 Okta로 위임

- **AWS 연동**
  - Okta → AWS IAM Identity Center로 SAML 페더레이션
  - 직원이 Okta 계정으로 AWS 콘솔·CLI에 접근

- **Zero Trust 구현**
  - 사용자·기기·위치·행동 기반 접근 제어로 Zero Trust 네트워크 설계

- **규정 준수**
  - SOC 2, ISO 27001 등 컴플라이언스 감사 로그 자동화

***
## 4. 유사 솔루션과의 비교
| 항목 | Okta | Microsoft Entra ID | AWS IAM Identity Center |
|---|---|---|---|
| **주요 강점** | 멀티 클라우드·앱 통합 | Microsoft 365 환경 최적화 | AWS 환경 네이티브 |
| **벤더 종속** | 없음 (중립) | Microsoft 생태계 | AWS 생태계 |
| **앱 통합 수** | 7,000개+ 사전 통합 | Microsoft 앱 중심 | AWS 서비스 중심 |
| **CIAM** | 강력 지원 | 제한적 | 미지원 |
| **온프레미스 연동** | AD·LDAP 모두 지원 | AD 네이티브 | 제한적 |

***
## 5. 한 줄 정리
Okta는 **SSO·MFA·프로비저닝·디렉터리를 통합 제공하는 클라우드 기반 IAM 플랫폼**으로, 어떤 클라우드·앱 환경에서도 "누가, 어디에, 어떻게 접근할 수 있는지"를 중앙에서 통제하는 "ID 보안의 허브"다.
