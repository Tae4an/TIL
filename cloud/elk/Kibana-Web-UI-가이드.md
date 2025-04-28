# Kibana 웹 UI 가이드
**ELK Stack**은 로그 수집과 분석을 위한 오픈소스 통합 플랫폼으로,

- **Elasticsearch**: 로그 저장 및 검색
- **Logstash/Filebeat**: 로그 수집 및 전처리
- **Kibana**: 시각화 및 분석용 웹 UI

이 중 **Kibana**가 실질적인 사용자 인터페이스 역할을 하며, 운영/분석의 중심이다.

---

## 🔹 1. Kibana 웹 UI 접속

- 기본 접속 주소: `http://localhost:5601/`
- Kibana는 Elasticsearch와 연결되어야 정상 동작
- 웹 UI는 다음 기능을 제공:
    - 데이터 탐색, 시각화, 대시보드, 알림, 쿼리 콘솔 등

---

## 🔹 2. Kibana 주요 메뉴별 기능 및 활용법

| 메뉴 | 설명 | 활용 예시 |
| --- | --- | --- |
| **Discover** | 저장된 로그 및 데이터를 검색/필터링 | 에러 로그 조회, 특정 서비스 로그 추적 |
| **Visualize** (또는 **Visualizations**) | 차트, 그래프 형태로 데이터 시각화 | 시간별 로그 분포, 상태 코드 비율 |
| **Dashboards** | 시각화 패널을 조합하여 대시보드 구성 | 실시간 운영 모니터링 화면 |
| **Index Patterns** | Kibana가 사용할 데이터 인덱스 등록 | `logstash-*`, `filebeat-*` 등 |
| **Dev Tools** | Elasticsearch 쿼리 콘솔 | 복잡한 검색/집계/인덱스 관리 |
| **Alerting & Monitoring** | 이상 탐지 시 알림 설정 | 에러 급증, 서비스 다운 감지 |

---

## 🔹 3. 대시보드 생성 흐름 (실전 플로우)

1. **인덱스 패턴 생성**

   → `Management` → `Index Patterns` → `logstash-*` 등 등록

2. **Discover**

   → 데이터 확인, 쿼리 조건 설정

3. **Visualizations**

   → 막대 그래프, 타임라인 등 시각화 구성

4. **Dashboards**

   → 여러 시각화를 조합해 대시보드 생성

5. **Alerting** (선택)

   → 조건 기반 자동 알림 설정


---

## 🔹 4. ELK 전체 연동 흐름

```mermaid
flowchart LR
    A[애플리케이션/서버] --> B[Filebeat / Logstash]
    B --> C[Elasticsearch]
    C --> D[Kibana]
    D --> E[시각화 / 대시보드 / 검색 / 알림]

```

- **Logstash/Filebeat**: 로그 수집 및 구조화
- **Elasticsearch**: 검색 가능한 형태로 저장
- **Kibana**: UI를 통해 통합된 분석/모니터링 제공

---

## 🔹 5. 실무에서 주의할 핵심 포인트

| 항목 | 내용 |
| --- | --- |
| **인덱스 패턴 등록** | Kibana가 데이터를 인식할 수 있도록 반드시 등록 필요 |
| **Discover 필터** | 쿼리 조건, 기간 설정 등 정확한 필터링 중요 |
| **시각화 템플릿 재사용** | 시각화 구성 요소 재활용으로 대시보드 효율화 |
| **로그 표준화** | 모든 로그를 `JSON + 공통 필드(trace-id, service-name)`로 통일 |
| **접근 권한 관리** | 팀별로 Kibana 접근 권한 분리 권장 |

---

## 🔹 6. Kibana vs Grafana 비교 요약

| 항목 | Kibana | Grafana |
| --- | --- | --- |
| 주 목적 | 로그 분석 및 검색 | 메트릭 시각화 중심 |
| 데이터 소스 | Elasticsearch 전용 (또는 Beats, Logstash 등 연동) | Prometheus, Elasticsearch, Jaeger 등 다양한 지원 |
| 검색 쿼리 | Lucene / KQL | PromQL, SQL 등 |
| 시각화 | 로그 기반 패널에 특화 | 메트릭/시계열에 특화 |
| 통합 가능성 | ELK 내 통합 | 관측계 통합에 강점 |

---

## 🔚 요약

- **Kibana**는 ELK Stack의 UI 중심으로, **로그 검색, 시각화, 대시보드 구성, 알림 설정**을 제공
- 실무에서는 `Discover`, `Dashboards`, `Dev Tools`, `Alerting` 메뉴를 중심으로 **모니터링·운영 분석**을 수행
- Prometheus + Grafana가 메트릭 중심이라면, ELK + Kibana는 **로그 중심 관측 솔루션**
- Kibana와 Grafana를 **동시에 구성하면 로그와 메트릭을 함께 분석하는 강력한 통합 관측 환경**을 구축할 수 있다.