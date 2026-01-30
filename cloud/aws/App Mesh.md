# AWS App Mesh란?

## 개념 정의

AWS App Mesh는 마이크로서비스 간 네트워크 트래픽을 관리하고 모니터링하는 서비스 메시로, 각 서비스 옆에 Envoy 프록시를 사이드카로 배포하여 통신을 중재하고, 애플리케이션 코드 수정 없이 트래픽 라우팅, 재시도, 타임아웃, 보안을 중앙에서 제어한다. ECS, EKS, Fargate, EC2의 모든 컴퓨팅 환경을 지원하며, 오픈소스 Envoy 프록시 기반으로 작동한다.

쉽게 말해 "마이크로서비스 사이에 투명한 프록시를 넣어서 트래픽을 자동으로 관리하고, 장애를 감지하며, 통신을 보호하는 네트워크 레이어"다.

## 핵심 기능

- Envoy 프록시 기반 사이드카 패턴
- 애플리케이션 코드 수정 없는 트래픽 제어
- 자동 서비스 디스커버리
- 동적 트래픽 라우팅 (카나리, A/B 테스트)
- 재시도, 타임아웃, 서킷 브레이커 자동 처리
- mTLS 기반 서비스 간 인증
- CloudWatch, X-Ray 통합 모니터링

## 주요 구성 요소

**Mesh**
- 서비스들의 네트워크 트래픽 논리적 경계
- 모든 서비스의 최상위 컨테이너
- 단일 애플리케이션의 전체 마이크로서비스를 포함

**Virtual Service**
- 실제 서비스의 추상화 레이어
- DNS 이름으로 참조 (예: api.example.com)
- Virtual Router 또는 Virtual Node로 라우팅

**Virtual Node**
- 실제 서비스 인스턴스의 논리적 표현
- ECS Task, Kubernetes Pod, EC2 인스턴스를 가리킴
- Service Discovery (AWS Cloud Map, DNS) 설정
- 백엔드 서비스 및 Health Check 정의

**Virtual Router**
- 트래픽 라우팅 규칙 정의
- Route를 통해 조건별 라우팅 수행
- 가중치 기반 트래픽 분배

**Route**
- 구체적인 라우팅 규칙 (HTTP 헤더, 경로, 메서드 기반)
- 여러 Virtual Node로 트래픽 분배
- 가중치 설정으로 카나리 배포 지원

**Virtual Gateway**
- 외부 트래픽을 Mesh로 유입시키는 진입점
- Ingress Gateway 역할
- 퍼블릭 ALB/NLB와 연결

**Envoy Proxy**
- 각 서비스의 사이드카 컨테이너
- App Mesh에서 자동 설정 수신
- 실제 트래픽 프록시 및 메트릭 수집

## 작동 방식

1. **Mesh 생성**: 애플리케이션의 네트워크 경계 정의
2. **Virtual Service 정의**: 서비스의 DNS 이름 추상화
3. **Virtual Node 생성**: 실제 서비스 인스턴스 등록 (ECS Task, Pod)
4. **Virtual Router 및 Route 설정**: 라우팅 규칙 정의
5. **Envoy 사이드카 배포**: Task/Pod에 Envoy 컨테이너 추가
6. **자동 설정 주입**: App Mesh가 Envoy에 라우팅 설정 전달
7. **트래픽 프록시**: 모든 통신이 Envoy를 거쳐 처리
8. **메트릭 수집**: CloudWatch, X-Ray로 자동 전송

## 트래픽 라우팅 패턴

### 카나리 배포
Virtual Router로 90% 트래픽을 v1으로, 10%를 v2로 라우팅하며, 점진적으로 비율을 조정한다.

### A/B 테스트
HTTP 헤더 또는 쿼리 파라미터 기반으로 특정 사용자 그룹을 신규 버전으로 라우팅한다.

### Blue/Green 배포
Route 가중치를 즉시 100% 전환하여 무중단 배포를 수행한다.

### 재시도 및 타임아웃
자동 재시도 정책과 타임아웃을 설정하여 일시적 장애를 처리한다.

### 서킷 브레이커
연속 실패 시 자동으로 서비스 차단하여 캐스케이드 장애를 방지한다.

## 주요 활용 사례

**마이크로서비스 통신 관리**
수십~수백 개의 마이크로서비스 간 통신을 중앙에서 제어하며, 각 팀이 독립적으로 서비스를 배포하면서도 일관된 네트워크 정책을 적용한다.

**무중단 배포**
카나리 배포로 신규 버전을 점진적으로 배포하며, 문제 발생 시 즉시 롤백하고, A/B 테스트로 사용자 반응을 확인한다.

**장애 격리**
서킷 브레이커로 장애 서비스를 자동 차단하며, 재시도 정책으로 일시적 장애를 흡수하고, 타임아웃으로 무한 대기를 방지한다.

**보안 강화**
mTLS로 서비스 간 통신을 암호화하며, ACM Private CA 또는 자체 CA로 인증서를 발급하고, 서비스 간 인증 및 권한 부여를 자동화한다.

**멀티 클러스터 통합**
여러 EKS 클러스터, ECS 클러스터, EC2 인스턴스를 단일 Mesh로 통합하며, 하이브리드 환경에서 일관된 통신 정책을 적용한다.

