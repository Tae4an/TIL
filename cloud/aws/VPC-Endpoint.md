# AWS VPC 엔드포인트란?

## 개념 정의
AWS VPC 엔드포인트(VPC Endpoint)는 AWS에서 제공하는 완전 관리형 프라이빗 연결 서비스다. VPC 내부 리소스가 인터넷 게이트웨이, NAT 디바이스, VPN, Direct Connect 없이도 AWS 서비스 또는 다른 VPC의 서비스에 직접적으로 안전하게 연결할 수 있다.

간단히 말해 “AWS 내부 전용 고속도로”로, 인터넷을 거치지 않고 AWS 서비스 간에 프라이빗하고 빠른 통신을 제공하는 가상 네트워킹 구성 요소다.

## 핵심 특징

**프라이빗 네트워크 통신**
- 인터넷 우회: 모든 트래픽이 AWS 백본 네트워크 내에서만 전송되어 외부 인터넷 노출이 없음
- AWS PrivateLink 기반: 고가용성과 확장성을 보장하는 PrivateLink 기술 사용
- 네트워크 격리: 트래픽이 VPC 외부로 나가지 않고 AWS 내부에서 직접 처리

**강화된 보안**
- 공격 표면 축소: 인터넷 경로 제거로 외부 위협 차단
- 세밀한 접근 제어: IAM 정책, 보안 그룹, 리소스 정책 등 다층 보안
- 데이터 유출 방지: 민감한 데이터가 AWS 네트워크를 벗어나지 않음

**비용 최적화**
- 데이터 전송비 절약: NAT 게이트웨이나 인터넷 게이트웨이 없이 연결
- 선택적 과금: Gateway 엔드포인트는 무료, Interface 엔드포인트는 시간당 및 데이터 처리량 기반 과금
- 효율적 라우팅: AWS 내부 최적 경로로 네트워크 성능 향상

**고가용성 및 확장성**
- 수평 확장 및 다중 AZ 지원, 무제한 대역폭

## 주요 구성 요소

**Interface 엔드포인트 (PrivateLink)**
- ENI(Elastic Network Interface)로 VPC에 사설 IP 할당
- 대부분의 AWS 서비스 지원
- 프라이빗 DNS 이름 제공 및 보안 그룹 설정 가능

**Gateway 엔드포인트**
- VPC 라우팅 테이블에 엔드포인트 경로 추가
- Amazon S3, DynamoDB 지원
- 추가 비용 없이 무료 제공
- 리소스 정책을 통한 세밀한 접근 제어 가능

**Gateway Load Balancer 엔드포인트**
- 방화벽, IDS/IPS 같은 보안 어플라이언스 통합에 사용
- 네트워크를 투명하게 검사 및 트래픽 분산

**엔드포인트 서비스**
- 자체 서비스 또는 타사 서비스의 VPC 엔드포인트화 가능
- 다른 AWS 계정에도 서비스 공개 가능
- 사용자 정의 DNS 이름 지원

## 전통적 인터넷 통신 vs VPC 엔드포인트

| 특징     | VPC 엔드포인트      | 전통적 인터넷 통신   |
|----------|--------------------|---------------------|
| 경로     | AWS 내부 백본      | 퍼블릭 인터넷 경유   |
| 보안     | 완전 프라이빗 통신 | 외부 위협 노출 가능  |
| 성능     | 내부 경로 최적화    | 인터넷 지연 및 병목  |
| 비용     | 전송비 절약        | NAT/IGW 데이터 비용  |
| 설정     | VPC 내부 구성      | IGW, NAT, 라우팅 필요|
| 가용성   | SLA 제공           | 인터넷 품질 의존     |

## 주요 기능

- 서비스별 맞춤 연결: S3에는 Gateway 엔드포인트, EC2/Lambda/CloudWatch 등 대부분의 서비스에는 Interface 엔드포인트 지원
- DNS 및 네이밍: 프라이빗 DNS 이름 자동 생성, 기존 코드 수정 없이 서비스 접근 가능
- 정책 기반 제어: 엔드포인트 및 리소스 정책, 조건부 접근, CloudTrail 감사 추적 지원

## 주요 활용 사례

- 하이브리드 클라우드 환경에서 온프레미스에서 AWS 서비스로 안전하게 연결
- 마이크로서비스 아키텍처에서 서비스 간 프라이빗 API 호출
- 데이터 파이프라인의 보안 전송 및 네트워크 비용 최적화
- 금융/의료 등 규정 준수, 데이터 주권이 필요한 환경의 보안 강화

## 기본 워크플로

### 1. Gateway 엔드포인트 생성 (S3 예시)
```bash
aws ec2 create-vpc-endpoint \
    --vpc-id vpc-12345678 \
    --service-name com.amazonaws.us-west-2.s3 \
    --vpc-endpoint-type Gateway \
    --route-table-ids rtb-12345678
```

### 2. Interface 엔드포인트 생성 (EC2 예시)
```bash
aws ec2 create-vpc-endpoint \
    --vpc-id vpc-12345678 \
    --service-name com.amazonaws.us-west-2.ec2 \
    --vpc-endpoint-type Interface \
    --subnet-ids subnet-12345678 \
    --security-group-ids sg-12345678
```

### 3. 정책 설정 예시
```json
{
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::my-secure-bucket/*",
            "Condition": {
                "StringEquals": {
                    "aws:PrincipalVpc": "vpc-12345678"
                }
            }
        }
    ]
}
```

### 4. 애플리케이션 연동 예시 (Python)
```python
import boto3

s3_client = boto3.client('s3', region_name='us-west-2')
response = s3_client.list_objects_v2(Bucket='my-secure-bucket')
```

## 비용 구조

- Gateway 엔드포인트(S3, DynamoDB): 무료
- Interface 엔드포인트: 시간당 요금(엔드포인트, AZ 별) + GB당 데이터 처리 요금
- Gateway Load Balancer 엔드포인트: 시간당, 데이터 처리량 요금, 별도 GWLB 요금

## 보안 모범 사례

- 전용 서브넷과 보안 그룹으로 트래픽 격리 및 최소 권한 설정
- 엔드포인트 및 리소스 정책으로 세밀한 접근 제어
- VPC Flow Logs, CloudTrail, CloudWatch로 트래픽 및 접근 추적
- 프라이빗 DNS 활성화, DNS 검증 및 도메인 정책 적용

VPC 엔드포인트는 현대적 클라우드 네트워킹의 핵심으로, 제로 트러스트 환경과 완전한 프라이빗 통신을 실현하며 AWS 서비스의 보안성과 효율성을 극대화할 수 있는 필수 서비스다.
