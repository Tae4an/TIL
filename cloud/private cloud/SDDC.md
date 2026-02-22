# SDDC(Software Defined Data Center)란?

## 개념 정의

SDDC(Software Defined Data Center, 소프트웨어 정의 데이터센터)는 데이터센터의 **컴퓨팅·스토리지·네트워크 등 모든 인프라 구성 요소를 소프트웨어로 추상화·가상화**하여, 하드웨어 종속 없이 소프트웨어와 API, 자동화로 전체 인프라를 프로비저닝·관리·운영하는 데이터센터 아키텍처 패러다임이다.

쉽게 말해, "하드웨어를 직접 만지지 않고 소프트웨어로 서버·스토리지·네트워크 전부를 코드처럼 정의하고 자동화로 운영하는 데이터센터"다.

## 전통 데이터센터 vs SDDC

| 항목 | 전통 데이터센터 | SDDC |
|------|----------------|------|
| 인프라 구성 | 하드웨어 직접 구성 | 소프트웨어로 추상화·자동화 |
| 프로비저닝 속도 | 수주~수개월 | 수분 내 자동 완료 |
| 확장성 | 물리 서버 추가 필요 | 소프트웨어로 즉시 확장 |
| 운영 방식 | 수동 관리, 사일로 구조 | 중앙 통합 관리, 정책 기반 자동화 |
| 유연성 | 낮음 (하드웨어 종속) | 높음 (하드웨어 독립) |
| 비용 구조 | 고정 CAPEX 중심 | 효율적 리소스 활용, OPEX 전환 가능 |

## 핵심 구성 요소

### SDC (Software Defined Compute, 소프트웨어 정의 컴퓨팅)
- 물리 CPU·메모리를 가상 머신(VM)·컨테이너로 추상화
- 하이퍼바이저(VMware ESXi, KVM, Hyper-V)가 핵심 기술
- 중앙 관리 플랫폼으로 VM 동적 프로비저닝·이동·확장 자동화
- Kubernetes 기반 컨테이너 오케스트레이션도 SDC의 일환

### SDS (Software Defined Storage, 소프트웨어 정의 스토리지)
- 물리 스토리지 장치를 논리적 리소스 풀로 추상화
- 여러 서버의 로컬 디스크를 묶어 공유 스토리지 구성
- 중복 제거·압축으로 용량 효율 향상, 정책 기반 데이터 배치
- 대표 솔루션: VMware vSAN, Nutanix, NetApp ONTAP, Ceph

### SDN (Software Defined Networking, 소프트웨어 정의 네트워킹)
- 물리 네트워크 장비(스위치, 라우터)를 소프트웨어로 추상화
- 제어 플레인(Control Plane)과 데이터 플레인(Data Plane)을 분리하여 중앙 소프트웨어가 네트워크 경로·정책 제어
- 마이크로세그멘테이션으로 VM 간 트래픽을 세밀하게 격리·보호
- 대표 솔루션: VMware NSX, Cisco ACI, OpenStack Neutron

### 자동화·오케스트레이션 레이어
- SDC·SDS·SDN을 통합 관리하는 중앙 소프트웨어 계층
- 정책 기반으로 리소스 자동 검색·할당·재구성
- IaC(Infrastructure as Code)와 연계하여 인프라를 코드로 정의·배포
- 대표 솔루션: VMware vCenter, OpenStack, HashiCorp Terraform

## SDDC의 작동 원리

1. **추상화(Abstraction)**: 물리 하드웨어를 소프트웨어 계층으로 추상화하여 논리 리소스로 표현
2. **풀링(Pooling)**: 추상화된 리소스를 하나의 공유 풀로 통합 관리
3. **자동화(Automation)**: 정책·API 기반으로 리소스 프로비저닝·구성·변경 자동 수행
4. **오케스트레이션(Orchestration)**: 컴퓨팅·스토리지·네트워크를 통합 조율하여 워크로드 요구에 동적 대응

## 주요 SDDC 솔루션

### VMware Cloud Foundation (VCF)
- VMware의 대표 풀스택 SDDC 플랫폼
- vSphere(SDC) + vSAN(SDS) + NSX(SDN) + vCenter(오케스트레이션)를 하나의 패키지로 제공
- 온프레미스 또는 퍼블릭 클라우드(VMware Cloud on AWS)에 배포 가능

### OpenStack
- 오픈소스 기반 SDDC 플랫폼
- Nova(컴퓨팅), Cinder(스토리지), Neutron(네트워킹), Keystone(인증)으로 구성
- 통신사, 대형 기업, 공공기관에서 프라이빗 클라우드 구축에 활용

### Nutanix
- 하이퍼컨버지드 인프라(HCI) 기반 SDDC 솔루션
- 컴퓨팅·스토리지·네트워크를 하나의 단일 플랫폼에 통합
- 설치·확장이 용이하고, VMware 대안으로 주목받고 있음

## SDDC와 클라우드의 관계

SDDC는 **플랫폼**이고, 클라우드는 **서비스 제공 방식**이다.

- SDDC는 클라우드 서비스(IaaS, PaaS, SaaS)를 제공하기 위한 인프라 기반 기술
- 퍼블릭 클라우드(AWS, Azure, GCP) 내부도 SDDC 원칙으로 구축된 대규모 인프라
- 온프레미스 SDDC + 퍼블릭 클라우드 = 하이브리드 클라우드 구현의 핵심

## AWS와 SDDC

### VMware Cloud on AWS
- VMware SDDC(vSphere + vSAN + NSX)를 AWS 베어메탈 전용 인프라 위에서 실행
- 온프레미스 VMware 워크로드를 코드 변경 없이 AWS로 이전 가능
- AWS 서비스(S3, RDS, DynamoDB 등)와 직접 연동 가능
- 재해 복구(DR) 세컨더리 사이트, 데이터센터 확장, 클라우드 마이그레이션에 활용

### VMware Cloud on AWS Outposts
- AWS Outposts 하드웨어 위에 VMware SDDC를 배포하는 완전관리형 서비스
- 온프레미스 데이터센터에서 VMware 환경을 유지하면서 AWS 서비스 활용
- 데이터 레지던시 요구사항, 낮은 지연 시간이 필요한 워크로드에 적합

## 정리

SDDC는 데이터센터의 컴퓨팅(SDC)·스토리지(SDS)·네트워크(SDN)를 소프트웨어로 추상화·자동화하여 하드웨어 종속에서 벗어나 정책 기반으로 전체 인프라를 프로비저닝·운영하는 아키텍처 패러다임으로, VMware VCF·OpenStack·Nutanix가 대표 솔루션이며, 퍼블릭 클라우드 내부도 SDDC 원칙으로 구축된 것이고, 온프레미스 SDDC와 퍼블릭 클라우드를 연결한 것이 하이브리드 클라우드이며, VMware Cloud on AWS로 기존 SDDC 환경을 그대로 AWS 위에 올려 운영하거나 점진적으로 클라우드 네이티브로 전환할 수 있다.
