# Azure CLI란?

## 개념 정의
Azure CLI(Azure Command-Line Interface)는 Microsoft Azure에서 제공하는 **크로스 플랫폼 명령줄 관리 도구**다. 터미널/쉘 환경에서 Azure의 모든 리소스(가상 머신, 스토리지, 네트워크, 앱 서비스 등)를 손쉽게 생성, 관리, 스크립팅할 수 있도록 설계됐다.

쉽게 말하면 “명령어로 Azure 리소스를 자동화·제어하는 클라우드 관리 툴”로, GUI 포털 대신 빠르고 일관된 운영, 대규모 자동화, DevOps CI/CD에 최적화된 인프라 관리 방법이다.

## 핵심 특징

**크로스 플랫폼 지원**
- Windows, MacOS, Linux 등 모든 OS에서 설치·사용 가능
- bash, PowerShell, zsh 등 주요 쉘 환경 지원

**REST API 기반 통합**
- Azure REST API와 직접 연동, 최신 클라우드 기능을 신속하게 CLI 명령으로 지원
- 포털에서 가능한 모든 작업을 명령어/스크립트로 처리 가능

**자동화 및 DevOps 친화**
- 파이프라인, 스크립트, IaC(Infrastructure as Code) 자동화에 활용
- Azure Resource Manager(ARM) 템플릿 배포, 관리도 명령 하나로 처리

**사용자 친화적 명령 구조**
- 직관적이고 일관성 있는 명령어 체계(az group, az vm, az storage 등)
- 명령어 옵션 학습 쉽고, 자동 완성 및 도움말 제공
- JSON/YAML 포맷 결과 반환 지원, 다른 도구와 연동 쉬움

**확장·커뮤니티 지원**
- 커스텀 확장(Extensions) 및 오픈소스 기반 신속한 신규 기능 도입
- Azure 서비스 업데이트와 함께 CLI도 지속적으로 진화, 많은 샘플/가이드 제공

## 주요 구성 요소 및 명령 예시

- **az login**: Azure 계정 인증/로그인
- **az group**: 리소스 그룹 생성·관리
- **az vm**: 가상 머신 생성, 시작, 중지, 삭제, SSH 접속 등
- **az storage**: Blob, File, Disk 등 스토리지 계정 및 컨테이너 관리
- **az network**: VNet, Subnet, Public IP, NSG 등 네트워크 구성 제어
- **az aks**: Kubernetes 클러스터 관리
- **az webapp**: App Service 배포·운영 자동화

## 기존 포털 vs Azure CLI 비교

| 특징          | Azure CLI         | Azure Portal(웹 GUI)      |
|---------------|-------------------|---------------------------|
| 접근 방식     | 터미널, 스크립트  | 브라우저                  |
| 자동화        | 고도 수행 가능    | 반복적 수동 작업          |
| 대규모 관리   | 효율적            | 규모가 크면 비효율        |
| DevOps 연동   | 파이프라인 친화    | 별도 연동 필요            |
| 사용성        | 명령어 기반       | 그래픽 기반               |
| 결과 포맷     | JSON, 표 등 다양  | GUI 표/그래프 형태        |
| 확장성        | Extensions, API   | 제한적                    |

## 주요 활용 사례

- 대규모 리소스 일괄 생성, 삭제, 업데이트, 배포
- DevOps 파이프라인 외부 자동화(예: GitHub Actions, Azure DevOps)
- IaC(ARM/Bicep) 템플릿 파일로 인프라 코드화 및 자동 배포
- 모니터링·통계·성능 데이터 실시간 CLI 수집
- 서버·컨테이너·웹앱 등 개발/테스트 환경 자동 구축

## 기본 워크플로 및 예시

### 1. Azure CLI 설치
- OS별 패키지 매니저(yum, apt, brew 등) 또는 공식 MSI 설치 파일로 설치

### 2. 계정 로그인
```bash
az login
```
- 브라우저 인증 또는 서비스 프린시펄(앱) 인증도 가능

### 3. 리소스 그룹 생성
```bash
az group create --name mygroup --location koreacentral
```

### 4. VM 생성 (최소 옵션)
```bash
az vm create \
  --resource-group mygroup \
  --name myvm \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

### 5. 주요 작업 예시
- 스토리지 계정 생성: `az storage account create ...`
- 웹앱 배포: `az webapp up --name mywebapp --resource-group mygroup`
- 네트워크 구성: `az network vnet create ...`
- 로그/메트릭 조회: `az monitor metrics list ...`

## 비용 구조

- Azure CLI 자체는 무료(오픈소스)
- 사용한 클라우드 리소스에 따른 Azure 표준 요금만 부과

## 보안 모범 사례

- 계정 인증 시 최소 권한 원칙 유지(서비스 프린시펄, role 기반 접근)
- 환경변수/스택 내 중요한 정보 노출 금지
- 인프라 변경 전 결과 파일·로그·버전 관리
- 자동화 파이프라인 보안 검증 및 비밀정보 별도 저장
- CLI 버전 최신 유지 및 보안 업데이트 적용

Azure CLI는 클라우드 인프라 자동화와 DevOps 기반 운영에 최적화된 현대적 Azure 관리 툴로, 신속·효율·확장·보안의 모든 요구를 충족하는 핵심 솔루션이다.
