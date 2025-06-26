# 쿠버네티스 실습 가이드 요약

## 1. 개요

- **목적**
    
    쿠버네티스(Kubernetes)의 설치부터 리소스 배포 및 운영까지의 전 과정을 실습을 통해 자연스럽게 익히도록 구성되어 있다.
    
    명령어 사용법, 클러스터 구성 요소, 애플리케이션 배포 방법 등을 실전 예제를 기반으로 학습하게끔 설계되어 있음.
    
- **대상**
    
    쿠버네티스를 처음 접하는 초보자부터, 실습을 통해 기초를 다지고 싶은 중급 사용자까지 폭넓게 활용 가능.
    
- **실습 환경**
    
    Minikube, k3s, Docker Desktop, kind 등 경량화된 로컬 클러스터 환경을 기반으로 한다.
    
    별도의 클라우드 리소스를 요구하지 않기 때문에 빠른 학습이 가능하며, 필요 시 AWS, GCP와 같은 퍼블릭 클라우드 환경으로 확장 가능.
    

---

## 2. 실습 준비

- **필수 도구**
    - `kubectl`: 쿠버네티스 클러스터와 상호작용하는 CLI 도구
    - `Minikube`, `k3s`, `kind`: 로컬에서 클러스터 실행을 위한 툴
    - `Docker`: 컨테이너 이미지 생성 및 실행 도구
    - (선택) `Helm`: 쿠버네티스 애플리케이션 배포를 간편하게 하는 패키지 매니저
- **권장 환경**
    - 운영체제: Windows 10 이상, macOS, Linux
    - RAM: 8GB 이상
    - CPU: 2코어 이상
    - Virtualization: 활성화 필요 (Minikube 실행 시 요구됨)

---

## 3. 실습 단계

### 1단계: Docker 기본 실습

- 간단한 컨테이너 이미지 생성 (`Dockerfile`)
- `docker build`, `docker run`, `docker push` 명령어 실습
- `docker-compose`로 멀티 컨테이너 애플리케이션 구성

### 2단계: 쿠버네티스 기본 실습

- Minikube 설치 및 클러스터 구동
- `kubectl`로 Pod, ReplicaSet, Deployment 생성
- Service, Ingress로 네트워크 구성
- 리소스 상태 확인 및 롤링 업데이트 실습

### 3단계: 애플리케이션 배포 실습

- 컨테이너 이미지 빌드 후 Docker Hub 또는 로컬 레지스트리에 업로드
- Deployment로 애플리케이션 배포
- ConfigMap, Secret으로 환경변수 주입
- PersistentVolume(PV), PersistentVolumeClaim(PVC)으로 데이터 유지 실습

### 4단계: 고급 기능 실습

- Horizontal Pod Autoscaler(HPA)로 자동 스케일링 구성
- Liveness/Readiness/Startup Probe로 헬스 체크 설정
- 쿠버네티스 대시보드 설치 및 활용
- Role-Based Access Control(RBAC)과 인증·인가 실습
- 기본적인 로깅, 모니터링 환경 구성 (예: `kubectl logs`, Prometheus, Grafana 연동)

---

## 4. 실습 구조 예시

| 단계 | 주요 내용 |
| --- | --- |
| 1단계 | Docker 및 Docker Compose 실습 |
| 2단계 | Minikube 설치 및 kubectl 사용법 익히기 |
| 3단계 | Pod, ReplicaSet, Deployment, Service, Ingress 구성 실습 |
| 4단계 | 이미지 빌드 및 배포, ConfigMap/Secret, PV/PVC 적용 |
| 5단계 | HPA, Probe, RBAC, 모니터링, 대시보드 등 고급 기능 실습 |

---

## 5. 추천 자료

- [**subicura의 쿠버네티스 안내서**](https://subicura.com/)
    
    실습 중심의 입문용 가이드. 친절한 해설과 스크린샷 포함.
    
- [**쿠버네티스 공식 튜토리얼**](https://kubernetes.io/docs/tutorials/)
    
    실습형 문서 다수 제공. 단계별 개념 설명에 충실.
    
- [**Play with Kubernetes**](https://labs.play-with-k8s.com/)
    
    웹 브라우저에서 실시간으로 쿠버네티스 클러스터 구성 가능. 설치 없이 실습 가능.
    
- [**Katacoda**](https://www.katacoda.com/)
    
    단계별 시나리오 기반 실습 환경 제공. 중급자에게도 추천.
    

---

## 6. 마무리

쿠버네티스 실습은 단순히 명령어를 외우는 데 그치지 않고, 전체 시스템이 어떻게 연동되고 배포되는지를 파악하는 데 목적이 있다.

Minikube와 kubectl 설치부터 시작하여 Pod 구성, 서비스 배포, 고급 기능까지 단계적으로 학습하는 것이 좋다.

로컬 실습 후에는 클라우드 환경에서의 실제 운영까지 확장하여 경험하는 것이 바람직하다.
