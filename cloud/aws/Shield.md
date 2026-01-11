# AWS Shield란?

## 개념 정의
AWS Shield는 AWS에서 실행되는 애플리케이션을 DDoS(Distributed Denial of Service, 분산 서비스 거부) 공격으로부터 보호하는 관리형 보안 서비스다. 네트워크 트래픽을 실시간으로 모니터링하여 DDoS 공격을 자동으로 탐지하고 완화하며, Standard와 Advanced 두 가지 계층으로 제공되어 기본 보호부터 고급 보호까지 다양한 요구사항을 충족한다.

쉽게 말해 "대량의 악성 트래픽으로 서비스를 마비시키려는 DDoS 공격을 자동으로 막아주는 AWS 방어막 서비스"다.

## DDoS 공격이란?

### 개념
여러 소스(봇넷, 감염된 컴퓨터)에서 동시에 대량의 트래픽을 보내 서버, 네트워크, 애플리케이션의 가용성을 방해하는 공격이다.

### 주요 DDoS 공격 유형

**Layer 3/4 공격 (네트워크·전송 계층)**
- SYN Flood: TCP 연결 요청 대량 전송
- UDP Flood: UDP 패킷 폭탄
- Reflection Attacks: 반사 증폭 공격
- ICMP Flood: Ping Flood

**Layer 7 공격 (애플리케이션 계층)**
- HTTP Flood: 정상적인 HTTP 요청을 대량으로 전송
- DNS Query Flood: DNS 쿼리 폭주
- Slowloris: 느린 연결 유지로 리소스 고갈

### 공격 영향
- 서비스 다운타임
- 사용자 접근 불가
- 비용 급증 (트래픽·컴퓨팅 리소스 증가)
- 브랜드 이미지 훼손

## AWS Shield 계층

### AWS Shield Standard

**특징**
- 모든 AWS 고객에게 무료 제공
- 자동 활성화 (별도 설정 불필요)
- Layer 3/4 DDoS 공격 방어
- 가장 흔하고 자주 발생하는 공격 차단
- 모든 AWS 리전·엣지 로케이션에서 사용 가능

**보호 범위**
- 모든 AWS 서비스 (EC2, ELB, CloudFront, Route 53, Global Accelerator 등)
- SYN Flood, UDP Flood, Reflection Attacks 방어
- 실시간 트래픽 검사

**장점**
- 추가 비용 없음
- 자동 보호
- AWS 인프라 전체에 적용
- 운영 부담 없음

**제한 사항**
- 공격 알림·보고서 미제공
- Layer 7 보호 제한적
- DDoS Response Team 지원 없음
- 비용 보호 없음

### AWS Shield Advanced

**특징**
- 유료 프리미엄 서비스 (월 $3,000)
- 1년 약정 필수
- Layer 3/4/7 고급 DDoS 보호
- 맞춤형 탐지·완화
- 24/7 DDoS Response Team (DRT) 지원

**보호 대상 리소스**
- Amazon EC2 Elastic IP
- Elastic Load Balancer (ALB, NLB, CLB)
- Amazon CloudFront 배포
- Amazon Route 53 호스팅 영역
- AWS Global Accelerator

**주요 기능**

#### 1. 고급 DDoS 탐지·완화
- 애플리케이션 트래픽 패턴 기반 맞춤 탐지
- 작은 규모 공격도 감지
- Layer 7 (애플리케이션 계층) 공격 자동 완화
- AWS WAF 규칙 자동 생성·적용

#### 2. 실시간 공격 알림
- CloudWatch 메트릭으로 공격 알림
- SNS 토픽으로 즉시 통지
- 상세한 공격 진단·분석

#### 3. DDoS Response Team (DRT) 24/7 지원
- AWS 보안 전문가 팀
- 공격 대응 지원
- 사후 분석·권장 사항
- Business·Enterprise Support 고객만 이용 가능

#### 4. 비용 보호 (DDoS Cost Protection)
- DDoS 공격으로 인한 스케일 아웃 비용 환불
- ELB, CloudFront, Route 53 사용량 급증 비용 보호
- 청구서 크레딧 형태로 환불

