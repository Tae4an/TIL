# 🔍 jQuery란?

- 자바스크립트 라이브러리
- 웹 개발 효율성 향상 도구
- 간결하고 강력한 기능 제공

## 🖋 기본 문법

```jsx
$(선택자).동작함수();
```

- `$`: jQuery 식별자
- `선택자`: HTML 요소 선택
- `동작함수`: 선택된 요소에 작업 수행

## 🛠 주요 기능

### 1️⃣ 요소 선택

- CSS 선택자 사용
- 예: `$('#id')`, `$('.class')`

### 2️⃣ DOM 조작

- HTML 내용 변경: `.html()`, `.text()`
- 요소 추가/제거: `.append()`, `.remove()`

### 3️⃣ 이벤트 처리

- `.on()` 메서드로 이벤트 핸들러 등록
- 예: `$('button').on('click', function() { ... })`

### 4️⃣ 효과 및 애니메이션

- `.hide()`, `.show()`, `.fadeIn()`, `.fadeOut()` 등

### 5️⃣ Ajax 통신

- `$.ajax()`, `$.get()`, `$.post()` 메서드 제공

### 6️⃣ 속성 조작

- `.attr()`, `.prop()` 메서드로 요소 속성 변경

### 7️⃣ CSS 조작

- `.css()` 메서드로 스타일 변경

### 8️⃣ 체이닝

- 여러 메서드 연속 호출 가능

## 💪 장점

- 크로스 브라우저 호환성 보장
- 간결한 코드 작성 가능
- 풍부한 플러그인 생태계 보유

## 🌟 추가 정보

- 2006년 John Resig에 의해 개발
- "write less, do more" 철학
- 현대 웹에서는 사용 빈도 감소 추세
- 대안: Vanilla JS, React, Vue.js 등

## 🔗 유용한 자료

- [jQuery 공식 문서](https://jquery.com/)
- [jQuery 학습 센터](https://learn.jquery.com/)
- [jQuery 치트 시트](https://oscarotero.com/jquery/)

---

💡 jQuery는 DOM 조작과 이벤트 처리를 단순화하여 웹 개발 생산성을 크게 향상시키는 도구.