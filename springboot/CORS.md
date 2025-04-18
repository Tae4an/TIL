# CORS (Cross-Origin Resource Sharing)
## 개요

CORS는 웹 브라우저에서 보안을 위해 구현된 핵심 보안 메커니즘입니다. 다른 출처(도메인, 프로토콜, 포트)의 리소스에 대한 접근을 제어하여 웹 애플리케이션의 안전성을 보장합니다.

## 🎯 목적

- 동일 출처 정책(Same-Origin Policy)의 제한을 안전하게 완화
- 웹 애플리케이션이 다른 도메인의 리소스에 접근할 수 있도록 허용

## ⚙️ 작동 방식

### 기본 프로세스

1. 브라우저가 교차 출처 요청을 보낼 때 `Origin` 헤더를 포함
2. 서버는 `Access-Control-Allow-Origin` 헤더로 허용된 출처를 지정하여 응답

### 프리플라이트 요청

- 복잡한 요청의 경우 브라우저가 사전에 `OPTIONS` 메서드로 서버에 권한 확인
- 실제 요청 전에 서버의 허용 여부를 먼저 확인하는 과정

## 🔑 주요 CORS 헤더

| 헤더 | 설명 |
| --- | --- |
| Access-Control-Allow-Origin | 허용된 출처 지정 |
| Access-Control-Allow-Methods | 허용된 HTTP 메서드 지정 |
| Access-Control-Allow-Headers | 허용된 헤더 지정 |
| Access-Control-Allow-Credentials | 인증 정보 포함 여부 |

## 🛡️ 보안

### 주의사항

- 서버 측에서 적절한 CORS 설정을 통해 리소스 접근 제어
- 잘못된 설정은 보안 취약점이 될 수 있음
- 와일드카드 사용 시 신중한 검토 필요

### 구현 시 고려사항

- 필요한 출처만 허용
- 필요한 HTTP 메서드만 허용
- 민감한 데이터 전송 시 특별한 주의 필요

## 💻 구현

### 서버 측 구현

```jsx
// Express.js 예시
app.use(cors({
  origin: '<https://allowed-origin.com>',
  methods: ['GET', 'POST'],
  allowedHeaders: ['Content-Type', 'Authorization']
}));

```

### 클라이언트 측

- 브라우저가 자동으로 CORS 처리
- 특별한 클라이언트 구현 불필요

## ❌ 오류 처리

### 디버깅 방법

1. 브라우저 콘솔에서 CORS 오류 확인
2. Network 탭에서 요청/응답 헤더 검사
3. 서버 로그 확인

### 주요 오류 메시지

```
Access to fetch at '<https://api.example.com>' from origin '<https://example.com>'
has been blocked by CORS policy
```

## 📱 사용 사례

### 일반적인 시나리오

- Frontend와 Backend가 다른 도메인에 있는 경우
- 서드파티 API 사용
- CDN에서 리소스 로드
- 마이크로서비스 아키텍처

### 실제 예시

1. React 앱 ([https://app.example.com](https://app.example.com/))에서 API 서버 ([https://api.example.com](https://api.example.com/)) 호출
2. 소셜 미디어 공유 버튼
3. 결제 게이트웨이 통합

## 🔍 모범 사례

- 필요한 출처만 명시적으로 허용
- 프로덕션과 개발 환경의 CORS 설정 분리
- 정기적인 보안 감사 수행
- 에러 로깅 및 모니터링 구현

## 📚 추가 참고사항

- CORS는 브라우저 보안 메커니즘임
- 서버-서버 통신에는 CORS가 적용되지 않음
- 개발 시 CORS 문제 해결을 위한 프록시 사용 가능