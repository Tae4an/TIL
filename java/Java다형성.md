# Java 다형성 (Polymorphism)
## 🔍 코드 비교

```java
Employee employee = new Manager(111, "qwer", 1000, 100);
Manager manager = new Manager(101, "Taesan2", 5000, 500);
```

## 🔑 주요 차이점

1. **변수의 선언 타입**
    - `employee`: `Employee` 타입
    - `manager`: `Manager` 타입
2. **다형성의 활용**
    - `employee`: 다형성 활용 (상위 클래스 타입으로 하위 클래스 객체 참조)
    - `manager`: 일반적인 객체 생성 (같은 타입으로 선언 및 초기화)

## 💡 개념 설명

### 1. 업캐스팅 (Upcasting)

> Employee employee = new Manager(...);
>
- 🔹 하위 클래스 객체를 상위 클래스 타입으로 참조
- 🔹 자동으로 수행됨 (명시적 캐스팅 불필요)
- 🔹 다형성의 기본 원리

### 2. 일반적인 객체 생성

> Manager manager = new Manager(...);
>
- 🔹 같은 타입으로 선언 및 초기화
- 🔹 모든 Manager 클래스의 멤버에 직접 접근 가능

## ⚖️ 각 방식의 특징

| 특징 | Employee employee = new Manager(...) | Manager manager = new Manager(...) |
| --- | --- | --- |
| 접근 가능한 멤버 | Employee 클래스에 정의된 멤버만 접근 가능 | Manager 클래스의 모든 멤버 접근 가능 |
| 다형성 | 활용됨 | 활용되지 않음 |
| 유연성 | 높음 (다양한 하위 클래스 객체 할당 가능) | 낮음 (Manager 객체만 할당 가능) |
| 타입 캐스팅 | 다운캐스팅 필요할 수 있음 | 불필요 |

## 🌟 사용 시나리오

1. **`Employee employee = new Manager(...);`**
    - 여러 종류의 직원(Manager, Engineer 등)을 동일하게 처리해야 할 때
    - 메소드 파라미터로 상위 클래스 타입을 받아 다양한 하위 클래스 객체를 처리할 때
2. **`Manager manager = new Manager(...);`**
    - Manager 클래스의 특정 메소드나 속성을 직접 사용해야 할 때
    - Manager 타입으로만 작업할 것이 확실할 때

---

💡 **Tip**: 다형성을 활용하면 코드의 유연성과 재사용성을 높일 수 있다. 그러나 특정 하위 클래스의 고유 기능이 필요한 경우, 해당 타입으로 직접 선언하는 것이 더 적절할 수 있습.

## 🔍 코드 비교 및 실행 예제

```java
public static void main(String[] args) {
    Employee employee = new Manager(111, "qwer", 1000, 100);
    Manager manager = new Manager(101, "Taesan2", 5000, 500);

    System.out.println(employee.toString());
    System.out.println(employee.getAnnSalary());

// 하위 클래스의 함수 사용 불가// System.out.println(employee.getBonus());  // 컴파일 에러

// Manager 객체에서는 getBonus() 호출 가능
    System.out.println(manager.getBonus());
}
```

## 🔑 주요 차이점 및 특징

1. **변수의 선언 타입**
    - `employee`: `Employee` 타입
    - `manager`: `Manager` 타입
2. **다형성의 활용**
    - `employee`: 다형성 활용 (상위 클래스 타입으로 하위 클래스 객체 참조)
    - `manager`: 일반적인 객체 생성 (같은 타입으로 선언 및 초기화)
3. **메소드 접근 가능성**
    - `employee`: `Employee` 클래스에 정의된 메소드만 호출 가능
    - `manager`: `Manager` 클래스의 모든 메소드 호출 가능

## 💡 실행 결과 분석

1. **`employee.toString()`**
    - `Employee`와 `Manager` 모두 `toString()` 메소드를 가짐
    - 실제로는 `Manager`의 `toString()` 메소드가 호출됨 (메소드 오버라이딩)
2. **`employee.getAnnSalary()`**
    - `Employee` 클래스에 정의된 메소드이므로 호출 가능
    - `Manager`에서 오버라이딩했다면 `Manager`의 메소드가 호출됨
3. **`employee.getBonus()` (주석 처리됨)**
    - `Employee` 타입으로 선언되어 있어 `Manager` 고유의 메소드 호출 불가
    - 컴파일 에러 발생
4. **`manager.getBonus()`**
    - `Manager` 타입으로 선언되어 있어 `Manager` 고유의 메소드 호출 가능

## 🌟 다형성의 장단점

### 장점

- 🔹 코드의 유연성 증가
- 🔹 확장성 향상
- 🔹 유지보수 용이성

### 단점

- 🔹 하위 클래스의 특정 메소드 직접 호출 불가
- 🔹 런타임 시 실제 객체 타입 확인 필요할 수 있음

## ⚖️ 다운캐스팅을 통한 해결책

하위 클래스의 특정 메소드를 호출해야 할 경우:

```java
if (employee instanceof Manager) {
    Manager managerEmployee = (Manager) employee;
    System.out.println(managerEmployee.getBonus());
}
```

- 🔹 `instanceof`로 실제 객체 타입 확인
- 🔹 명시적 형변환(다운캐스팅)을 통해 하위 클래스 메소드 접근

---

💡 **Tip**: 다형성은 강력한 도구지만, 때로는 명시적인 타입 체크와 캐스팅이 필요할 수 있다. 이를 통해 유연성과 구체적인 기능 사용 사이의 균형을 맞출 수 있다.