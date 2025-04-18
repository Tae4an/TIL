## 정의 🎯

- 비동기 작업 완료 후 실행될 함수
- 다른 함수에 인자로 전달되는 함수

## 주요 특징 🌟

- 비동기 프로그래밍의 핵심 개념
- 코드의 실행 순서 제어
- 함수를 일급 객체로 취급

## 사용 예시 💻

```jsx
function getData(callback) {
// 비동기 작업 (예: API 호출)
    setTimeout(() => {
        const data = "Some data";
        callback(data);
    }, 1000);
}

getData((result) => {
    console.log(result);
});
```

## 장점 👍

- 비동기 작업의 결과 처리 용이성
- 코드의 모듈화 증진
- 유연한 함수 구성 가능

## 단점 👎

- 콜백 지옥 (Callback Hell) 발생 가능성
- 에러 처리의 복잡성
- 코드 가독성 저하 위험

## 대안 및 발전 🚀

- Promise 객체
- Async/Await 구문
- 리액티브 프로그래밍

## 주의사항 ⚠️

- 깊은 중첩 지양
- 명확한 에러 처리 방식 필요
- 콜백 함수의 실행 컨텍스트 주의
- 