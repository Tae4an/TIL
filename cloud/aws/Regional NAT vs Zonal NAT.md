# AWS Regional NAT vs Zonal NAT

## 1. 개념 요약

Zonal NAT Gateway는 **AZ별로 개별 생성해 관리하는 기존 방식**이고, Regional NAT Gateway는 **2025년 11월 신규 출시된 리전 단위 NAT로 단 1개만 생성해도 모든 AZ에 자동 확장되는 방식**이다.  
쉽게 말해, Zonal은 "AZ마다 NAT 1개씩 직접 관리", Regional은 "AWS가 알아서 전 AZ에 확장해주는 NAT 1개"라고 보면 된다.

***

## 2. 핵심 차이점

- **AZ Affinity(친화성) 관리**
  - Zonal: 고객이 AZ별 라우팅 테이블을 직접 구성해야 AZ 친화성 유지
  - Regional: AWS가 내부적으로 자동 처리, 모든 서브넷이 동일한 NAT ID로 라우팅

- **고가용성**
  - Zonal: 직접 AZ마다 NAT 생성·라우팅 분리 → 고가용성 직접 설계
  - Regional: AWS가 AZ 간 자동 확장·축소·장애 대응 처리 → 기본 내장 HA

- **퍼블릭 서브넷 연결**
  - Zonal: NAT를 반드시 특정 퍼블릭 서브넷에 배치해야 함
  - Regional: IGW처럼 VPC에 직접 연결, 퍼블릭 서브넷 불필요

- **모니터링**
  - Zonal: AZ별 NAT ID가 각각 존재 → CloudWatch에서 개별 모니터링
  - Regional: 단일 NAT ID, CloudWatch의 AZ 차원(Dimension)으로 AZ별 트래픽 구분

***

## 3. 구성 비교

| 항목 | Zonal NAT Gateway | Regional NAT Gateway |
|---|---|---|
| **출시** | 기존 방식 | 2025년 11월 신규 |
| **생성 단위** | AZ별 개별 생성 | 리전 단위 1개 생성 |
| **NAT 개수** | AZ 수만큼 필요 | 1개로 전 AZ 커버 |
| **고가용성** | 직접 구성 필요 | 자동 내장 |
| **라우팅 테이블** | AZ별 개별 구성 | 전 서브넷 동일 NAT ID |
| **퍼블릭 서브넷** | 필수 | 불필요 |
| **운영 복잡도** | 높음 | 낮음 |
| **Private NAT** | 지원 | 미지원 |
| **비용** | AZ당 NAT 비용 | 단일 NAT 비용 |

***

## 4. 언제 무엇을 쓰는가

- **Regional NAT 권장 상황**
  - 일반적인 멀티 AZ 프라이빗 서브넷 + 인터넷 아웃바운드 환경
  - 운영 단순화·자동 고가용성 원할 때
  - 신규 VPC 설계 시

- **Zonal NAT 유지 상황**
  - Private NAT(온프레미스·다른 VPC와 프라이빗 연결)가 필요한 경우
  - AZ별 세밀한 트래픽 제어가 필요한 경우
  - 기존 Zonal 환경을 유지해야 하는 경우

***

## 5. 한 줄 정리

Zonal NAT는 **AZ마다 직접 생성·관리하는 기존 방식**이고, Regional NAT는 **단 1개 생성으로 AWS가 전 AZ 자동 확장·고가용성을 처리해주는 2025년 신규 방식**으로, 대부분의 일반 환경에서는 Regional NAT가 운영 부담이 훨씬 적다.