#### 5. AWS WAF 무료 사용
- Shield Advanced 구독 시 추가 비용 없이 WAF 사용
- WebACL, 규칙 비용 면제
- 애플리케이션 계층 보호 강화

#### 6. Global Threat Dashboard
- 실시간 공격 현황
- 이력 공격 데이터
- 트래픽 패턴 분석

#### 7. Health-based Detection
- Route 53 Health Check와 통합
- 애플리케이션 가용성 모니터링
- 비정상 상태 시 자동 완화

## 작동 방식

### 트래픽 모니터링
- AWS Shield가 모든 인바운드 트래픽을 지속적으로 모니터링
- 정상 트래픽 베이스라인 학습
- 이상 패턴 실시간 감지

### 자동 탐지
- 머신러닝 알고리즘으로 DDoS 패턴 식별
- 정적 임계값·트래픽 시그니처·이상 탐지 기법 조합
- Shield Advanced: 리소스별 맞춤 탐지

**탐지 기준**
- 트래픽 볼륨 급증
- 비정상적인 패킷 패턴
- 알려진 DDoS 시그니처
- 애플리케이션 응답 시간 저하

### 자동 완화

**Shield Standard**
- 네트워크·전송 계층 공격 자동 차단
- AWS 글로벌 엣지 네트워크에서 악성 트래픽 필터링
- 정상 트래픽만 애플리케이션으로 전달

**Shield Advanced**
- Layer 3/4/7 모든 계층 보호
- AWS WAF 규칙 자동 생성·적용
- 애플리케이션별 맞춤 완화 전략
- 수초 내 완화 조치 적용

### 트래픽 정화 (Scrubbing)
- AWS 글로벌 DDoS 스크러빙 센터로 트래픽 우회
- 악성 트래픽 필터링
- 정상 트래픽만 목적지로 전달
- 소스에 가까운 위치에서 차단

## 주요 활용 사례

### 1. 웹 애플리케이션 보호

**시나리오**
- 전자상거래 사이트
- 뉴스·미디어 플랫폼
- SaaS 애플리케이션

**구성**
- CloudFront + ALB + EC2
- Shield Advanced 활성화
- WAF 규칙으로 Layer 7 보호
- Route 53 Health Check 통합

**효과**
- HTTP Flood 공격 자동 차단
- 서비스 다운타임 방지
- 사용자 경험 유지

### 2. API 보호

**시나리오**
- 공개 REST API
- 모바일 백엔드 API

**구성**
- API Gateway + Lambda
- Shield Advanced로 API Gateway 보호
- Rate-based WAF 규칙

**효과**
- API Flood 공격 차단
- 정당한 API 요청만 처리

### 3. DNS 보호

**시나리오**
- 도메인 서비스 가용성 보장

**구성**
- Route 53 호스팅 영역
- Shield Advanced 활성화

**효과**
- DNS Query Flood 방어
- 도메인 해석 지속 가능

### 4. 게임 서버 보호

**시나리오**
- 멀티플레이어 게임
- 실시간 대전 게임

**구성**
- EC2 Elastic IP
- Shield Advanced 보호
- Network Load Balancer

**효과**
- SYN Flood, UDP Flood 차단
- 게임 서버 안정성 유지

### 5. 금융·결제 서비스 보호

**시나리오**
- 온라인 뱅킹
- 결제 게이트웨이

**구성**
- ALB + EC2 (프라이빗 서브넷)
- Shield Advanced + WAF
- DRT 지원 활성화

**효과**
- 고급 DDoS 공격 방어
- 규제 준수 (가용성 보장)
- 전문가 지원

## Shield Advanced 설정 방법

### 1. 전제 조건
- AWS Business 또는 Enterprise Support 플랜 (DRT 지원용)
- 보호할 리소스 (EIP, ALB, CloudFront 등)

### 2. 활성화 단계

**콘솔에서 활성화**
1. AWS Shield 콘솔 접속
2. "Subscribe to Shield Advanced" 클릭
3. 이용 약관 동의 (1년 약정)
4. 보호할 리소스 선택
5. Health Check 구성 (선택)
6. 자동 완화 설정 (권장)
7. SNS 알림 설정

