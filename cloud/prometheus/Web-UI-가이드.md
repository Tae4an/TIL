# Prometheus 웹 UI 가이드
Prometheus는 웹 인터페이스를 통해 **메트릭 데이터, 수집 대상, 설정, 쿼리 결과 등**을 직접 확인하고 모니터링할 수 있는 강력한 도구를 제공한다. 아래는 **실무에서 반드시 알아야 할 주요 기능과 활용법**이다.

---

## 🔹 1. **Targets** (`/targets`)

> 메트릭 수집 대상(타겟) 상태 확인
> 
- **위치**: 상단 메뉴 → `Status` → `Targets`
- **주소**: `http://localhost:9090/targets`

### ✅ 확인 가능 항목

- 현재 Prometheus가 수집 중인 서비스 목록
- 각 타겟의 상태 (`UP`, `DOWN`)
- 마지막 스크랩 시간, 지연 시간, 에러 메시지

### 💡 실무 활용

- 특정 서비스에서 **메트릭 수집 실패 여부 즉시 확인**
- `DOWN` 상태 탐지 → 서비스 비정상, exporter 문제 여부 확인

---

## 🔹 2. **Graph** (`/graph`)

> 메트릭 쿼리 및 시각화
> 
- **위치**: 상단 메뉴 → `Graph`
- **주소**: 기본 메인 화면 (`http://localhost:9090/graph`)

### ✅ 기능

- PromQL 쿼리 입력 가능
- 실시간 또는 과거 메트릭 시계열 그래프 확인
- 데이터 표(table) 보기 전환 가능

### 💡 실무 활용

- 주요 메트릭 조회: `up`, `http_requests_total`, `cpu_usage_seconds_total` 등
- 예시 쿼리:
    
    ```
    sum(rate(http_requests_total[5m])) by (job)
    increase(node_cpu_seconds_total[1h])
    
    ```
    

---

## 🔹 3. **Metrics** (`/metrics`)

> Prometheus 서버 자체 메트릭 확인
> 
- **위치**: `Status` → `Metrics`
- **주소**: `http://localhost:9090/metrics`

### ✅ 내용

- Prometheus 내부 지표
    
    (스크랩 횟수, 처리 시간, 쿼리 실행 통계, 메모리 사용량 등)
    

### 💡 실무 활용

- Prometheus 서버 자체 모니터링에 활용
- **알림 설정 대상 자체도 모니터링 가능**

---

## 🔹 4. 기타 Status 메뉴

| 메뉴 | 기능 |
| --- | --- |
| **Configuration** | 현재 로드된 설정 파일(prometheus.yml) 내용 |
| **Rules** | 설정된 알림 룰(Recording Rule, Alerting Rule) 상태 확인 |
| **Service Discovery** | 동적으로 발견된 대상 목록 및 상태 확인 |
| **TSDB Status** | 시계열 데이터베이스 내부 상태 (메모리 사용량, 청크 수 등) |

---

## ✅ 실무에서 반드시 확인해야 할 포인트

| 위치 | 점검 항목 |
| --- | --- |
| `/targets` | 서비스 수집 상태 (UP/DOWN), 스크랩 성공 여부 |
| `/graph` | 주요 메트릭 쿼리 및 시각화 (트래픽, 에러율 등) |
| PromQL | `rate()`, `sum()`, `increase()` 등으로 메트릭 분석 |
| `/rules` | 알림 룰 동작 여부 확인 |
| `/metrics` | Prometheus 자체 성능과 이상 여부 탐지 |

---

## 🔄 예시 사용 흐름

1. *`/targets`*에서 수집 대상 상태 확인
2. *`/graph`*에서 PromQL 쿼리로 주요 메트릭 확인
3. *`/rules`*에서 알림 설정 상태 검토
4. *`/metrics`*에서 Prometheus 서버 자체 상태 진단

---

## 📌 보충 팁: PromQL 실전 예시

| 목적 | 예시 쿼리 |
| --- | --- |
| 모든 서비스 상태 확인 | `up` |
| 에러 비율 확인 | `rate(http_requests_total{status=~"5.."}[5m])` |
| CPU 사용량 | `rate(node_cpu_seconds_total{mode!="idle"}[1m])` |
| 평균 응답 시간 | `avg_over_time(http_request_duration_seconds[5m])` |

---

## 📝 요약

| 기능 | 주소 | 주요 활용 |
| --- | --- | --- |
| **Targets** | `/targets` | 수집 대상 상태 확인 |
| **Graph** | `/graph` | 메트릭 쿼리/시각화 |
| **Metrics** | `/metrics` | Prometheus 자체 메트릭 확인 |
| **Configuration / Rules** | `/status` 하위 메뉴 | 설정 및 알림 룰 점검 |

---

## ✅ 결론

Prometheus의 웹 UI는 단순한 모니터링 도구를 넘어 **서비스 운영의 핵심 진단 도구**다.

**메트릭 수집 상태, 쿼리 기반 분석, 내부 지표 확인, 알림 룰 점검까지 통합적으로 제공**되며,

**실시간 대응과 지속적인 성능 개선을 위한 기반 역할**을 한다.