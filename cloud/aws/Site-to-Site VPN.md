# AWS Site-to-Site VPN란?

## 개념 정의
AWS Site-to-Site VPN은 온프레미스 네트워크와 AWS VPC 간을 공용 인터넷을 통해 IPsec으로 암호화된 터널로 안전하게 연결하는 관리형 VPN 서비스로, Virtual Private Gateway(VGW)와 Customer Gateway(CGW) 간에 이중화된 VPN 터널을 자동으로 생성하여 고가용성을 보장한다.

쉽게 말해 "회사 사무실·데이터센터와 AWS VPC를 암호화된 가상 전용선으로 연결해서, 마치 같은 네트워크처럼 안전하게 통신할 수 있게 해주는 서비스"다.

## 핵심 특징

### IPsec 암호화 터널
공용 인터넷을 통해 전송되지만 IPsec 프로토콜로 모든 트래픽을 암호화하여 도청·변조를 방지하고, 자동으로 이중 터널(2개의 VPN 터널)을 생성해 한 터널 장애 시 다른 터널로 자동 전환한다.

### 고가용성
각 VPN 연결은 서로 다른 가용 영역의 2개 VPN 터널로 구성되며, 자동 장애 조치(Failover)를 지원해 99.95% 가용성을 제공한다.

### 관리형 서비스
AWS가 VPN 게이트웨이 인프라를 자동 관리·패치·모니터링하며, 사용자는 온프레미스 측 라우터만 구성하면 된다.

### 저렴한 비용
VPN 연결당 시간당 $0.05 + 데이터 전송 비용으로, Direct Connect보다 훨씬 저렴하다.

## 주요 구성 요소

### 1. Virtual Private Gateway (VGW)
VPC 측 VPN 엔드포인트로, VPC에 연결하여 온프레미스와 VPC 간 게이트웨이 역할을 수행한다.

**생성 절차**
1. VPC 콘솔에서 Virtual Private Gateway 생성
2. VPC에 연결(Attach)
3. 라우팅 테이블에 VGW로의 경로 추가

### 2. Customer Gateway (CGW)
온프레미스 측 VPN 엔드포인트로, 실제 물리 라우터·방화벽·VPN 장비 또는 소프트웨어 VPN(OpenSwan, StrongSwan 등)을 나타낸다.

**필수 정보**
- 온프레미스 라우터의 공인 IP 주소
- BGP ASN(동적 라우팅 사용 시) 또는 Static 라우팅

### 3. Site-to-Site VPN Connection
VGW와 CGW 간의 암호화된 IPsec VPN 터널로, 2개의 터널이 자동 생성되며 각 터널은 서로 다른 AWS 엔드포인트를 사용한다.

**터널 구성**
- Tunnel 1: 온프레미스 라우터 ↔ AWS 엔드포인트 1
- Tunnel 2: 온프레미스 라우터 ↔ AWS 엔드포인트 2

### 4. 라우팅
VPN 연결 후 트래픽이 터널을 통과하도록 라우팅 설정이 필요하다.

**AWS 측 라우팅**
- VPC 라우팅 테이블에 온프레미스 네트워크 대역(예: 10.0.0.0/16)을 대상으로 VGW를 지정
- Route Propagation 활성화 시 BGP로 자동 경로 업데이트

**온프레미스 측 라우팅**
- 라우터에 VPC CIDR(예: 172.31.0.0/16)을 대상으로 VPN 터널 지정
- Static 또는 BGP 동적 라우팅

## 설정 단계

### 1. Virtual Private Gateway 생성 및 연결
- VPC 콘솔 → Virtual Private Gateway 생성 → VPC에 Attach

### 2. Customer Gateway 생성
- 온프레미스 라우터의 공인 IP 주소 입력
- BGP ASN 지정 (또는 Static 선택)

### 3. Site-to-Site VPN Connection 생성
- VGW와 CGW 선택
- 라우팅 옵션: Static(수동) 또는 Dynamic(BGP)
- AWS가 자동으로 2개 터널 생성

### 4. 구성 파일 다운로드
- AWS 콘솔에서 VPN 연결의 구성 파일 다운로드
- 온프레미스 라우터 종류 선택(Cisco, Juniper, Palo Alto, OpenSwan 등)
- 다운로드한 파일에 터널 IP, Pre-Shared Key 등 포함

### 5. 온프레미스 라우터 구성
- 다운로드한 구성 파일을 참고하여 라우터에 IPsec 터널 설정
- Pre-Shared Key, 터널 IP, 암호화 알고리즘 등 입력

### 6. 라우팅 테이블 업데이트
- VPC 라우팅 테이블에 온프레미스 네트워크 경로 추가
- 온프레미스 라우터에 VPC 네트워크 경로 추가

### 7. 터널 상태 확인
- AWS 콘솔에서 터널 상태가 "UP"인지 확인
- ping 테스트로 연결 검증

## 라우팅 옵션

### Static Routing (정적 라우팅)
온프레미스 네트워크 대역을 VPN 연결 생성 시 수동으로 지정하며, 간단하지만 네트워크 변경 시 수동 업데이트가 필요하다.