**CLI로 활성화**
```bash
aws shield subscribe-to-shield-advanced

aws shield create-protection \
  --name "MyALB-Protection" \
  --resource-arn arn:aws:elasticloadbalancing:region:account:loadbalancer/app/my-alb
```

### 3. 자동 완화 구성

**Application Layer DDoS Automatic Mitigation**
- Shield Advanced가 WAF 규칙을 자동 생성
- 공격 패턴에 따라 동적으로 규칙 조정
- 수동 개입 불필요

**설정 옵션**
- Automatic (권장): 자동으로 규칙 활성화
- Count-only: 규칙은 생성하되 차단은 안 함 (모니터링용)

### 4. DRT 권한 부여 (선택)

**Proactive Engagement**
- DRT가 공격 탐지 시 고객에게 먼저 연락
- 긴급 연락처 등록 필요
- 사전 대응으로 다운타임 최소화

**IAM 역할 생성**
- DRT가 WAF 규칙을 대신 생성·수정할 수 있도록 권한 부여

## Shield vs WAF

### 차이점

| 항목 | AWS Shield | AWS WAF |
|------|-----------|---------|
| 목적 | DDoS 공격 방어 | 웹 애플리케이션 취약점 방어 |
| 공격 유형 | 대량 트래픽 공격 | SQL Injection, XSS, Bot 등 |
| 계층 | Layer 3/4/7 (Advanced) | Layer 7 (애플리케이션) |
| 작동 방식 | 자동 탐지·완화 | 규칙 기반 필터링 |
| 비용 | Standard 무료, Advanced $3,000/월 | WebACL $5/월 + 규칙당 $1/월 |
| 통합 | 자동 (Standard) 또는 리소스별 (Advanced) | ALB, CloudFront, API Gateway |

### 통합 사용 (권장)

**Shield Advanced + WAF**
- Shield Advanced 구독 시 WAF 무료
- Layer 3~7 전체 보호
- DDoS + 웹 취약점 모두 방어

**사용 예시**
```
Layer 3/4 (네트워크·전송) → Shield Advanced 자동 완화
Layer 7 (애플리케이션) → Shield Advanced가 WAF 규칙 생성 + WAF 커스텀 규칙
```

## 비용

### Shield Standard
- **무료** (모든 AWS 고객)
- 추가 비용 없음

### Shield Advanced

**구독 비용**
- 월 $3,000 (조직당)
- 1년 약정 필수
- 조직 내 모든 계정에 적용 (Organizations 통합)

**데이터 전송 비용**
- Shield Advanced 자체는 데이터 전송 비용 없음
- 기본 AWS 데이터 전송 비용은 발생

**비용 보호 (DDoS Cost Protection)**
- DDoS 공격으로 인한 스케일 아웃 비용 환불
- ELB, CloudFront, Route 53 사용량 급증 비용 크레딧
- 청구서 분석 후 자동 적용

**WAF 비용**
- Shield Advanced 구독 시 WAF 무료
- WebACL, 규칙, 요청 비용 면제
- 단독 WAF 사용 시: WebACL $5/월 + 규칙당 $1/월 + 요청 백만 건당 $0.60

### 비용 예시

**Shield Standard**
- 비용: $0

**Shield Advanced (중견 기업)**
- 구독: $3,000/월 × 12개월 = $36,000/년
- WAF 비용 절감: ~$100/월 (포함)
- DRT 지원: 포함
- 비용 보호: 포함

**ROI 고려 사항**
- DDoS 공격 1회 다운타임 비용 vs Shield Advanced 연간 비용
- 대규모 전자상거래, 금융, 미디어 기업에 적합
- 공격 빈도가 높거나 가용성이 중요한 서비스

## 모범 사례

### 1. 멀티 레이어 방어

**Defense in Depth**
- Shield (DDoS 방어) + WAF (웹 공격 방어) + Security Group (네트워크 필터링)
- CloudFront (엣지 방어) + ALB (리전 방어)
- Route 53 (DNS 방어)

### 2. 아키텍처 설계

**CloudFront 사용**
- 엣지 로케이션에서 공격 차단
- 오리진 서버 보호
- 캐싱으로 부하 감소

