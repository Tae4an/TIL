# Java 예외 처리
## 🔍 예외의 분류

### 1. Checked Exception

- 📚 Exception 클래스 상속, RuntimeException 미상속
- ⚖️ 컴파일 시점에 처리 여부 확인
- 🔮 대표 예시: IOException, SQLException

### 2. Unchecked Exception

- 💥 RuntimeException 상속
- 🎭 컴파일러의 처리 여부 확인 없음
- 🎯 대표 예시: NullPointerException, ArrayIndexOutOfBoundsException

---

## 🛠️ 예외 처리 방법

### try-catch 블록

```java
try {
// 예외 발생 가능 코드
} catch (ExceptionType e) {
// 예외 처리 코드
} finally {
// 항상 실행되는 코드 (선택적)
}
```

### throws 키워드

```java
public void methodName() throws ExceptionType {
// 메소드 내용
}
```

---

## 💡 예외 처리 모범 사례

1. 🎯 **구체적 예외 사용**: 문제의 원인을 명확히 식별
2. 📝 **예외 로깅**: 디버깅 및 문제 해결을 위한 정보 기록
3. 🚰 **리소스 관리**: try-with-resources 구문을 통한 자동 리소스 해제
4. 🎁 **예외 래핑**: 저수준 예외를 고수준 예외로 변환하여 의미 있는 컨텍스트 제공
5. 💼 **트랜잭션 관리**: 예외 발생 시 데이터 일관성을 위한 롤백 처리

---

## 🌟 예외 처리의 중요성

- 🏰 프로그램 안정성 향상
- 🦸‍♂️ 예기치 못한 상황에 대한 대응 능력 강화
- 🔍 효율적인 디버깅 및 유지보수 지원
- 🌟 전반적인 코드 품질 개선
- 🧘‍♂️ 오류 상황에서의 정상적인 프로그램 동작 유지
- 🗣️ 사용자에게 적절한 피드백 제공
- 😊 향상된 사용자 경험 제공
- 🛡️ 시스템의 안정성 및 신뢰성 확보

> "효과적인 예외 처리는 견고하고 안정적인 소프트웨어 개발의 핵심 요소다. 적절한 예외 처리를 통해 프로그램의 품질과 사용자 경험을 크게 향상시킬 수 있다."

## 🔍 예외의 두 가지 범주

### 1. ✅ Checked Exceptions

> 컴파일 시점에 처리 여부를 확인하는 예외
>

## 주요 Checked Exceptions

1. 📂 **IOException**: 입출력 작업 관련 예외
2. 🗄️ **SQLException**: 데이터베이스 작업 관련 예외
3. 🔍 **ClassNotFoundException**: 클래스를 찾지 못할 때 발생하는 예외
4. 🔧 **InvocationTargetException**: 리플렉션을 통한 메서드 호출 시 발생하는 예외
5. 📁 **FileNotFoundException**: 파일을 찾지 못할 때 발생하는 예외

## 특징

- 🚦 컴파일 시점에 처리 여부 확인
- 🛠️ try-catch 블록 또는 throws 키워드로 반드시 처리 필요
- 💼 트랜잭션 내에서 발생 시 자동 롤백되지 않음

### 2. 🚀 Unchecked Exceptions (RuntimeExceptions)

> 실행 시점에 발생할 수 있는 예외
>

## 주요 Unchecked Exceptions

1. ❗ **NullPointerException**: null 객체 참조 시 발생하는 예외
2. 📊 **ArrayIndexOutOfBoundsException**: 배열의 유효하지 않은 인덱스 접근 시 발생하는 예외
3. 🚫 **IllegalArgumentException**: 메서드에 부적절한 인자 전달 시 발생하는 예외
4. 🧮 **ArithmeticException**: 수학적 연산 오류 시 발생하는 예외 (예: 0으로 나누기)
5. 🔄 **ClassCastException**: 잘못된 클래스 캐스팅 시 발생하는 예외
6. 🔢 **NumberFormatException**: 문자열을 숫자로 변환할 수 없을 때 발생하는 예외

## 특징

- 🎈 컴파일러가 처리 여부를 확인하지 않음
- 🤹‍♀️ 명시적 처리는 선택사항
- 💣 트랜잭션 내에서 발생 시 자동 롤백 발생

---

## 🛡️ 예외 처리 방식

1. Checked Exceptions: try-catch 블록으로 처리 또는 throws 키워드로 예외 전파
2. Unchecked Exceptions: 필요에 따라 선택적으로 처리

---

## 💡 주요 고려사항

- 🌱 최근 개발 동향: Unchecked Exceptions 선호 추세
- 🔄 Checked Exceptions: 복구 가능한 상황에서 주로 사용
- 🐞 Unchecked Exceptions: 프로그래밍 오류 표현에 주로 사용

> "적절한 예외 처리는 프로그램의 안정성과 견고성을 향상시키는 핵심 요소다. 각 예외의 특성을 이해하고 상황에 맞게 활용하는 것이 중요하다."
