# JSP (JavaServer Pages) 개요 및 기본 문법
## 📌 JSP 개요

JSP는 HTML 내에 자바 코드를 삽입하여 동적으로 웹 페이지를 생성하는 서버 사이드 스크립트 언어다.

## 🌟 JSP의 주요 특징

1. 📝 HTML 코드에 Java 코드 삽입 가능
2. 🖥️ 서블릿 기술을 기반으로 동작
3. 🏗️ MVC 패턴에서 View 역할 담당
4. 🧰 내장 객체 제공으로 편리한 개발 가능

## 🔧 JSP 기본 문법

### 1. 스크립트 요소

- 🏷️ 선언문: `<%! %>` - 변수, 메소드 선언
- 📜 스크립틀릿: `<% %>` - Java 코드 작성
- 🖨️ 표현식: `<%= %>` - 값을 출력

### 2. 지시자

- ⚙️ page 지시자: `<%@ page ... %>` - JSP 페이지 설정
- 📎 include 지시자: `<%@ include ... %>` - 다른 파일 포함
- 🏷️ taglib 지시자: `<%@ taglib ... %>` - 태그 라이브러리 사용

### 3. 액션 태그

- 🔗 `<jsp:include>`, `<jsp:forward>`, `<jsp:useBean>` 등

### 4. 내장 객체

- 📦 request, response, session, application 등

### 5. 표현 언어(EL)

- 💬 `${...}` 형식으로 사용

### 6. JSTL(JSP Standard Tag Library)

- 🏷️ 조건문, 반복문 등을 태그 형식으로 제공

## 💻 JSP 예시 코드

```html
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>JSP Example</title>
</head>
<body>
    <h1>Welcome to JSP</h1>

    <%-- 스크립틀릿 사용 예시 --%>
    <%
    String name = "John Doe";
    out.println("Hello, " + name + "!");
    %>

    <%-- 표현식 사용 예시 --%>
    <p>The current time is: <%= new java.util.Date() %></p>

    <%-- JSTL 사용 예시 (core 태그 라이브러리 사용) --%>
    <%@ taglib uri="<http://java.sun.com/jsp/jstl/core>" prefix="c" %>
    <c:if test="${not empty param.name}">
        <p>Hello, ${param.name}!</p>
    </c:if>
</body>
</html>
```

## 💡 JSP 사용 시 주의사항

- 🔄 JSP는 서블릿으로 변환되어 실행됨
- ⏳ 초기 실행 시 변환 및 컴파일 과정으로 인한 지연 발생 가능
- 🚀 이후 요청에서는 빠른 응답 속도를 보임
- 🔧 비즈니스 로직은 JavaBeans나 서비스 클래스로 분리하는 것이 좋음
- 🏗️ MVC 패턴을 적용하여 유지보수성과 재사용성을 높이는 것이 권장됨

JSP는 Java 기반의 웹 애플리케이션 개발에서 View 계층을 구현하는 데 널리 사용되며, 서블릿과 함께 동적인 웹 페이지를 생성하는 강력한 도구다.