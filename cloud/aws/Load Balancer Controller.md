# AWS Load Balancer Controller란?

## 개념 정의

AWS Load Balancer Controller는 EKS 클러스터에서 Kubernetes Ingress와 Service 리소스를 감지하여 AWS Elastic Load Balancer(ALB/NLB)를 자동으로 프로비저닝하고 관리하는 컨트롤러다. 이전 ALB Ingress Controller를 확장한 것으로, Ingress로 ALB를 생성하고 Service로 NLB를 생성한다.

쉽게 말해 "쿠버네티스에서 Ingress/Service를 만들면 자동으로 AWS 로드밸런서를 생성해주는 도구"다.

## 핵심 기능

- Kubernetes Ingress → ALB 자동 프로비저닝
- Kubernetes Service → NLB 자동 프로비저닝
- 여러 Ingress가 단일 ALB 공유 (비용 절감)
- Pod IP 직접 타겟팅 (IP 모드)
- TargetGroupBinding 커스텀 리소스 지원
- Annotation 기반 세밀한 설정

## 주요 구성 요소

**Controller Pod**
EKS 클러스터 내 Deployment로 실행되며, Kubernetes API Server를 Watch하고, Ingress/Service 이벤트를 감지한다.

**IAM Role**
IRSA(IAM Roles for Service Accounts)로 Pod에 IAM 권한을 부여하며, ELB, EC2, Target Group 생성/수정/삭제 권한을 포함한다.

**IngressClass**
Ingress 리소스가 어떤 컨트롤러를 사용할지 지정하며, alb IngressClass를 지정한다.

**TargetGroupBinding (CRD)**
기존 Target Group을 Pod와 연결하는 커스텀 리소스로, 외부에서 관리하는 LB와 EKS를 통합한다.

## 작동 방식

### Ingress → ALB 생성 흐름

1. 사용자가 Ingress 리소스 생성 (IngressClass: alb)
2. Controller가 Kubernetes API Server에서 이벤트 감지
3. Controller가 AWS API로 ALB 생성
4. Path별로 리스너 규칙 생성
5. Ingress의 각 Service에 대해 Target Group 생성
6. Target Group에 Pod IP 또는 Node 등록
7. Security Group 자동 생성 및 연결
8. ALB DNS를 Ingress Status에 기록

### Service → NLB 생성 흐름

1. Service type: LoadBalancer 생성 (annotation 추가)
2. Controller가 Service 이벤트 감지
3. AWS API로 NLB 생성
4. Target Group 생성 및 Pod/Node 등록
5. NLB DNS를 Service Status에 기록

### 타겟팅 모드

**Instance 모드 (기본)**
- Target Group에 Worker Node를 등록
- Traffic: ALB → NodePort → Pod
- 모든 노드에 보안 그룹 규칙 추가

**IP 모드**
- Target Group에 Pod IP 직접 등록
- Traffic: ALB → Pod (직접)
- 지연시간 감소, 확장성 향상
- Fargate Pod 지원

## 설정 예시

### ALB Ingress 생성

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
  annotations:
    alb.ingress.kubernetes.io/scheme: internet-facing
    alb.ingress.kubernetes.io/target-type: ip
    alb.ingress.kubernetes.io/listen-ports: '[{"HTTP": 80}, {"HTTPS": 443}]'
spec:
  ingressClassName: alb
  rules:
  - host: example.com
    http:
      paths:
      - path: /app1
        pathType: Prefix
        backend:
          service:
            name: service1
            port:
              number: 80
```

### NLB Service 생성

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-nlb
  annotations:
    service.beta.kubernetes.io/aws-load-balancer-type: "external"
    service.beta.kubernetes.io/aws-load-balancer-nlb-target-type: "ip"
    service.beta.kubernetes.io/aws-load-balancer-scheme: internet-facing
spec:
  type: LoadBalancer
  selector:
    app: my-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
```

## 주요 Annotation

### ALB Ingress Annotation

- `alb.ingress.kubernetes.io/scheme`: internet-facing / internal
- `alb.ingress.kubernetes.io/target-type`: instance / ip
- `alb.ingress.kubernetes.io/certificate-arn`: HTTPS 인증서 ARN
- `alb.ingress.kubernetes.io/listen-ports`: 리스너 포트 설정
- `alb.ingress.kubernetes.io/security-groups`: 보안 그룹 지정
- `alb.ingress.kubernetes.io/subnets`: 서브넷 지정
- `alb.ingress.kubernetes.io/wafv2-acl-arn`: WAF 연결

### NLB Service Annotation

- `service.beta.kubernetes.io/aws-load-balancer-type`: external / internal
- `service.beta.kubernetes.io/aws-load-balancer-nlb-target-type`: instance / ip
- `service.beta.kubernetes.io/aws-load-balancer-scheme`: internet-facing / internal
- `service.beta.kubernetes.io/aws-load-balancer-cross-zone-load-balancing-enabled`: true / false

## 주요 활용 사례