**관찰성 향상**
X-Ray로 분산 추적을 자동 수집하며, CloudWatch로 트래픽 메트릭을 모니터링하고, Envoy 메트릭으로 세밀한 성능 분석을 수행한다.

## 통합 서비스

- Amazon ECS, EKS, Fargate, EC2: 실행 환경
- AWS Cloud Map: 서비스 디스커버리
- Amazon CloudWatch: 메트릭 모니터링
- AWS X-Ray: 분산 추적
- ACM Private CA: mTLS 인증서 발급
- AWS IAM: 접근 제어
- Amazon Route 53: DNS 해석

## App Mesh vs Istio

| 항목 | App Mesh | Istio |
|------|----------|-------|
| 관리 | AWS 완전관리형 | 자체 설치 및 관리 |
| 컨트롤 플레인 | AWS 관리 (무료) | 클러스터 내 실행 (리소스 소비) |
| 프록시 | Envoy | Envoy |
| 플랫폼 | ECS, EKS, Fargate, EC2 | Kubernetes 전용 |
| 학습 곡선 | 낮음 | 높음 |
| 확장성 | AWS 인프라 활용 | 클러스터 리소스 제한 |
| 비용 | 데이터 전송 비용만 | 컨트롤 플레인 리소스 비용 |

## 설정 예시

### EKS에서 App Mesh 배포

```yaml
# Virtual Node 정의
apiVersion: appmesh.k8s.aws/v1beta2
kind: VirtualNode
metadata:
  name: product-v1
spec:
  podSelector:
    matchLabels:
      app: product
      version: v1
  listeners:
    - portMapping:
        port: 8080
        protocol: http
  serviceDiscovery:
    awsCloudMap:
      namespaceName: product.local
      serviceName: product

# Virtual Router 정의
apiVersion: appmesh.k8s.aws/v1beta2
kind: VirtualRouter
metadata:
  name: product-router
spec:
  listeners:
    - portMapping:
        port: 8080
        protocol: http
  routes:
    - name: product-route
      httpRoute:
        match:
          prefix: /
        action:
          weightedTargets:
            - virtualNodeRef:
                name: product-v1
              weight: 90
            - virtualNodeRef:
                name: product-v2
              weight: 10
```

## 모범 사례

**Mesh 설계**
애플리케이션별로 별도 Mesh를 생성하며, 환경(dev, staging, prod)별로 분리하고, 명명 규칙을 표준화한다.

**Virtual Service 추상화**
실제 서비스 이름 대신 Virtual Service 이름을 사용하며, 버전 변경 시 애플리케이션 코드 수정 없이 라우팅만 변경한다.

**Health Check 설정**
Virtual Node에 Health Check를 정의하여 비정상 엔드포인트를 자동 제거하며, 적절한 간격과 임계값을 설정한다.

**재시도 정책**
일시적 네트워크 오류를 흡수하도록 재시도를 설정하며, 최대 재시도 횟수와 백오프를 적절히 조정한다.

**타임아웃 설정**
무한 대기를 방지하도록 요청 타임아웃을 설정하며, 서비스 특성에 맞게 조정한다.

**mTLS 활성화**
민감한 데이터를 다루는 서비스 간 통신에 mTLS를 적용하며, 인증서 자동 갱신을 설정한다.

**메트릭 모니터링**
CloudWatch 대시보드로 트래픽, 지연시간, 오류율을 추적하며, 알람을 설정하여 이상 징후를 감지한다.

**점진적 배포**
신규 버전은 5-10% 트래픽으로 시작하여 점진적으로 증가시키며, 문제 발생 시 즉시 롤백한다.

## 제약 사항

**Envoy 오버헤드**
사이드카 컨테이너가 CPU와 메모리를 추가 소비하며, 일반적으로 컨테이너당 256MB 이상 필요하다.

**학습 곡선**
Virtual Service, Virtual Node, Virtual Router 개념 이해가 필요하며, 초기 설정이 복잡할 수 있다.

**지원 프로토콜**
HTTP/1.1, HTTP/2, gRPC, TCP를 지원하며, UDP는 지원하지 않는다.

**리전 제한**
일부 리전에서만 사용 가능하며, 글로벌 서비스가 아니다.

## 비용

App Mesh 자체는 무료이지만, 다음 비용이 발생한다.
- Envoy 사이드카 컨테이너 실행 비용 (ECS/Fargate vCPU/메모리)
- 데이터 전송 비용 (VPC 내 무료, AZ 간/리전 간 유료)
- CloudWatch Logs 및 메트릭 비용
- X-Ray 추적 비용

일반적으로 중소규모 애플리케이션은 월 $100~$300 추가 발생한다.

## 요약

AWS App Mesh는 마이크로서비스 간 네트워크 트래픽을 관리하는 서비스 메시로, Envoy 프록시를 사이드카로 배포하여 통신을 중재하며, Virtual Service, Virtual Node, Virtual Router로 추상화하여 애플리케이션 코드 수정 없이 트래픽을 제어하고, 카나리 배포, 재시도, 타임아웃, 서킷 브레이커를 자동 처리하며, mTLS로 서비스 간 인증을 제공하고, CloudWatch 및 X-Ray와 통합하여 관찰성을 향상시키며, ECS, EKS, Fargate, EC2의 모든 컴퓨팅 환경을 지원하고, Istio와 달리 AWS 완전관리형으로 운영 부담이 낮다.
