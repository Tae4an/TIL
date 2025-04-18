# SSR (Server-Side Rendering)
## 📌 SSR 개요

SSR은 서버에서 페이지의 HTML을 렌더링하여 클라이언트로 보내주는 웹 렌더링 방식이다.

### 주요 특징

- 🏗️ 서버에서 HTML 생성 후 클라이언트로 전송
- ⚡ 초기 로딩 시 완성된 HTML로 빠른 콘텐츠 표시
- 🔍 검색 엔진 최적화(SEO)에 유리

## 🔄 SSR의 작동 방식

1. 🌐 사용자가 웹사이트 방문 요청
2. 🛠️ 서버는 요청받은 페이지에 대한 HTML 생성
    - 필요한 데이터 조회 및 페이지 내용 구성
3. 📤 서버가 렌더링된 HTML을 클라이언트로 전송
4. 📊 클라이언트(브라우저)가 받은 HTML 파싱 및 렌더링
    - 이 시점에서 사용자는 페이지 콘텐츠 확인 가능
5. 📥 브라우저가 HTML 파싱 중 필요한 JS 파일 요청
6. 📤 서버가 요청받은 JS 파일을 클라이언트로 전송
7. 💧 브라우저에서 JS 실행 및 하이드레이션 과정 수행
    - 하이드레이션: 정적 HTML에 동적 기능 연결
8. ✅ 페이지 완전 로드 및 사용자 상호작용 가능 상태

## ⚖️ SSR의 장단점

### 장점 👍

- 🚀 초기 로딩 속도가 빠름
- 🔍 SEO에 유리
- 📉 클라이언트 부하 감소 (서버에서 데이터 처리)

### 단점 👎

- 📈 서버 부하 증가
- 🔄 페이지 이동 시 새로고침 필요
- 🧩 개발 복잡도 증가

## 🎯 SSR 적용 케이스

SSR은 다음과 같은 경우에 적합한 렌더링 방식입니다:

- 📰 초기 로딩 속도가 중요한 웹사이트
- 🔍 SEO가 중요한 웹사이트
- 📚 콘텐츠 중심의 정적 웹사이트
- 🌐 대규모 정보 제공 사이트

## 💻 SSR 예시 코드 (Node.js + Express)

```jsx
const express = require('express');
const app = express();

app.get('/', (req, res) => {
  const html = `
    <html>
      <head>
        <title>SSR Example</title>
      </head>
      <body>
        <h1>Hello, SSR!</h1>
        <p>This content was rendered on the server.</p>
      </body>
    </html>
  `;
  res.send(html);
});

app.listen(3000, () => {
  console.log('Server is running on http://localhost:3000');
});
```

이 예시에서 서버는 요청을 받으면 HTML을 동적으로 생성하여 클라이언트에 전송한다.

SSR을 활용하면 초기 페이지 로드 속도를 개선하고 검색 엔진 최적화를 향상시킬 수 있다.