# Blackbox Exporter란?

**Blackbox Exporter**는 Prometheus 모니터링 생태계에서 **외부 시점에서 서비스의 가용성과 응답 상태를 탐색(Probe)**하는 도구입니다.  
다른 Exporter들이 내부 메트릭을 수집하는 반면, **Blackbox Exporter는 "사용자 관점의 외부 접근성"을 측정**하는 것이 특징입니다.

---

## 🔹 핵심 특징

| 항목 | 설명 |
|------|------|
| **모니터링 방식** | 내부 상태가 아닌 **외부에서의 접근 시도(블랙박스 테스트)** |
| **역할** | 서비스 가용성 확인, 네트워크 경로 점검, 응답 시간 측정 등 |
| **관점** | 실제 사용자 또는 외부 시스템이 서비스에 접근했을 때의 상태를 측정 |
| **설정 방법** | Prometheus에서 `module`, `target`을 정의하여 외부로부터 Probe 요청 수행 |

---

## 🔹 주요 기능

- `HTTP`: 웹 서비스의 응답 상태 및 코드 확인, 응답 시간 측정  
- `TCP`: 포트 오픈 여부, 연결 가능성 확인  
- `ICMP (Ping)`: 호스트 생존 여부 확인  
- `DNS`: 이름 해석 결과 및 시간 확인  
- `SSL`: 인증서 만료일, 유효성 등 확인

---

## 🔹 작동 원리

1. Prometheus가 `blackbox_exporter`에 프로브 요청을 전송  
2. `blackbox_exporter`가 대상 서비스에 직접 접속(HTTP, TCP 등)  
3. 프로브 결과를 Prometheus가 수집 가능한 **메트릭 포맷으로 반환**

> 📥 메트릭 예시:  
> `probe_success`, `probe_duration_seconds`, `probe_http_status_code`, `probe_ssl_earliest_cert_expiry`, ...

---

## 🔹 우리 프로젝트에서의 적용 예시

| 대상 | 설정 파일 | 모니터링 목적 |
|------|------------|----------------|
| **Kong Gateway** | `blackbox-kong.yml` | API 게이트웨이 엔드포인트의 가용성 확인 |
| **MongoDB** | `blackbox-mongodb.yml` | MongoDB HTTP 포트 접근 가능 여부 확인 |
| **Kibana** | `blackbox-kibana.yml` | Kibana 대시보드 외부 노출 상태 확인 |

---

## 🔹 실전 예: MongoDB Probe 구성

```yaml
- job_name: 'blackbox-mongodb'
  metrics_path: /probe
  params:
    module: [http_2xx]
  static_configs:
    - targets:
        - http://mongo.example.local:27017
  relabel_configs:
    - source_labels: [__address__]
      target_label: __param_target
    - target_label: instance
      source_labels: [__address__]
    - target_label: __address__
      replacement: blackbox-exporter:9115  # Blackbox Exporter 주소
```

---

## 🔹 장점 및 효과

| 장점 | 설명 |
|------|------|
| **실제 사용자 시점 관찰** | 내부 상태가 아니라, 외부에서 실제로 접속 가능한지 확인 |
| **빠른 장애 감지** | 서비스가 정상적으로 기동 중이어도 외부 접속 불가 상태를 감지 가능 |
| **성능 측정 가능** | 응답 시간, 인증서 유효성 등 사용자 경험 관련 지표 수집 가능 |
| **간단한 설정** | 별도의 서비스 내 Agent 없이 외부 모니터링만으로 구성 가능 |

---

## ✅ 요약

> **Blackbox Exporter는 서비스의 외부 접근성을 측정하는 Prometheus Exporter로, 가용성, 응답 시간, 인증서 상태 등을 사용자 관점에서 모니터링할 수 있도록 도와줍니다.**

실제 운영 환경에서는 내부 메트릭 수집 Exporter와 **Blackbox Exporter를 병행 구성**함으로써,  
**내부 상태 + 외부 접근 가능 여부**를 함께 확인하는 **완전한 관측 체계**를 구축할 수 있습니다.