**적합한 경우**: 네트워크 토폴로지가 단순하고 변경이 적을 때

### Dynamic Routing (BGP)
Border Gateway Protocol로 라우팅 정보를 자동 교환하며, 온프레미스 네트워크 변경 시 자동 업데이트되고 장애 시 자동 경로 전환을 지원한다.

**적합한 경우**: 복잡한 네트워크, 여러 VPN 연결, 고가용성 필요 시

## 주요 활용 사례

### 하이브리드 클라우드 아키텍처
온프레미스 데이터센터와 AWS를 안전하게 연결하여 워크로드를 분산 배치하고, 온프레미스 DB와 AWS 애플리케이션 간 통신을 구현한다.

### 클라우드 마이그레이션
대규모 데이터를 AWS로 마이그레이션할 때 VPN으로 안전한 전송 경로를 확보하고, AWS DMS나 DataSync를 VPN 위에서 실행한다.

### 재해 복구 (DR)
온프레미스 시스템의 백업을 AWS에 저장하거나, AWS를 DR 사이트로 활용해 장애 시 빠르게 전환한다.

### 원격 지사 연결
본사 AWS VPC와 여러 지사를 VPN으로 연결하여 중앙 집중식 리소스 접근을 제공한다.

### 개발·테스트 환경 격리
개발팀이 온프레미스에서 AWS 개발 환경에 안전하게 접근하도록 VPN을 구성한다.

## Site-to-Site VPN vs Direct Connect

| 항목 | Site-to-Site VPN | AWS Direct Connect |
|------|------------------|-------------------|
| 연결 방식 | 공용 인터넷 + IPsec 암호화 | 전용 물리 회선 |
| 대역폭 | 최대 1.25 Gbps | 1 Gbps ~ 100 Gbps |
| 지연 시간 | 가변적 (인터넷 품질 영향) | 일관적·낮은 지연 |
| 비용 | 저렴 (시간당 $0.05) | 비싼 (월 수백~수천 달러) |
| 구축 시간 | 수 분~수 시간 | 수 주~수 개월 |
| 보안 | IPsec 암호화 | 전용 회선 (암호화 옵션) |
| 적합 사례 | 임시·저예산·빠른 구축 | 대용량·안정적·장기 운영 |

**VPN over Direct Connect**: Direct Connect 위에 VPN을 구성해 암호화 추가 가능

## 성능 및 제한 사항

### 대역폭
- VPN 터널당 최대 1.25 Gbps
- 2개 터널 사용 시 이론상 최대 2.5 Gbps (하지만 실제로는 단일 터널 사용이 일반적)

### 지연 시간
- 공용 인터넷 경유로 지연 시간이 가변적
- 지연에 민감한 애플리케이션은 Direct Connect 권장

### VPN 연결 수
- VPC당 기본 VPN 연결 제한 있음 (증가 요청 가능)

## 보안

### 암호화
- IPsec으로 모든 트래픽 암호화
- AES-128, AES-256, SHA-1, SHA-2 등 지원

### 인증
- Pre-Shared Key(PSK)로 양측 인증

### NAT Traversal
- NAT 뒤의 라우터도 VPN 연결 가능

## 비용

### VPN 연결 비용
- 시간당 $0.05 (VPN 연결당)
- 한 달 약 $36

### 데이터 전송 비용
- 아웃바운드 데이터 전송: GB당 $0.09 (리전에 따라 다름)
- 인바운드: 무료

**예시**: 월 1TB 아웃바운드 전송 시
- VPN 연결: $36
- 데이터 전송: 1,000 GB × $0.09 = $90
- 총: $126

## 모범 사례

### 이중화 구성
- 2개 터널 모두 활성화하여 고가용성 확보
- BGP 사용 시 자동 장애 조치

### 모니터링
- CloudWatch로 터널 상태·데이터 전송량 모니터링
- 터널 다운 시 SNS 알림 설정

### 보안 그룹·NACL
- VPN 트래픽 허용 규칙 설정
- 온프레미스 네트워크 대역만 허용

### 대역폭 요구사항 평가
- 대용량·낮은 지연 필요 시 Direct Connect 고려
- VPN + Direct Connect 하이브리드 구성 검토

## 요약
AWS Site-to-Site VPN은 온프레미스 네트워크와 AWS VPC를 공용 인터넷을 통해 IPsec 암호화된 터널로 안전하게 연결하는 관리형 서비스로, Virtual Private Gateway(VPC 측)와 Customer Gateway(온프레미스 측) 간에 이중화된 2개 터널을 자동 생성하여 99.95% 가용성을 제공하며, Static 또는 BGP 동적 라우팅을 지원하고, VPN 연결당 시간당 $0.05로 Direct Connect보다 저렴하며, 하이브리드 클라우드·마이그레이션·재해 복구·원격 지사 연결에 활용되고, 최대 1.25 Gbps 대역폭과 가변적 지연 시간을 가지며, 대용량·안정적 연결이 필요하면 Direct Connect를 권장한다.
