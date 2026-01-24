# AWS DAX란?

## 개념 정의

AWS DAX(DynamoDB Accelerator)는 DynamoDB 전용 완전관리형 인메모리 캐시로, 읽기 응답 시간을 밀리초에서 마이크로초로 최대 10배 단축하며, DynamoDB API와 완전 호환되어 엔드포인트만 변경하면 통합되는 서비스다.

쉽게 말해 "DynamoDB 앞에 붙여서 읽기 속도를 10배 빠르게 만드는 자동 캐시"다.

## 핵심 기능

**마이크로초 응답 시간**
밀리초 → 마이크로초로 단축하고, 초당 수백만 요청을 처리한다.

**DynamoDB API 호환**
GetItem, Query, Scan 등을 그대로 사용하며, 엔드포인트만 바꾸면 된다.

**Write-Through 캐싱**
쓰기 시 DynamoDB와 캐시를 자동 동기화하며, 별도 무효화 로직이 불필요하다.

**완전관리형**
노드 장애 복구, 패치, Multi-AZ 배포를 AWS가 자동 관리한다.

## 주요 구성 요소

### 클러스터 구조
- Primary Node: 읽기/쓰기 처리, 캐시 업데이트
- Read Replica: 읽기만 처리, 최대 10개 노드
- Cluster Endpoint: 애플리케이션 연결 진입점

### 캐시 종류
- Item Cache: GetItem 결과 저장, 기본 TTL 5분
- Query Cache: Query/Scan 결과 저장, LRU 알고리즘

### 노드 타입
dax.r3/r4 시리즈 메모리 최적화 인스턴스, 작업 집합 크기로 선택

## 작동 방식

1. **캐시 적중**: DAX가 캐시에서 데이터 발견 → 즉시 반환
2. **캐시 누락**: DynamoDB 조회 → 캐시 저장 → 반환
3. **쓰기 처리**: DynamoDB 먼저 쓰기 → 캐시 업데이트 → 복제본 전파
4. **TTL 만료**: 5분 후 자동 삭제, 다음 요청 시 재조회

## 주요 활용 사례

- 읽기 집약적 워크로드 (읽기:쓰기 10:1 이상)
- 실시간 이벤트 (블랙프라이데이, 티켓팅)
- Hot Key 시나리오 (인기 상품 조회)
- 모바일/IoT 백엔드
- 세션 스토어

## 통합 서비스

AWS Organizations, IAM Identity Center, CloudTrail, Config, VPC, CloudWatch

## DAX vs ElastiCache

| 항목 | DAX | ElastiCache |
|------|-----|-------------|
| 대상 | DynamoDB 전용 | 범용 캐싱 |
| API | DynamoDB API 그대로 | Redis/Memcached API |
| 캐싱 전략 | Write-Through 자동 | 수동 구현 |
| 코드 수정 | 엔드포인트만 변경 | 캐시 로직 전체 작성 |

## 사용하지 말아야 할 경우

- 쓰기 집약적 워크로드
- Strongly Consistent Read 필요 시 (DAX 우회됨)
- Full Table Scan 위주
- 데이터 접근 패턴이 균등한 경우
- 비용 민감하고 읽기 빈도 낮은 경우

## 모범 사례

- Multi-AZ로 최소 3노드 배치
- TTL 최적화: 자주 변경되는 데이터 1-2분, 정적 데이터 10-15분
- 캐시 히트율 70% 이상 유지
- DAX 장애 시 DynamoDB 직접 연결하는 Fallback 로직 구현
- 애플리케이션과 동일 VPC 내 배치

## 비용

노드 인스턴스 시간당 과금, DAX 서비스 자체 비용 없음. dax.r4.large 기준 시간당 약 $0.30, 3노드 클러스터 월 약 $650

## 요약

AWS DAX는 DynamoDB 전용 완전관리형 인메모리 캐시로, 읽기 응답을 마이크로초로 단축하고, Write-Through 캐싱으로 자동 동기화하며, DynamoDB API 완전 호환으로 엔드포인트만 변경하면 통합되고, Primary Node + 최대 10개 Read Replica로 초당 수백만 요청 처리하며, 읽기 집약적 워크로드와 Hot Key 시나리오에 최적이고, Multi-AZ 배포와 TTL 최적화가 권장된다.
