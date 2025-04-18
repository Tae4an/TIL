# URL 패턴
## 1. 쿼리 파라미터 (Query Parameters)

### 📝 형태

`/target?key=value`

### 🎯 목적

- URL의 쿼리 문자열에서 사용
- '?' 이후에 키-값 쌍으로 데이터 전달

### 💻 예시 (JavaScript)

```jsx
const url = '/search?keyword=javascript&page=1';
```

## 2. 경로 변수 (Path Variables)

### 📝 형태

- `/api/ex{target}` 또는 `/api/ex/{target}`

### 🎯 목적

- RESTful API에서 자주 사용
- URL 경로의 일부를 변수로 사용

### 💻 예시 (Java Spring)

```java
@GetMapping("/api/users/{id}")
public User getUser(@PathVariable Long id) {
    // id를 사용하여 사용자 정보를 조회
}
```

### 💻 예시 (JavaScript Fetch)

```jsx
fetch(`/api/users/${userId}`)
    .then(response => response.json())
    .then(data => console.log(data));
```

## 3. 템플릿 리터럴 (Template Literals in JavaScript)

### 📝 형태

```jsx
`/api/${variable}`
```

### 🎯 목적

- JavaScript에서 문자열 내에 변수 삽입

### 💻 예시

```jsx
const target = 'example';
const url = `/api/${target}`;
```

## 🚀 활용

- 동적 URL 생성
- API 엔드포인트 정의
- RESTful API 설계
- 프론트엔드에서 백엔드로 데이터 요청 시 사용