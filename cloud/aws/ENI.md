# AWS ENI란?

## 개념 정의
ENI(Elastic Network Interface, 탄력적 네트워크 인터페이스)는 VPC 내에서 가상 네트워크 카드를 나타내는 논리적 네트워킹 구성 요소다. EC2 인스턴스가 네트워크에 연결되어 통신할 수 있도록 하는 가상 랜카드로, IP 주소와 MAC 주소를 가지며 보안 그룹, 트래픽 라우팅, 네트워크 접근 제어를 담당한다.

쉽게 말해 "EC2를 네트워크에 연결하는 가상 랜카드로, IP 주소를 부여받아 AWS 서비스·인터넷·온프레미스와 통신하게 해주는 논리적 네트워크 인터페이스"다.

## 핵심 특징

**가상 네트워크 카드**
- 물리 서버의 네트워크 인터페이스 카드(NIC)와 동일한 기능
- EC2 인스턴스와 네트워크 간 데이터 송수신 담당
- 서브넷에 배치되어 VPC 네트워크에 연결

**IP 및 MAC 주소 보유**
- 하나 이상의 Private IPv4 주소 (Primary + Secondary)
- Public IPv4 주소 (선택 사항)
- Elastic IP 주소 연결 가능
- IPv6 주소 (선택 사항)
- 고유 MAC 주소

**인스턴스 독립성**
- ENI는 EC2 인스턴스와 독립적으로 생성·관리·삭제 가능
- 인스턴스에서 분리 후 다른 인스턴스에 재연결 가능
- 인스턴스 종료되어도 ENI는 유지 (기본 ENI 제외)

**가용 영역 바인딩**
- ENI는 생성 시 지정한 특정 가용 영역(AZ)에 영구적으로 바인딩
- 같은 AZ 내의 인스턴스 간에만 이동 가능
- 다른 AZ 인스턴스에는 연결 불가

**즉시 연결·분리**
- 실행 중인 인스턴스에 ENI를 즉시(On the fly) 연결
- 다운타임 없이 분리·재연결 가능
- 장애 조치(Failover) 시나리오에 활용

**보안 그룹 적용**
- ENI마다 독립적인 보안 그룹 연결
- 인바운드·아웃바운드 트래픽 제어
- 여러 보안 그룹 동시 적용 가능

## ENI 구성 요소 및 속성

### 필수 속성

**Primary Private IPv4 주소**
- ENI가 생성될 때 자동 또는 수동으로 할당되는 주된 사설 IP
- 서브넷의 CIDR 범위 내에서 선택
- ENI 삭제 전까지 변경 불가

**MAC 주소**
- ENI의 고유 하드웨어 주소
- EC2 인스턴스 OS에서 인식하는 네트워크 카드 식별자
- 변경 불가

**서브넷**
- ENI가 위치하는 서브넷
- 특정 가용 영역의 특정 서브넷에 바인딩
- ENI 생성 후 변경 불가

**보안 그룹**
- ENI에 적용되는 가상 방화벽
- 최대 5개까지 연결 가능
- 동적으로 변경 가능

### 선택적 속성

**Secondary Private IPv4 주소**
- ENI에 추가로 할당 가능한 사설 IP (여러 개 가능)
- 단일 인스턴스에서 여러 웹사이트 호스팅 시 유용
- 필요 시 추가·제거 가능

**Elastic IP 주소**
- ENI의 Private IP와 연결되는 고정 Public IP
- 인스턴스 재시작해도 유지
- ENI 분리 후 다른 ENI로 재연결 가능

**Public IPv4 주소**
- 인스턴스 시작 시 자동 할당되는 동적 Public IP
- 인스턴스 중지·재시작 시 변경됨
- Elastic IP와 달리 비영구적

**IPv6 주소**
- IPv6 CIDR 블록이 활성화된 VPC/서브넷에서 사용
- 여러 IPv6 주소 할당 가능

**Source/Destination Check**
- 기본적으로 활성화 (Enabled)
- 비활성화 시 ENI가 송신·수신하는 패킷의 출발지/목적지가 자신이 아니어도 허용
- NAT 인스턴스, 라우터, 방화벽 구축 시 비활성화 필요

**설명(Description)**
- ENI를 식별하기 위한 사용자 정의 텍스트

## ENI 유형

### 1. Primary ENI (기본 네트워크 인터페이스)

**특징**
- EC2 인스턴스 생성 시 자동으로 함께 생성 (eth0)
- 인스턴스와 생명 주기가 동일 (인스턴스 삭제 시 함께 삭제)
- 인스턴스에서 분리 불가
- 서브넷 변경 불가

