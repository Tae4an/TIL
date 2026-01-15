# VIF(Virtual Interface)란?

## 개념 정의
VIF(Virtual Interface, 가상 인터페이스)는 가상화 환경이나 네트워크 서비스에서 물리적 네트워크 인터페이스를 소프트웨어적으로 추상화한 논리적 네트워크 인터페이스로, 가상 머신(VM)이나 컨테이너가 네트워크에 연결되거나 클라우드 서비스에서 전용선을 논리적으로 분할하여 사용할 때 생성된다.

쉽게 말해 "물리적 네트워크 카드 없이 소프트웨어로 만든 가상의 네트워크 인터페이스로, VM이나 클라우드 서비스에서 네트워크 연결을 위해 사용한다."

## 주요 사용 사례

### 1. 가상 머신 네트워킹 (Xen, KVM, VMware)

**개념**
가상화 플랫폼(Xen, KVM, VMware)에서 VM이 네트워크에 연결되기 위해 생성되는 가상 네트워크 인터페이스다.

**작동 방식**
- VM당 하나 이상의 VIF 생성
- VIF는 가상 스위치(vSwitch)에 연결
- 가상 스위치가 물리 네트워크 인터페이스(PIF)와 연결
- VM → VIF → vSwitch → PIF → 물리 네트워크

**특징**
- VLAN 태깅(802.1q) 지원
- Xen 기반 시스템은 기본적으로 VM당 최대 7개 VIF 지원
- 각 VIF는 고유 MAC 주소 보유
- TUN/TAP 디바이스로 구현

### 2. AWS Direct Connect 가상 인터페이스

**개념**
AWS Direct Connect 물리 회선 위에 생성되는 논리적 네트워크 인터페이스로, 하나의 전용선을 여러 용도로 논리적으로 분할하여 사용한다.

**VIF 유형**

#### Private VIF (프라이빗 가상 인터페이스)
- VPC의 프라이빗 리소스 접근
- Virtual Private Gateway(VGW) 또는 Transit Gateway(TGW) 연결
- 프라이빗 IP 주소 사용
- BGP를 통한 라우팅

#### Public VIF (퍼블릭 가상 인터페이스)
- AWS 퍼블릭 서비스 접근 (S3, DynamoDB 등)
- 공인 IP 주소 필수
- 고객 소유 공인 IP 또는 AWS 제공 IP 사용

#### Transit VIF (트랜짓 가상 인터페이스)
- Direct Connect Gateway를 통해 Transit Gateway 연결
- 여러 리전·VPC 동시 연결
- 확장성 높음

**구성 요소**
- VLAN ID: 802.1q 태깅용 VLAN 번호
- BGP ASN: Border Gateway Protocol 자율 시스템 번호
- Peer IP: BGP 피어링용 /30 또는 /31 CIDR

### 3. 컨테이너 네트워킹

**개념**
컨테이너(Docker, Kubernetes)가 네트워크에 연결되기 위한 가상 인터페이스다.

**예시**
- Docker: veth pair (virtual ethernet pair)
- Kubernetes: Pod 네트워크 인터페이스

### 4. VPN/터널링

**개념**
TUN 디바이스를 사용하여 L3(IP) 패킷을 터널링하는 가상 인터페이스다.

**사용 예시**
- Telepresence: 로컬 워크스테이션과 Kubernetes 클러스터 간 네트워크 투명성 제공
- VPN 클라이언트: OpenVPN, WireGuard 등

## VIF vs PIF

| 항목 | VIF (Virtual Interface) | PIF (Physical Interface) |
|------|------------------------|-------------------------|
| 정의 | 소프트웨어 가상 인터페이스 | 실제 물리 네트워크 카드 |
| 위치 | VM, 컨테이너, 게스트 OS | 호스트, 물리 서버 |
| 개수 | VM당 여러 개 가능 | 하드웨어에 따라 제한 |
| MAC 주소 | 소프트웨어로 생성 | 하드웨어에 내장 |

## AWS Direct Connect VIF 설정 예시

### Private VIF 생성
1. Direct Connect 콘솔에서 "가상 인터페이스 생성" 선택
2. 유형: Private 선택
3. VLAN ID 입력 (예: 100)
4. BGP ASN 입력 (예: 65000)
5. Virtual Private Gateway 또는 Transit Gateway 선택
6. Peer IP 설정 (AWS 자동 할당 또는 수동 지정)

### 라우팅
- BGP 세션 수립
- 온프레미스 라우터 ↔ AWS 라우터
- 동적 라우팅 테이블 교환

## VIF 기술적 구현

### TUN/TAP 디바이스
- **TUN**: Layer 3 (IP 패킷) 가상 인터페이스
- **TAP**: Layer 2 (Ethernet 프레임) 가상 인터페이스

**차이점**
- TUN: IP 패킷만 처리 (VPN, 터널링)
- TAP: 모든 Ethernet 프레임 처리 (브릿지, VM 네트워킹)

### veth pair
컨테이너 네트워킹에서 사용되는 가상 이더넷 쌍으로, 한쪽은 컨테이너 내부, 다른 쪽은 호스트 또는 브릿지에 연결된다.

## 제한 사항

### Xen/XCP-ng
- 기본적으로 VM당 최대 7개 VIF 지원
- CLI로 추가 가능하나 일부 게스트 OS에서 인식 문제 발생 가능
- VLAN 태깅 시 MTU를 4바이트 증가 필요 (1504바이트)

### AWS Direct Connect
- Private VIF: 1개당 1개 VGW 또는 Direct Connect Gateway 연결
- VLAN ID 중복 불가
- BGP 세션 설정 필수

## 요약
VIF(Virtual Interface)는 가상화 환경·클라우드 서비스에서 물리적 네트워크 카드 없이 소프트웨어로 만든 논리적 네트워크 인터페이스로, VM 네트워킹(Xen, KVM, VMware)에서 VM을 가상 스위치에 연결하거나, AWS Direct Connect에서 물리 전용선을 논리적으로 분할(Private VIF, Public VIF, Transit VIF)하여 VPC·퍼블릭 서비스·Transit Gateway 접근에 사용되며, VLAN ID·BGP ASN·Peer IP 설정이 필요하고, TUN/TAP·veth pair로 구현되며, Xen은 VM당 기본 최대 7개 VIF를 지원한다.
