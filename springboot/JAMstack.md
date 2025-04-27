# JAMstack이란?
**JAMstack**은 JavaScript, API, Markup의 앞글자를 딴 현대적인 웹 개발 아키텍처이다.

프론트엔드와 백엔드를 분리하고, 정적 파일을 CDN으로 배포하며, 동적 기능은 JavaScript와 API로 처리함.

---

## JAMstack의 구성요소 🧩

### 1. JavaScript 🖥️

브라우저에서 실행되어 사용자 상호작용과 동적 기능을 담당함.

(예: React, Vue, Angular 등)

### 2. API 🔗

서버 비즈니스 로직이나 데이터 처리를 서버리스 함수 또는 외부 API로 외부화함.

(예: REST API, GraphQL API)

### 3. Markup 📄

정적 사이트 생성기(SSG)를 통해 빌드 시점에 미리 생성된 HTML 파일.

(예: Gatsby, Next.js, Hugo)

---

## JAMstack은 어떻게 동작할까? ⚙️

1. 빌드 시점에 모든 페이지를 정적으로 생성함.
2. 생성된 정적 파일(HTML, CSS, JS)을 CDN에 배포함.
3. 사용자가 요청 시 CDN에서 파일을 제공하고, 필요한 경우 JavaScript가 API를 호출함.

---

## JAMstack의 장점 🌟

- **빠른 성능** ⚡
    
    → CDN을 통한 빠른 파일 제공, SEO 최적화
    
- **높은 보안성** 🛡️
    
    → 서버 측 동적 렌더링 제거로 취약점 감소
    
- **확장성과 비용 효율성** 🌍
    
    → 서버 관리 부담 감소, 트래픽 분산 용이
    
- **개발자 경험 개선** 🧑‍💻
    
    → 프론트엔드와 백엔드 분리로 협업 용이
    
- **유지보수 용이성** 🔄
    
    → 정적 파일 기반으로 빠른 배포·롤백 가능
    

---

## JAMstack과 SPA, SSR은 어떤 관계일까? 🔥

- **SPA**(Single Page Application)는 일부 JAMstack 아키텍처에 포함될 수 있음.
- 그러나 모든 SPA가 JAMstack은 아님.
- JAMstack은 SPA의 초기 로딩 지연, SEO 문제를 **정적 생성(SSG)**, **API 통합** 등으로 보완함.
- **Next.js**, **Gatsby**, **Nuxt.js**는 SSG와 SSR 모두 지원하여 JAMstack 구축에 최적화된 프레임워크임.

---

## JAMstack의 한계와 주의사항 ⚠️

- 모든 페이지를 정적으로 생성하는 것이 어려운 경우 있음.
    
    → 실시간 데이터는 API 또는 서버리스 함수로 처리해야 함.
    
- 사이트 규모가 커질수록 빌드 시간이 길어질 수 있음.
- 복잡한 동적 기능은 서버 기반 구조에 비해 번거로울 수 있음.

---

## JAMstack 개발 예시 🛠️

- **Gatsby + Netlify**
    
    → Gatsby로 정적 사이트 생성, Netlify로 자동 빌드·배포. API를 통해 동적 기능 추가.
    
- **Next.js**
    
    → SSG, SSR, API 라우트 모두 지원. JAMstack 대규모 프로젝트에 적합.
    

---

# JAMstack 요약 ✨

**JAMstack**은 빠르고 안전한 웹사이트 구축을 위한 현대적 아키텍처임.

JavaScript + API로 동적 기능을 처리하고, Markup은 정적 생성하여 CDN으로 배포함.

성능, 보안, 확장성, 개발 생산성 모두 뛰어남.

**Netlify**, **Vercel** 등 다양한 서비스와 오픈소스 생태계가 JAMstack을 지원함.