**역할**
- 인스턴스의 기본 네트워크 연결
- 기본 Private IP 주소 제공
- 기본 라우팅 경로

### 2. Secondary ENI (추가 네트워크 인터페이스)

**특징**
- 사용자가 수동으로 생성 (eth1, eth2, ...)
- 인스턴스와 독립적으로 존재
- 인스턴스에서 분리·재연결 가능
- 인스턴스 삭제되어도 유지

**역할**
- 다중 네트워크 경로 (Multi-homing)
- 장애 조치(Failover)
- 네트워크 트래픽 분리 (관리·데이터·백업 네트워크)
- 라이선스 바인딩 (MAC 주소 기반)

## 주요 활용 사례

### 1. 장애 조치 (Failover)

**시나리오**
- Primary EC2 인스턴스에 Secondary ENI 연결
- Standby EC2 인스턴스 준비
- Primary 인스턴스 장애 시 ENI를 Standby 인스턴스로 이동
- IP 주소·MAC 주소가 유지되어 투명한 전환

**예시**
```
Primary 인스턴스 (i-1234) + ENI (eni-abc, IP: 10.0.1.10)
  → 장애 발생
  → ENI 분리
  → Standby 인스턴스 (i-5678)에 ENI 연결
  → 서비스 복구 (같은 IP 10.0.1.10 유지)
```

### 2. 다중 IP 주소 (Multiple IP Addresses)

**시나리오**
- 단일 인스턴스에서 여러 웹사이트·서비스 호스팅
- 각 서비스마다 고유 IP 주소 필요

**예시**
```
ENI: eni-123
  - Primary IP: 10.0.1.10 (웹사이트 A)
  - Secondary IP: 10.0.1.11 (웹사이트 B)
  - Secondary IP: 10.0.1.12 (웹사이트 C)
```

### 3. 네트워크 세그멘테이션 (다중 홈)

**시나리오**
- 관리 네트워크와 데이터 네트워크 분리
- 보안 강화 (트래픽 격리)

**예시**
```
Primary ENI (eth0): 10.0.1.10 → Public Subnet (인터넷 접근)
Secondary ENI (eth1): 10.0.2.10 → Private Subnet (DB 접근)
```

**장점**
- 관리 트래픽과 애플리케이션 트래픽 분리
- 각 ENI에 다른 보안 그룹 적용
- 네트워크 성능 최적화

### 4. 라이선스 관리 (MAC 주소 바인딩)

**시나리오**
- MAC 주소 기반 라이선스를 사용하는 소프트웨어
- 인스턴스 교체 시에도 라이선스 유지 필요

**방법**
- ENI를 별도로 생성 (고유 MAC 주소 부여)
- 라이선스를 ENI의 MAC 주소에 바인딩
- 인스턴스 교체 시 ENI를 새 인스턴스로 이동
- 라이선스 재발급 불필요

### 5. 보안 어플라이언스 (NAT, 방화벽)

**시나리오**
- NAT 인스턴스, 방화벽, 침입 탐지 시스템(IDS) 구축
- Source/Destination Check 비활성화 필요

**예시**
```
Public Subnet ENI → NAT 인스턴스 → Private Subnet ENI
  - Source/Destination Check: Disabled
  - 다른 인스턴스의 트래픽을 중계
```

## ENI 생성 및 관리 방법

### AWS 콘솔에서 ENI 생성

```
1. EC2 콘솔 → 왼쪽 메뉴 "네트워크 인터페이스" 클릭
2. "네트워크 인터페이스 생성" 클릭
3. 설정 입력:
   - 설명: DemoENI
   - 서브넷: subnet-abc123 (특정 AZ 선택)
   - Private IPv4 주소: 자동 또는 수동 (예: 10.0.1.50)
   - 보안 그룹: sg-xyz789
4. "생성" 클릭
```

### ENI를 EC2 인스턴스에 연결

```
1. 생성된 ENI 선택
2. "작업" → "연결" 클릭
3. 인스턴스 ID 선택 (같은 AZ 내)
4. "연결" 클릭
5. EC2 인스턴스에서 자동으로 인식 (eth1, eth2 등)
```

### ENI 분리

```
1. ENI 선택
2. "작업" → "분리" 클릭
3. 강제 분리 옵션 (필요 시)
4. "분리" 클릭
```

### AWS CLI로 ENI 생성

