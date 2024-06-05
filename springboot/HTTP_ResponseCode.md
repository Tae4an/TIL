## HTTP 응답 코드 종류와 설명

### 1xx: 정보 응답
- **100 Continue**: 클라이언트가 요청의 일부를 전송했고, 서버가 이를 수신하여 나머지 요청을 계속하라는 응답을 의미한다.
- **101 Switching Protocols**: 클라이언트가 요청한 프로토콜로 변경 중이라는 응답을 의미한다.

### 2xx: 성공 응답
- **200 OK**: 요청이 성공적으로 처리되었음을 의미한다.
- **201 Created**: 요청이 성공적으로 처리되었고, 새로운 자원이 생성되었음을 의미한다.
- **202 Accepted**: 요청이 수락되었지만, 아직 처리되지 않았음을 의미한다.
- **203 Non-Authoritative Information**: 요청이 성공적으로 처리되었으나, 다른 소스에서 수집된 정보를 의미한다.
- **204 No Content**: 요청이 성공적으로 처리되었지만, 응답 본문이 없음을 의미한다.
- **205 Reset Content**: 요청이 성공적으로 처리되었으며, 클라이언트는 현재 문서를 초기 상태로 리셋해야 함을 의미한다.
- **206 Partial Content**: 클라이언트가 범위 요청을 했고, 서버가 해당 부분만 전송했음을 의미한다.

### 3xx: 리다이렉션 응답
- **300 Multiple Choices**: 요청에 대해 여러 응답이 가능함을 의미한다.
- **301 Moved Permanently**: 요청한 리소스가 새로운 URL로 영구적으로 이동했음을 의미한다.
- **302 Found**: 요청한 리소스가 일시적으로 다른 URL로 이동했음을 의미한다.
- **303 See Other**: 요청한 리소스를 다른 URL에서 GET 메서드로 얻을 수 있음을 의미한다.
- **304 Not Modified**: 클라이언트가 캐시한 버전이 최신 버전과 동일하므로, 다시 전송할 필요가 없음을 의미한다.
- **305 Use Proxy**: 요청한 리소스를 얻기 위해 프록시를 사용해야 함을 의미한다.
- **307 Temporary Redirect**: 요청한 리소스가 일시적으로 다른 URL로 이동했음을 의미하며, 클라이언트는 요청 메서드를 변경하지 말아야 한다.
- **308 Permanent Redirect**: 요청한 리소스가 새로운 URL로 영구적으로 이동했음을 의미하며, 클라이언트는 요청 메서드를 변경하지 말아야 한다.

### 4xx: 클라이언트 오류 응답
- **400 Bad Request**: 잘못된 요청으로 인해 서버가 요청을 이해할 수 없음을 의미한다.
- **401 Unauthorized**: 인증이 필요함을 의미한다.
- **402 Payment Required**: 결제 요구가 필요함을 의미한다 (현재 사용되지 않음).
- **403 Forbidden**: 서버가 요청을 이해했지만 승인을 거부했음을 의미한다.
- **404 Not Found**: 요청한 리소스를 찾을 수 없음을 의미한다.
- **405 Method Not Allowed**: 요청한 메서드가 허용되지 않음을 의미한다.
- **406 Not Acceptable**: 요청한 리소스가 클라이언트의 헤더에 따라 응답할 수 없음을 의미한다.
- **407 Proxy Authentication Required**: 프록시 인증이 필요함을 의미한다.
- **408 Request Timeout**: 서버가 요청 대기 시간을 초과했음을 의미한다.
- **409 Conflict**: 요청이 서버의 현재 상태와 충돌함을 의미한다.
- **410 Gone**: 요청한 리소스가 더 이상 사용되지 않음을 의미한다.
- **411 Length Required**: Content-Length 헤더 필드가 필요함을 의미한다.
- **412 Precondition Failed**: 클라이언트의 전제 조건이 서버에서 충족되지 않았음을 의미한다.
- **413 Payload Too Large**: 요청한 엔터티가 너무 커서 서버가 처리할 수 없음을 의미한다.
- **414 URI Too Long**: 요청한 URI가 너무 길어서 서버가 처리할 수 없음을 의미한다.
- **415 Unsupported Media Type**: 요청한 미디어 타입이 서버에서 지원되지 않음을 의미한다.
- **416 Range Not Satisfiable**: 클라이언트가 요청한 범위가 서버에서 처리할 수 없음을 의미한다.
- **417 Expectation Failed**: 클라이언트의 Expect 헤더가 서버에서 충족되지 않았음을 의미한다.
- **418 I'm a teapot**: 만우절 기념으로 만든 코드로, 커피를 끓일 수 없음을 의미한다 (RFC 2324).
- **421 Misdirected Request**: 요청이 서버의 의도와 일치하지 않음을 의미한다.
- **422 Unprocessable Entity**: 요청이 잘못되었지만 서버가 구문 분석할 수 있음을 의미한다.
- **423 Locked**: 요청한 리소스가 잠겨 있음을 의미한다.
- **424 Failed Dependency**: 이전 요청이 실패하여 현재 요청도 실패함을 의미한다.
- **426 Upgrade Required**: 클라이언트가 다른 프로토콜로 전환해야 함을 의미한다.
- **428 Precondition Required**: 요청에 전제 조건 헤더가 필요함을 의미한다.
- **429 Too Many Requests**: 클라이언트가 너무 많은 요청을 보냈음을 의미한다.
- **431 Request Header Fields Too Large**: 요청 헤더 필드가 너무 커서 서버가 처리할 수 없음을 의미한다.
- **451 Unavailable For Legal Reasons**: 법적인 이유로 요청한 리소스를 제공할 수 없음을 의미한다.

### 5xx: 서버 오류 응답
- **500 Internal Server Error**: 서버에 오류가 발생하여 요청을 처리할 수 없음을 의미한다.
- **501 Not Implemented**: 서버가 요청을 처리할 기능을 지원하지 않음을 의미한다.
- **502 Bad Gateway**: 게이트웨이나 프록시 서버가 잘못된 응답을 받았음을 의미한다.
- **503 Service Unavailable**: 서버가 과부하 상태이거나 유지보수 중임을 의미한다.
- **504 Gateway Timeout**: 게이트웨이나 프록시 서버가 정해진 시간 내에 응답을 받지 못했음을 의미한다.
- **505 HTTP Version Not Supported**: 서버가 요청에 사용된 HTTP 버전을 지원하지 않음을 의미한다.
- **506 Variant Also Negotiates**: 서버가 변형 선택에 따라 오류가 발생했음을 의미한다.
- **507 Insufficient Storage**: 서버가 요청을 저장할 공간이 부족함을 의미한다.
- **508 Loop Detected**: 서버가 무한 루프를 감지했음을 의미한다.
- **510 Not Extended**: 요청에 필요한 추가 확장이 필요함을 의미한다.
- **511 Network Authentication Required**: 네트워크 인증이 필요함을 의미한다.
