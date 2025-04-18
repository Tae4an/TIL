# REST, REST API, RESTful API 개념 정리
## REST (Representational State Transfer) 🏛️

REST는 웹 서비스를 위한 아키텍처 스타일 또는 설계 원칙

### 주요 특징 ✨

- 자원 기반 구조
- 무상태성
- 클라이언트-서버 구조
- 캐시 가능성
- 계층화된 시스템
- 균일한 인터페이스

## REST API 🔗

REST API는 REST 아키텍처 스타일을 따라 설계된 API

### 특징 🛠️

- HTTP 프로토콜 사용
- URI를 통한 리소스 식별
- HTTP 메서드(GET, POST, PUT, DELETE 등)를 통한 리소스 조작
- 다양한 데이터 형식 지원 (JSON, XML 등)

## RESTful API 🎯

RESTful API는 REST 원칙을 충실히 따르는 API

### 특징 🔍

- REST 아키텍처의 제약조건을 모두 만족
- 리소스와 행위를 명확히 분리
- 하이퍼미디어를 통한 애플리케이션 상태 전이 (HATEOAS) 지원

## 차이점 및 관계 🔄

### 1. 개념적 범위 📊

- REST: 가장 넓은 개념으로, 아키텍처 스타일을 정의
- REST API: REST 원칙을 적용한 API
- RESTful API: REST 원칙을 엄격히 준수하는 API

### 2. 구현 정도 📈

- REST API: REST 원칙을 부분적으로 적용할 수 있음
- RESTful API: REST 원칙을 완전히 준수하려고 노력함

### 3. 유연성 🌊

- REST API: 상대적으로 유연한 구현 가능
- RESTful API: 더 엄격한 구현 요구

### 4. HATEOAS 적용 🔗

- REST API: HATEOAS를 반드시 적용하지 않아도 됨
- RESTful API: HATEOAS 적용을 권장

### 5. 사용 맥락 💬

- REST API: 일반적으로 REST 스타일을 따르는 API를 지칭할 때 사용
- RESTful API: REST 원칙을 철저히 따르는 API를 강조할 때 사용

## 결론 🎓

- REST는 전체적인 아키텍처 스타일이며, REST API는 이를 구현한 API.
- RESTful API는 REST 원칙을 더욱 엄격하게 따르는 API를 의미.
- 실제로 많은 개발자들이 "REST API"와 "RESTful API"를 혼용하여 사용하지만, 엄밀히 말하면 RESTful API가 REST의 모든 제약조건을 더 충실히 따르는 API를 지칭한다.

## 추가 정보 📚

### REST 원칙의 이점 💡

- 확장성: 클라이언트-서버 분리로 인한 높은 확장성
- 유연성: 다양한 클라이언트 지원 가능
- 독립성: 기술 독립적인 인터페이스 제공

### RESTful API 설계 모범 사례 🌟

- 명확한 리소스 명명 규칙 사용
- 적절한 HTTP 상태 코드 활용
- 버전 관리 구현
- 페이지네이션 지원
- 적절한 인증 및 보안 메커니즘 적용

### REST의 한계와 대안 🔄

- 복잡한 요청 처리의 어려움
- 오버페칭/언더페칭 문제
- 대안 기술: GraphQL, gRPC 등