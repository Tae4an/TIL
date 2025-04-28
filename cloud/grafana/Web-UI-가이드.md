# Grafana Web UI 가이드
**Grafana**는 다양한 메트릭 데이터(Prometheus 등)를 실시간으로 시각화하고, 모니터링 대시보드를 구축할 수 있는 오픈소스 도구이다.

---

## 🔹 1. 접속 및 로그인

- **주소**: `http://localhost:3000/`
- **기본 계정**:
    - ID: `admin`
    - PW: `admin`
- 최초 로그인 후 비밀번호 변경 필요

---

## 🔹 2. 데이터 소스 연결 (최우선 단계)

1. 왼쪽 메뉴에서 **“Connections” → “Data Sources”** 클릭
2. Prometheus, Loki, MySQL 등 원하는 데이터 소스 선택
3. 주소 및 설정 입력 (예: Prometheus는 `http://localhost:9090`)
4. 하단의 **“Save & test”** 버튼으로 연결 테스트

> ✔️ 중요: 대시보드를 만들기 전에 반드시 데이터 소스를 연결해야 한다.
>

---

## 🔹 3. 대시보드 생성 및 패널 구성

### 📌 대시보드 생성

1. 왼쪽 메뉴 → `Dashboards` → `+ New` → `New Dashboard` 클릭
2. “+ Add visualization” 클릭 → 패널 생성 화면 진입

### 📌 패널 설정

- **쿼리 작성**: PromQL, SQL 등 데이터 소스에 맞는 쿼리 작성
- **시각화 유형 선택**: Time series, Gauge, Stat, Bar chart 등
- **설정 옵션**:
    - 단위(Unit): %, ms, req/sec 등
    - 색상/범위: 임계값 설정 가능
    - 제목/라벨/범례 설정 가능

### 📌 예시 쿼리 (Prometheus)

```
rate(http_requests_total[5m])
sum(rate(node_cpu_seconds_total{mode!="idle"}[1m])) by (instance)

```

---

## 🔹 4. 대시보드 UI 기능 및 활용 팁

| 기능 | 설명 |
| --- | --- |
| **Time picker** | 조회할 시간 범위 선택 (1h, 24h, 7d 등) |
| **Refresh 설정** | 자동 새로고침 간격 설정 가능 |
| **Panel 이동/크기 조정** | 드래그 & 리사이즈로 자유롭게 레이아웃 구성 |
| **템플릿 변수 (Variables)** | 서버, 네임스페이스 등 조건을 동적으로 변경 가능 |
| **Dashboard sharing** | 링크, 이미지, iframe 공유 가능 |
| **Export/Import** | JSON 파일로 내보내기/가져오기 가능 |

> 팁: 자주 쓰는 변수(예: $instance, $job)를 설정하면 하나의 대시보드로 여러 상황에 대응 가능함
>

---

## 🔹 5. 알림(Alerting) 설정

- 패널 우측 상단 → “Edit” → `Alert` 탭 진입
- **조건 설정**:
    - 예: `avg() > 80` → 경고 발생
- **채널 연동**:
    - Slack, Email, Webhook 등
- **Grafana Alerting vs Legacy Alerting**:
    - 최신 버전에서는 **Unified Alerting 시스템** 권장

> 실시간 대응 체계를 구성할 때 매우 유용
>

---

## 🔹 6. 고급 기능 (Optional)

### 🎛️ 템플릿 변수 사용

```sql
label_values(node_cpu_seconds_total, instance)

```

→ 대시보드 상단에서 서버 인스턴스를 드롭다운으로 선택 가능

### 🔄 패널 반복 (Repeat by variable)

- 템플릿 변수로 조건 설정 후 여러 개의 패널을 동적으로 반복 생성 가능
- 예: 서버별 CPU 사용량을 하나의 대시보드에서 반복 표시

### 📦 플러그인 설치 (Plugins)

- Worldmap Panel, Pie Chart, Heatmap 등 외부 시각화 플러그인 활용
- `Configuration` → `Plugins`에서 설치

---

## 🔹 7. 실전 예시 흐름

> Prometheus와 연동한 실시간 시스템 모니터링 대시보드 구축 예시
>
1. **Prometheus 데이터 소스 연결**
2. `New Dashboard` 생성 → `+ Add Panel`
3. 쿼리: `sum(rate(http_requests_total[5m])) by (job)`
4. 시각화: `Time series` 선택, 단위: `req/sec`, 임계값 설정
5. 변수 추가: `$job`, `$instance` 등 → 대시보드 유연성 확보
6. 자동 새로고침: `30s`, 전체화면(Kiosk mode) 설정
7. 알림 설정: 5분 평균 요청이 500을 넘으면 Slack 알림 전송

---

## 🔚 요약 정리

| 항목 | 설명 |
| --- | --- |
| 접속 주소 | `http://localhost:3000/` |
| 데이터 소스 연결 | Prometheus 등과 연동 필수 |
| 대시보드 생성 | 패널 추가, 쿼리 입력, 시각화 선택 |
| 주요 기능 | 변수, 알림, 공유, 자동 새로고침, 패널 반복 등 |
| 실무 팁 | 변수 활용, 경고 설정, 반복 패널로 유연한 모니터링 구성 |

---

## 📌 추천 활용 사례

- 서버 자원(CPU, 메모리, 디스크 I/O 등) 모니터링
- 컨테이너/Pod 상태 시각화 (Kubernetes 연동 시)
- 사용자 요청 수, 에러율, 지연 시간 추이 확인
- AI 모델 배포 시 지표(Accuracy, Latency 등) 모니터링

---