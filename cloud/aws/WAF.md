# AWS WAF(Web Application Firewall)

## 개념 정의

AWS WAF는 **웹 애플리케이션 계층(OSI 7계층)에서 동작하는 완전관리형 웹 방화벽** 서비스로, SQL 인젝션·XSS·봇 트래픽 등 일반적인 웹 공격 패턴을 탐지하고 차단한다. 네트워크 방화벽이 IP/포트 기반으로 트래픽을 제어하는 것과 달리, WAF는 HTTP 요청의 내용(URL, 헤더, 바디, 쿼리스트링 등)을 직접 검사해 악의적인 패턴을 필터링한다.

쉽게 말해, "웹 요청을 애플리케이션에 도달하기 전에 규칙에 따라 허용·차단·모니터링하는 웹 전용 보안 문지기"다.

## 핵심 구성 요소

### 웹 ACL (Web Access Control List)
WAF의 핵심 리소스로, 규칙과 규칙 그룹의 집합이다. 보호할 AWS 리소스에 웹 ACL을 연결하면, 들어오는 모든 HTTP 요청이 웹 ACL의 규칙을 순서대로 평가받는다.

### 규칙 (Rule)
요청에서 검사할 트래픽 패턴(조건)과 일치 시 수행할 액션을 정의한다.

- **Allow**: 요청을 통과시켜 애플리케이션에 전달
- **Block**: 요청을 차단하고 403 응답 반환
- **Count**: 차단하지 않고 일치 건수만 집계 (규칙 테스트 시 활용)
- **CAPTCHA / Challenge**: 봇 여부 확인을 위한 CAPTCHA 또는 자동 브라우저 챌린지 실행

### 규칙 그룹 (Rule Group)
여러 규칙을 재사용 가능한 단위로 묶은 것이다.

- **AWS 관리형 규칙 그룹**: AWS가 자동으로 업데이트·유지관리하는 사전 구성 규칙 세트 (추가 설정 없이 바로 활용 가능)
- **AWS Marketplace 관리형 규칙**: Imperva, Fortinet 등 보안 전문 벤더가 제공하는 규칙 세트
- **사용자 정의 규칙 그룹**: 직접 작성한 규칙을 그룹화하여 재사용

### WCU (Web ACL Capacity Unit)
규칙 실행에 필요한 리소스 비용을 측정하는 단위로, 웹 ACL당 기본 WCU 한도(1,500)가 존재하며, 복잡한 규칙일수록 높은 WCU를 소비한다.

## 검사 가능한 요청 요소

AWS WAF는 아래 HTTP 요청 구성 요소를 기준으로 규칙 조건을 정의할 수 있다.

- **IP 주소 / 지역**: 특정 IP 범위, 국가/지역 기반 차단
- **URI**: 요청 경로 패턴 매칭
- **HTTP 헤더**: User-Agent, Referer, 커스텀 헤더 값 검사
- **HTTP 바디**: POST 요청 본문 내용 검사
- **쿼리 문자열**: URL 파라미터 검사
- **쿠키**: 쿠키 값 검사

## AWS 관리형 규칙 그룹 (주요 예시)

| 규칙 그룹 | 차단 대상 | 과금 |
|-----------|----------|------|
| Core Rule Set (CRS) | XSS, 명령어 인젝션, OWASP Top 10 | 무료 |
| SQL Database | SQL 인젝션 공격 | 무료 |
| Known Bad Inputs | Log4Shell, SSRF 등 알려진 취약점 | 무료 |
| Amazon IP Reputation List | 악성 IP, 봇넷 IP | 무료 |
| Anonymous IP List | VPN, Tor, 프록시 IP | 무료 |
| Bot Control | 스크래퍼, 크롤러, 악성 봇 탐지·차단 | 유료 |
| Fraud Control - Account Takeover | 크리덴셜 스터핑, 계정 탈취 시도 | 유료 |

## 연결 가능한 AWS 서비스

AWS WAF는 아래 서비스에 직접 연결하여 해당 서비스로 들어오는 트래픽을 검사한다.

- **Amazon CloudFront**: 엣지 로케이션에서 글로벌 트래픽 필터링 (가장 앞단)
- **Application Load Balancer (ALB)**: 리전 단위 웹 트래픽 필터링
- **Amazon API Gateway**: REST API 요청 보호
- **AWS AppSync**: GraphQL API 요청 보호
- **Amazon Cognito**: 사용자 풀 인증 엔드포인트 보호
- **AWS App Runner**: 컨테이너 앱 트래픽 보호
- **AWS Amplify**: 프론트엔드 앱 트래픽 보호
- **AWS Verified Access**: 제로트러스트 접근 환경 보호

## 동작 흐름

```
사용자 요청
   ↓
CloudFront / ALB / API Gateway (트래픽 수신)
   ↓
AWS WAF로 요청 전달
   ↓
웹 ACL 규칙 순차 평가
   ├─ 일치 시 → Allow / Block / Count / CAPTCHA
   └─ 미일치 → 기본 액션(Allow 또는 Block)
   ↓
(허용된 경우) 백엔드 애플리케이션 도달
```

## AWS Shield와의 차이 및 연계

| 항목 | AWS WAF | AWS Shield |
|------|---------|------------|
| 계층 | L7 (애플리케이션 계층) | L3/L4 (네트워크/전송 계층) |
| 주요 대상 | SQL 인젝션, XSS, 봇, 웹 취약점 | DDoS 공격 (SYN Flood, UDP Flood 등) |
| 과금 | WAF + 규칙 수 기반 | Shield Standard(무료) / Advanced(유료) |
| 연계 | 함께 사용 시 L3~L7 전 계층 보호 | CloudFront + WAF + Shield 조합 권장 |

CloudFront에 WAF와 Shield를 함께 적용하면, 엣지 레벨에서 네트워크 DDoS(Shield)와 웹 공격(WAF)을 모두 방어하는 다중 계층 보안 아키텍처를 구성할 수 있다.

## AWS Firewall Manager와의 연계

AWS Firewall Manager는 여러 AWS 계정과 리전에 걸쳐 WAF 규칙을 **중앙에서 일괄 적용·관리**하는 서비스다. 새 리소스가 추가될 때 자동으로 WAF 정책을 적용하므로, 대규모 멀티 계정 환경(AWS Organizations)에서 보안 정책의 일관성을 유지하는 데 활용된다.

## 비용 구조

- **웹 ACL**: 월 $5/개
- **규칙**: 월 $1/개 (웹 ACL당)
- **요청 처리**: $0.60/백만 건
- **Bot Control 등 스마트 규칙 그룹**: 별도 추가 요금
- **로그 전송** (Kinesis Data Firehose 등): 별도 요금
