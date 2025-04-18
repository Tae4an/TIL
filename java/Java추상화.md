# 🎭 자바의 추상화 개념 및 예제

## 📌 1. 추상화의 정의

> "복잡한 현실을 단순화하여 본질만을 표현하는 과정"
>
- 🔹 불필요한 세부 사항을 제거하고 공통된 특성을 추출
- 🔹 현실 세계의 개념을 프로그래밍 언어로 표현하는 방법

## 🌟 2. 추상화의 주요 구성 요소

| 요소 | 설명 |
| --- | --- |
| 추상 클래스 | 하나 이상의 추상 메소드를 포함하는 클래스 |
| 추상 메소드 | 선언만 있고 구현은 없는 메소드 |
| 인터페이스 | 모든 메소드가 추상 메소드인 특별한 형태의 추상 클래스 |

## 💻 3. 추상 클래스 예제

```java
abstract class Animal {
    protected String name;

    public Animal(String name) {
        this.name = name;
    }

    // 추상 메소드
    public abstract void makeSound();

    // 일반 메소드
    public void sleep() {
        System.out.println(name + " is sleeping");
    }
}

class Dog extends Animal {
    public Dog(String name) {
        super(name);
    }

    @Override
    public void makeSound() {
        System.out.println(name + " says Woof!");
    }
}

public class AbstractionExample {
    public static void main(String[] args) {
        Animal dog = new Dog("Buddy");
        dog.makeSound();  // 출력: Buddy says Woof!
        dog.sleep();      // 출력: Buddy is sleeping
    }
}

```

## 🔑 4. 추상화의 장점

- 🔹 코드 재사용성 증가
- 🔹 유지보수 용이성 향상
- 🔹 확장성 제공
- 🔹 복잡성 감소

## 🛠️ 5. 추상 클래스 vs 인터페이스

| 특징 | 추상 클래스 | 인터페이스 |
| --- | --- | --- |
| 메소드 | 추상 메소드와 일반 메소드 모두 가능 | 추상 메소드만 가능 (Java 8 이후 default, static 메소드 가능) |
| 변수 | 모든 종류의 변수 선언 가능 | 상수(public static final)만 가능 |
| 상속 | 단일 상속만 가능 | 다중 구현 가능 |
| 사용 목적 | 관련된 클래스들의 공통 특성 정의 | 서로 관련 없는 클래스들이 공통으로 사용하는 방식 정의 |

## ⚠️ 6. 주의사항

- 🔹 추상 클래스는 인스턴스화할 수 없음
- 🔹 추상 메소드를 포함한 클래스는 반드시 추상 클래스로 선언해야 함
- 🔹 추상 클래스를 상속받은 하위 클래스는 모든 추상 메소드를 구현해야 함

---

## 🎓 결론

> 추상화는 복잡한 시스템을 단순화하고 핵심 개념을 표현하는 강력한 도구. 적절히 사용하면 코드의 구조를 개선하고 유지보수성을 높일 수 있는 핵심 개념.
>

---

💡 **Tip**: 추상화를 설계할 때는 "is-a" 관계를 고려. 추상 클래스는 공통된 특성을 가진 객체들의 일반화된 표현이 되어야 한다.