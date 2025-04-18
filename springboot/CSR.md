# CSR (Client-Side Rendering) 개요
## 📌 CSR 개요

CSR은 클라이언트(브라우저)에서 JavaScript를 사용하여 페이지 콘텐츠를 동적으로 생성하고 렌더링하는 웹 애플리케이션의 렌더링 방식이다.

## 🔄 CSR의 작동 방식

1. 📤 서버가 최소한의 HTML 구조와 JavaScript 파일 링크 전송
2. 📥 브라우저가 JavaScript 다운로드 및 실행
3. 🏗️ JavaScript가 동적으로 DOM 조작하여 페이지 콘텐츠 생성
4. 👀 사용자가 JavaScript 실행 완료 후 완전한 페이지 확인

## ✅ CSR의 장점

1. 🔄 **상호작용성**: 동적 콘텐츠 업데이트가 빠르고 부드러움
2. 📉 **서버 부하 감소**: 렌더링 작업이 클라이언트에서 이루어져 서버 부하 감소
3. 📱 **SPA 구현**: 단일 페이지 애플리케이션(SPA) 구현에 적합

## ❌ CSR의 단점

1. ⏳ **초기 로딩 시간**: JavaScript 파일 다운로드 및 실행에 시간 소요
2. 🔍 **SEO 문제**: 검색 엔진이 JavaScript로 생성된 콘텐츠 인식 어려움
3. 💻 **성능 의존성**: 클라이언트 기기의 성능에 따라 렌더링 속도 변동

## 🎯 CSR 사용에 적합한 경우

1. 👥 사용자 상호작용이 많은 웹 애플리케이션
2. 🔄 실시간 데이터 업데이트가 필요한 경우
3. 🌐 SEO가 중요하지 않은 웹사이트

## 💻 CSR 예시 코드 (React)

```jsx
import React, { useState, useEffect } from 'react';

function App() {
  const [data, setData] = useState(null);

  useEffect(() => {
    // 데이터를 가져오는 비동기 작업
    fetchData().then(result => setData(result));
  }, []);

  if (!data) return <div>Loading...</div>;

  return (
    <div>
      <h1>CSR Example</h1>
      <p>{data}</p>
    </div>
  );
}

export default App;
```

이 예시에서 React 컴포넌트는 클라이언트 측에서 렌더링되며, 데이터를 비동기적으로 가져와 표시한다.

## 💡 CSR 활용 팁

- 🔄 SSR과 CSR을 적절히 조합하여 사용 (예: Next.js)
- ⚡ 코드 분할(Code Splitting)을 통해 초기 로딩 시간 최적화
- 🔍 SEO가 중요한 경우, 서버사이드 렌더링 또는 정적 생성 고려
- 📊 성능 모니터링 도구를 사용하여 렌더링 성능 최적화

CSR은 현대적인 웹 개발에서 널리 사용되는 방식이지만, 프로젝트의 요구사항에 따라 다른 렌더링 방식과 적절히 조합하여 사용하는 것이 좋다.