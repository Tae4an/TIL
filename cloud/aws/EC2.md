
# AWS EC2란?
**Amazon EC2**는 AWS에서 제공하는 **클라우드 기반 가상 서버** 서비스이다.

필요할 때 서버(인스턴스)를 생성하고, 원하는 시점에 종료할 수 있으며, 컴퓨팅 자원을 탄력적으로 사용할 수 있다.

---

## 주요 특징

| 항목 | 설명 |
| --- | --- |
| **탄력성 (Elastic)** | 서버를 자유롭게 생성/삭제 가능. 트래픽에 따라 수평 확장/축소 용이 |
| **유형 다양성** | 일반형, 컴퓨팅 최적화, GPU, 메모리 최적화 등 용도에 따른 인스턴스 유형 제공 |
| **비용 효율성** | 하드웨어 구매 없이 사용한 만큼만 과금 (종량제, 예약 인스턴스 등) |
| **유연한 제어** | 네트워크, 스토리지, 보안 그룹 등 인프라 구성 요소를 직접 설정 가능 |
| **리전 기반 제공** | 전 세계 다양한 리전/가용 영역에서 인스턴스를 배포 가능 |

---

## 이름 의미

- **Elastic**: 자원(서버)을 탄력적으로 늘리고 줄일 수 있음
- **Compute**: 서버의 연산 처리 능력을 제공
- **Cloud**: 클라우드 인프라에서 운영되는 서비스

즉, **필요할 때마다 조정 가능한 클라우드 컴퓨팅 자원**이라는 뜻

---

## 실제 활용 예시

| 용도 | 설명 |
| --- | --- |
| 웹 서버 | 웹사이트 또는 API 서버 호스팅 |
| 데이터베이스 | DBMS 설치 및 운영 |
| 머신러닝 실험 | 학습용 GPU 인스턴스 사용 |
| 이벤트 대응 | 이벤트 기간 중 임시 서버 확장 |
| 게임 서버 운영 | 트래픽에 따라 확장 가능한 게임 백엔드 |

---

## 요약 문장

> AWS EC2는 클라우드에서 가상 서버를 자유롭게 생성하고 제어할 수 있는 서비스로,탄력적 컴퓨팅 자원을 필요에 따라 사용할 수 있는 핵심 인프라 서비스입니다.
>