```bash
# ENI 생성
aws ec2 create-network-interface \
  --subnet-id subnet-abc123 \
  --description "Secondary ENI for failover" \
  --groups sg-xyz789 \
  --private-ip-address 10.0.1.50

# 인스턴스에 ENI 연결
aws ec2 attach-network-interface \
  --network-interface-id eni-12345678 \
  --instance-id i-1234567890abcdef0 \
  --device-index 1

# ENI 분리
aws ec2 detach-network-interface \
  --attachment-id eni-attach-abc123
```

### Elastic IP 연결

```bash
# Elastic IP 할당
aws ec2 allocate-address --domain vpc

# ENI의 Private IP와 연결
aws ec2 associate-address \
  --allocation-id eipalloc-abc123 \
  --network-interface-id eni-12345678 \
  --private-ip-address 10.0.1.50
```

## ENI 제약 사항

**가용 영역 제한**
- ENI는 특정 AZ에 바인딩되어 다른 AZ 인스턴스에 연결 불가
- AZ 간 이동 필요 시 새 ENI 생성 후 IP 재할당

**인스턴스 유형별 ENI 수 제한**
- 인스턴스 유형에 따라 연결 가능한 ENI 개수 제한
- 예: t2.micro (최대 2개), t3.large (최대 3개), m5.xlarge (최대 4개)
- 각 ENI당 IP 주소 개수도 제한

**Primary ENI 제거 불가**
- 기본 ENI는 인스턴스 실행 중 분리 불가
- 인스턴스 삭제 시에만 함께 삭제

**서브넷 변경 불가**
- ENI 생성 후 서브넷 변경 불가
- 다른 서브넷 필요 시 새 ENI 생성

## 모범 사례

**명확한 네이밍 및 태그**
- ENI에 설명과 태그를 명확히 추가
- 예: "prod-web-failover-eni", "Environment: Production"
- 관리·추적 용이

**장애 조치 자동화**
- CloudWatch + Lambda로 인스턴스 장애 감지 시 ENI 자동 이동
- 다운타임 최소화

**보안 그룹 최소 권한**
- ENI별로 필요한 최소 트래픽만 허용
- 네트워크 세그멘테이션으로 공격 표면 축소

**Elastic IP 활용**
- 고정 IP 필요 시 Elastic IP를 ENI에 연결
- 인스턴스 교체해도 IP 유지

**Source/Destination Check 관리**
- NAT·방화벽 구축 시에만 비활성화
- 일반 애플리케이션은 활성화 유지 (보안)

**ENI 정리**
- 사용하지 않는 ENI 정기적으로 삭제
- 비용 절감 (ENI 자체는 무료지만 Elastic IP 과금)

## ENI vs Elastic IP vs Public IP

| 항목 | ENI | Elastic IP | Public IP |
|------|-----|-----------|----------|
| 정의 | 가상 네트워크 카드 | 고정 Public IP | 동적 Public IP |
| 생명 주기 | 독립적 (Secondary) | 독립적 | 인스턴스 종속 |
| 재할당 | 다른 인스턴스로 이동 가능 | 다른 인스턴스로 재연결 가능 | 불가능 (재시작 시 변경) |
| 비용 | 무료 | 연결 안 하면 과금 | 무료 |
| 용도 | 네트워크 연결 | 고정 IP 필요 시 | 임시 인터넷 접근 |

## 비용

**ENI 자체**
- ENI 생성·보유는 무료
- 연결된 인스턴스 실행 시에만 인스턴스 비용 발생

**Elastic IP**
- ENI에 연결된 Elastic IP가 사용 중이면 무료
- 할당만 하고 연결 안 하면 시간당 과금 (약 $0.005/hour)

**데이터 전송**
- ENI를 통한 데이터 전송은 일반 EC2 데이터 전송 비용 동일

## 요약

AWS ENI(Elastic Network Interface)는 VPC 내에서 EC2 인스턴스를 네트워크에 연결하는 가상 랜카드로, IP 주소·MAC 주소·보안 그룹을 보유하며 인스턴스와 독립적으로 생성·관리·이동 가능하다. 장애 조치, 다중 IP 주소, 네트워크 세그멘테이션, 라이선스 관리, 보안 어플라이언스 구축 등 다양한 시나리오에 활용되며, 가용 영역에 바인딩되어 동일 AZ 내 인스턴스 간에만 이동 가능하다. Primary ENI는 인스턴스와 생명 주기가 같지만, Secondary ENI는 독립적으로 존재해 유연한 네트워크 아키텍처를 구현할 수 있다.
