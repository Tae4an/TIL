# Java 매개변수의 다형성
## 📚 정의

> 💡 메소드의 매개 변수를 부모 클래스 타입으로 선언하여, 해당 부모 클래스의 자식 클래스 객체들을 모두 매개 변수로 받을 수 있게 하는 것
>

## ✨ 특징

1. 🌈 **유연성**
    - 하나의 메소드로 여러 타입의 객체 처리 가능
    - 코드의 유연성 증가
2. 🚀 **확장성**
    - 새로운 자식 클래스 추가 시 기존 메소드 수정 불필요
3. ♻️ **코드 재사용**
    - 동일한 메소드를 여러 자식 클래스에 대해 사용 가능
    - 코드 재사용성 향상

## 🛠️ 구현 방법

1. 부모 클래스 또는 인터페이스 타입으로 매개 변수 선언
2. 메소드 내에서 해당 매개 변수를 통해 객체의 메소드 호출

## 💻 예시 코드

```java
class Animal {
    public void sound() {
        System.out.println("동물 소리");
    }
}

class Dog extends Animal {
    @Override
    public void sound() {
        System.out.println("멍멍");
    }
}

class Cat extends Animal {
    @Override
    public void sound() {
        System.out.println("야옹");
    }
}

public class Main {
    public static void makeSound(Animal animal) {
        animal.sound();
    }

    public static void main(String[] args) {
        makeSound(new Dog()); // 출력: 멍멍
        makeSound(new Cat()); // 출력: 야옹
    }
}

```

## 🔑 핵심 포인트

- 매개 변수의 다형성을 통해 코드의 유연성과 재사용성 향상
- 객체 지향 프로그래밍의 핵심 원칙인 다형성을 효과적으로 구현
- 하나의 메소드로 다양한 자식 클래스 객체 처리 가능
- 새로운 자식 클래스 추가 시 기존 코드 수정 최소화

---

> 🌟 요약: 매개 변수의 다형성은 메소드의 매개 변수를 부모 클래스 타입으로 선언하여 여러 자식 클래스 객체를 처리할 수 있게 한다. 이를 통해 코드의 유연성, 확장성, 재사용성이 향상되며, 객체 지향 프로그래밍의 다형성 원칙을 효과적으로 구현할 수 있다.
>