**마이크로서비스 트래픽 라우팅**
단일 ALB에서 Path 기반 라우팅으로 여러 서비스를 노출하며, /api → API Service, /web → Web Service로 분기한다.

**HTTPS 종료**
ACM 인증서를 Annotation으로 지정하여 ALB에서 TLS 종료를 수행하며, Pod는 HTTP로만 통신한다.

**Fargate Pod 노출**
IP 타겟 모드로 Fargate Pod를 직접 타겟팅하며, Node가 없는 서버리스 환경에서 작동한다.

**내부 서비스 로드밸런싱**
Internal NLB로 클러스터 내부 서비스 간 통신을 로드밸런싱하며, Private Subnet에 배치한다.

**비용 절감**
여러 Ingress가 단일 ALB를 공유하여 ALB 개수를 줄이고, Group 기능으로 리소스를 통합한다.

## 통합 서비스

- Amazon EKS: 실행 환경
- Elastic Load Balancing: ALB/NLB 생성
- AWS Certificate Manager: HTTPS 인증서
- AWS WAF: 보안 규칙 적용
- AWS Shield: DDoS 방어
- Amazon VPC: 네트워크 배치
- AWS IAM: 권한 관리
- Amazon CloudWatch: 모니터링

## ALB Ingress Controller vs Load Balancer Controller

| 항목 | ALB Ingress Controller (v1) | Load Balancer Controller (v2) |
|------|------------------------------|-------------------------------|
| 지원 LB | ALB만 | ALB + NLB |
| 이름 | ALB Ingress Controller | AWS Load Balancer Controller |
| ALB 공유 | 제한적 | 여러 Ingress 공유 지원 |
| IP 타겟 | 제한적 | 완전 지원 |
| Fargate | 제한적 | 완전 지원 |
| TargetGroupBinding | 없음 | 지원 |
| 상태 | Deprecated | 현재 권장 |

## 설치 방법 요약

1. **IAM Policy 생성**: Controller에 필요한 권한 정의
2. **IAM Role 생성**: IRSA로 ServiceAccount와 연결
3. **Helm 설치**: helm repo add 후 install
4. **Controller Pod 확인**: kubectl get deployment -n kube-system
5. **IngressClass 생성**: alb IngressClass 정의
6. **Ingress/Service 생성**: Annotation으로 ALB/NLB 프로비저닝

## 모범 사례

**IRSA 사용**
IAM Role을 ServiceAccount에 연결하여 Pod에 최소 권한을 부여하며, 노드 레벨 권한을 피한다.

**IP 타겟 모드**
지연시간 감소와 확장성 향상을 위해 IP 타겟 모드를 사용하며, Fargate에서는 필수다.

**ALB 공유**
Group Annotation으로 여러 Ingress를 단일 ALB에 통합하여 비용을 절감한다.

**보안 그룹 관리**
자동 생성되는 보안 그룹을 검토하고, 필요시 수동 지정하며, 최소 권한 원칙을 적용한다.

**Health Check 설정**
Pod의 Readiness Probe와 ALB Health Check 경로를 일치시켜 정확한 상태 확인을 수행한다.

**WAF 통합**
퍼블릭 ALB에는 WAF를 연결하여 보안을 강화하며, Annotation으로 ACL ARN을 지정한다.

**로깅 및 모니터링**
ALB Access Log를 S3에 저장하고, CloudWatch로 메트릭을 추적하며, X-Ray로 트레이싱한다.

## 제약 사항

**EKS 전용**
EKS 클러스터에서만 작동하며, 일반 Kubernetes는 지원하지 않는다.

**IMDSv2 Hop Limit**
IRSA 사용 시 Worker Node의 IMDSv2 Hop Limit을 2 이상으로 설정해야 한다.

**Fargate IP 모드 필수**
Fargate Pod는 IP 타겟 모드만 지원하며, Instance 모드는 불가능하다.

**보안 그룹 복잡성**
Instance 모드에서 모든 노드와 LB 간 통신을 허용해야 하므로, 보안 그룹 규칙이 복잡해진다.

## 비용

Controller 자체는 무료이며, Pod 실행 비용만 발생한다. 생성되는 ALB/NLB는 시간당 과금되며, ALB는 시간당 약 $0.025 + LCU 비용, NLB는 시간당 약 $0.0225 + LCU 비용이다.

## 요약

AWS Load Balancer Controller는 EKS 클러스터에서 Kubernetes Ingress로 ALB를 자동 프로비저닝하고 Service로 NLB를 자동 생성하는 컨트롤러로, 이전 ALB Ingress Controller를 확장했으며, IRSA로 IAM 권한을 받아 ELB API를 호출하고, IP 타겟 모드로 Pod를 직접 타겟팅하여 지연시간을 감소시키며, 여러 Ingress가 단일 ALB를 공유하여 비용을 절감하고, Fargate Pod 지원과 TargetGroupBinding CRD를 제공하며, Annotation으로 세밀한 설정이 가능하고, HTTPS 종료, WAF 통합, Path 기반 라우팅에 최적화되어 있다.
