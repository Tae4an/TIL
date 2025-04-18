# 🛠️ 자바 커스텀 예외 처리의 개요 및 예제

## 📌 1. 커스텀 예외의 정의

> "표준 예외로는 부족할 때, 우리만의 예외를 만든다"
>
- 🔹 기존 Java 예외 클래스를 확장하여 생성한 사용자 정의 예외
- 🔹 특정 비즈니스 로직이나 애플리케이션 요구사항에 맞춘 예외 처리 방법

## 🌟 2. 커스텀 예외의 장점

| 장점 | 설명 |
| --- | --- |
| 명확성 | 예외의 의미를 더 구체적으로 전달 |
| 유연성 | 애플리케이션 특화된 예외 처리 가능 |
| 계층화 | 예외의 계층 구조 형성 가능 |

## 🛠️ 3. 커스텀 예외 생성 방법

1. `Exception` 또는 `RuntimeException` 클래스를 상속
2. 필요에 따라 생성자 정의
3. 필요한 추가 메서드나 필드 구현

## 💻 4. 예제 코드

```java
// 커스텀 예외 클래스
public class InsufficientFundsException extends Exception {
    private double amount;

    public InsufficientFundsException(double amount) {
        super("잔액 부족: " + amount + " 원이 부족합니다.");
        this.amount = amount;
    }

    public double getAmount() {
        return amount;
    }
}

// 사용 예시
public class BankAccount {
    private double balance;

    public void withdraw(double amount) throws InsufficientFundsException {
        if (balance < amount) {
            throw new InsufficientFundsException(amount - balance);
        }
        balance -= amount;
    }

    // 기타 메서드...
}

// 메인 클래스
public class CustomExceptionExample {
    public static void main(String[] args) {
        BankAccount account = new BankAccount();
        try {
            account.withdraw(100);
        } catch (InsufficientFundsException e) {
            System.out.println(e.getMessage());
            System.out.println("부족한 금액: " + e.getAmount() + " 원");
        }
    }
}

```

## 🔑 5. 커스텀 예외 사용 시 고려사항

- 🔹 기존 표준 예외로 충분한지 먼저 검토
- 🔹 예외의 이름은 명확하고 설명적으로 작성 (예: `InsufficientFundsException`)
- 🔹 예외 메시지에 충분한 정보 포함
- 🔹 필요한 경우 추가 데이터나 메서드 제공

## ⚖️ 6. Checked vs Unchecked 예외

| Checked 예외 | Unchecked 예외 |
| --- | --- |
| `Exception` 상속 | `RuntimeException` 상속 |
| 컴파일러가 처리를 강제 | 컴파일러가 처리를 강제하지 않음 |
| 예측 가능한 예외에 사용 | 프로그래밍 오류에 주로 사용 |

---

## 🎓 결론

> 커스텀 예외는 애플리케이션의 특정 요구사항에 맞는 예외 처리를 가능하게 하는 강력한 도구. 적절히 사용하면 코드의 가독성과 유지보수성을 크게 향상시킬 수 있는 개념.
>

---

💡 **Tip**: 커스텀 예외를 만들 때는 항상 그 필요성을 신중히 고려. 때로는 기존의 표준 예외를 사용하는 것이 더 좋을 수 있다.