**Auto Scaling**
- 트래픽 급증 시 자동 확장
- Shield와 결합하여 복원력 향상

**프라이빗 서브넷**
- 오리진 서버를 프라이빗 서브넷에 배치
- 직접 공격 불가능
- ALB/CloudFront를 통해서만 접근

### 3. 모니터링·알림

**CloudWatch 알람 설정**
- DDoS 공격 탐지 알림
- 트래픽 이상 징후 모니터링

**SNS 통합**
- 공격 발생 시 즉시 이메일·SMS 알림
- PagerDuty, Slack 연동

### 4. Health Check 구성

**Route 53 Health Check**
- 애플리케이션 가용성 모니터링
- Shield Advanced가 건강 상태 기반 탐지

### 5. 정기 훈련

**DDoS 대응 계획 수립**
- 공격 발생 시 대응 절차
- 담당자 역할·책임 명시
- 정기 모의 훈련

### 6. DRT 활용

**Proactive Engagement 활성화**
- 공격 초기 대응 지원
- 긴급 연락처 최신 유지
- DRT 권한 사전 부여

### 7. 로그·분석

**CloudTrail 로깅**
- Shield API 호출 기록
- 공격 이력 추적

**VPC Flow Logs**
- 네트워크 트래픽 패턴 분석
- 공격 소스 식별

## 제한 사항

### Shield Standard
- 공격 알림 없음
- 상세 리포트 없음
- Layer 7 보호 제한적
- DRT 지원 없음

### Shield Advanced
- 1년 약정 필수 (중도 해지 불가)
- 월 $3,000 비용
- DRT 지원은 Business/Enterprise Support 필요
- 특정 리소스 타입만 지원 (EC2 EIP, ELB, CloudFront, Route 53, Global Accelerator)

## Shield Advanced vs 기타 DDoS 방어 솔루션

### Shield Advanced vs 온프레미스 DDoS 방어

| 항목 | Shield Advanced | 온프레미스 솔루션 |
|------|----------------|----------------|
| 용량 | 무제한 (AWS 글로벌 네트워크) | 제한적 (하드웨어) |
| 확장성 | 자동·즉시 | 수동·시간 소요 |
| 초기 비용 | 없음 (구독제) | 높음 (장비 구매) |
| 유지보수 | AWS 관리 | 자체 관리 |
| 전문가 지원 | DRT 24/7 | 벤더 지원 |

### Shield Advanced vs 3rd Party CDN DDoS 방어

| 항목 | Shield Advanced | Cloudflare, Akamai |
|------|----------------|-------------------|
| AWS 통합 | 네이티브 통합 | API 연동 필요 |
| 비용 보호 | 있음 | 없음 |
| 커버리지 | AWS 리소스 | 모든 웹사이트 |
| 설정 | AWS 콘솔 | 별도 설정 |

## 요약

AWS Shield는 DDoS 공격으로부터 AWS 애플리케이션을 보호하는 관리형 서비스로, 무료로 제공되는 Shield Standard는 모든 AWS 고객에게 자동 활성화되어 Layer 3/4 네트워크·전송 계층 공격(SYN Flood, UDP Flood, Reflection Attacks)을 실시간으로 탐지·완화하며, 월 $3,000(1년 약정)의 Shield Advanced는 Layer 3/4/7 전체 계층 보호, 애플리케이션 트래픽 패턴 기반 맞춤 탐지, 자동 Layer 7 완화(WAF 규칙 자동 생성), 24/7 DDoS Response Team 지원, DDoS 비용 보호(스케일 아웃 비용 환불), AWS WAF 무료 사용을 제공한다. EC2 Elastic IP, ELB, CloudFront, Route 53, Global Accelerator를 보호 대상으로 하며, 웹 애플리케이션, API, DNS, 게임 서버, 금융 서비스 보호에 활용되고, CloudFront + ALB + Shield Advanced + WAF로 멀티 레이어 방어, Auto Scaling, 프라이빗 서브넷, Route 53 Health Check, CloudWatch 알림, DRT Proactive Engagement 활성화가 모범 사례다.
