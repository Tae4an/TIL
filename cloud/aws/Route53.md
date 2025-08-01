# AWS Route 53란?

## Route 53의 기본 정의

**AWS Route 53은 Amazon Web Services에서 제공하는 클라우드 기반의 확장 가능하고, 안정적인 도메인 이름 시스템(DNS) 웹 서비스다**. 도메인 이름 등록, DNS 라우팅, 서비스 상태 체크 기능까지 통합 제공하는 글로벌 DNS 관리 플랫폼이다.

**간단히 말해, 인터넷 사용자가 특정 도메인(ex: mysite.com)을 입력하면 실제로 연결돼야 할 웹 서버의 IP 주소로 안내해주는 인터넷의 ‘주소 안내소’ 같은 역할**을 한다.

## Route 53의 주요 기능

### 1. 도메인 이름 등록

- .com, .net, .co.kr 등 다양한 도메인 등록 가능
- 만료/이전/연장 등 표준 도메인 관리 지원
- WHOIS 프라이버시 보호와 DNSSEC 지원

### 2. DNS 레코드 관리

**다양한 유형의 DNS 레코드 관리 가능**:
- A, AAAA: IPv4/IPv6 주소 지정
- CNAME: 별칭 레코드로 도메인 간 연결
- MX: 이메일 서버 설정
- TXT: 인증, 정책, 도메인 소유 인증 등에 사용
- Alias: S3, CloudFront 등 AWS 리소스와 바로 연결

### 3. 트래픽 라우팅 및 분산

**전 세계 어디서든 빠르고 안정적으로 서비스 접속 유도**:
- Simple: 단일 IP 또는 엔드포인트에 라우팅
- Weighted: 여러 리소스에 트래픽 분산, A/B 테스트나 Canary 배포
- Latency-based: 사용자와 가장 가까운 리전으로 연결
- Geolocation: 국가·지역별 맞춤 라우팅
- Failover: 장애 발생 시 대체 리소스로 자동 전환

### 4. 상태 체크 및 장애 복구

- 지정한 웹 서버나 리소스의 정상 작동 여부 자동 모니터링
- 비정상 탐지 시 자동으로 대체 리소스로 트래픽 라우팅
- 실시간 장애 복구(Active-Passive, Active-Active 구성)

### 5. 통합 AWS 서비스 연동

- Amazon S3, CloudFront, Elastic Load Balancer(ELB), API Gateway 등과 원클릭 통합
- VPC 내부 프라이빗 DNS 관리(Private Hosted Zone)
- 로드 밸런싱, CDN, 서버리스 인프라와 연동 최적화

## 사용 사례

### 1. 글로벌 웹사이트 트래픽 분산
- 전 세계 사용자를 지리적으로 가까운 리전의 서버로 연결
- Latency-based 또는 Geolocation 기반 라우팅 활용

### 2. 장애 복구 및 고가용성 아키텍처
- 메인 DC 장애 시 자동으로 백업 DC/클라우드로 트래픽 우회
- 24/365 연속적 서비스 보장

### 3. SaaS 멀티테넌트 서비스
- 각 고객사별로 커스텀 도메인 연결 쉬움
- 도메인 소유 인증/검증(TXT 레코드) 자동화

### 4. 이메일·API 엔드포인트 관리
- MX/TXT 레코드로 이메일 시스템 연결
- API Gateway, Lambda와의 연동 주소 관리

## Route 53의 장점

- 완전 관리형, 무제한 확장성
- 글로벌 자동화된 인프라(초저지연 DNS 응답)
- 실시간 모니터링·장애 복구 기능 내장
- AWS 생태계 서비스들과의 진정한 통합

## 비용 체계

- Hosted Zone(도메인) 당 월별 기본 요금
- 쿼리 응답 수(조회 트래픽) 기반 추가 요금
- 상태 체크 개수 및 빈도에 따라 과금
- Alias 레코드 등 AWS 리소스 연결은 대부분 무료

## 주의점 및 활용 팁

- TTL(Time To Live) 값 조절로 DNS 갱신 속도/트래픽 제어
- Private Hosted Zone으로 보안성 강화(내부 도메인 할당)
- Route 53 Resolver 기능 활용 시 온프레미스-클라우드 통합 네트워킹 최적화

**AWS Route 53은 도메인 관리, 트래픽 라우팅, 서비스 상태 체크를 아우르는 글로벌 DNS 인프라 솔루션으로, AWS 환경뿐만 아니라 외부 시스템과의 다양한 융합도 자유롭게 실현할 수 있는 핵심 서비스**다. 글로벌 웹 사이트 운영, 복잡한 하이브리드 클라우드, 마이크로서비스의 DNS 관리까지 모두 손쉽고 강력하게 처리할 수 있다.
