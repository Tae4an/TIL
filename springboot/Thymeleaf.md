# Thymeleaf
## 📌 개요

Thymeleaf는 서버 사이드 Java 템플릿 엔진으로, HTML, XML, JavaScript, CSS 및 일반 텍스트를 처리할 수 있다.

## 🌟 주요 특징

### 1. 🎨 자연스러운 템플릿

- HTML 파일을 그대로 템플릿으로 사용 가능
- 디자이너와 개발자 간 협업 용이
- 브라우저에서 정적 파일로 열람 가능

### 2. 🔗 스프링 프레임워크와의 통합

- 스프링 부트와 원활한 통합
- 간편한 설정 및 사용

### 3. 💬 다양한 표현식

- 변수 표현식: `${...}`
- 선택 변수 표현식: `{...}`
- 메시지 표현식: `#{...}`
- 링크 URL 표현식: `@{...}`

### 4. 🔄 반복문, 조건문 지원

- `th:each`: 반복문
- `th:if`, `th:unless`: 조건문
- 동적 콘텐츠 생성 가능

### 5. 🏗️ 레이아웃 기능

- 템플릿 조각(fragments)을 사용한 레이아웃 구성

### 6. 🌐 국제화(i18n) 지원

- 다국어 지원 기능 제공

### 7. 🔧 확장 가능성

- 사용자 정의 방언(dialect)을 통한 기능 확장

## 💻 Thymeleaf 예시 코드

```html
<!DOCTYPE html>
<html xmlns:th="<http://www.thymeleaf.org>">
<head>
    <title>User Profile</title>
</head>
<body>
    <h1 th:text="'Welcome, ' + ${user.name} + '!'"></h1>

    <div th:if="${user.isAdmin}">
        <p>You have admin privileges.</p>
    </div>

    <h2>Your Posts</h2>
    <ul>
        <li th:each="post : ${user.posts}" th:text="${post.title}"></li>
    </ul>

    <footer th:replace="fragments/footer :: footer"></footer>
</body>
</html>
```

## 💡 Thymeleaf 사용 팁

1. 🔍 IDE 플러그인 활용: Thymeleaf 문법 하이라이팅 및 자동 완성 지원
2. 🧩 재사용 가능한 fragment 적극 활용
3. 🔒 보안: `th:text`와 같은 이스케이프 처리 속성 사용
4. 🎨 레이아웃 템플릿 구성으로 일관된 디자인 유지
5. 🌐 국제화 설정 시 메시지 소스 활용

## 🚀 Thymeleaf의 장점

- 🎨 디자이너 친화적인 자연스러운 템플릿
- 🔧 강력하고 유연한 표현식
- 🔗 스프링 프레임워크와의 긴밀한 통합
- 🌐 다양한 콘텐츠 타입 지원 (HTML, XML, JS, CSS 등)
- 🏗️ 모듈화 및 재사용성이 높은 템플릿 구조

Thymeleaf를 사용하면 서버 사이드에서 동적으로 HTML을 생성하고 데이터를 쉽게 바인딩할 수 있어, Java 기반 웹 애플리케이션 개발에 매우 유용다.