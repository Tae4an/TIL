# 🌳 자바 상속의 개요 및 예제

## 📚 1. 상속의 정의

> "기존의 것을 이어받아, 새로운 것을 만들어내는 과정"
>
- 🔹 기존 클래스의 특성을 새로운 클래스가 물려받는 개념
- 🔹 코드 재사용성 증가와 계층적 관계 구조 형성의 방법

## 🔑 2. 상속의 주요 특징

| 특징 | 설명 |
| --- | --- |
| `extends` 키워드 | 상속 구현에 사용 |
| 단일 상속 | 다중 상속 불가 |
| Object 클래스 | 모든 클래스의 최상위 부모 |

## 💡 3. 상속의 이점

- 🔹 코드 중복 감소
- 🔹 확장성과 유지보수성 향상
- 🔹 다형성 구현의 기반

## 🧩 4. 주요 개념

- 🌟 **부모 클래스** (슈퍼 클래스): 상속을 제공하는 클래스
- 🌱 **자식 클래스** (서브 클래스): 상속받는 클래스
- 🔄 **메소드 오버라이딩**: 부모 클래스의 메소드를 자식 클래스에서 재정의
- 🔝 **`super` 키워드**: 부모 클래스의 멤버에 접근하는 방법

## 💻 5. 예제 코드

```java
// 부모 클래스
class Animal {
    protected String name;

    public Animal(String name) {
        this.name = name;
    }

    public void makeSound() {
        System.out.println("동물이 소리를 낸다");
    }
}

// 자식 클래스
class Dog extends Animal {
    public Dog(String name) {
        super(name);  // 부모 클래스의 생성자 호출
    }

    @Override
    public void makeSound() {
        System.out.println(name + "이(가) 멍멍 짖는다");
    }

    public void fetch() {
        System.out.println(name + "이(가) 공을 가져온다");
    }
}

// 메인 클래스
public class InheritanceExample {
    public static void main(String[] args) {
        Animal animal = new Animal("동물");
        Dog dog = new Dog("뽀삐");

        animal.makeSound();  // 출력: 동물이 소리를 낸다
        dog.makeSound();     // 출력: 뽀삐이(가) 멍멍 짖는다
        dog.fetch();         // 출력: 뽀삐이(가) 공을 가져온다
    }
}

```

## ⚠️ 6. 주의사항

- 🔒 캡슐화 원칙을 고려한 상속 설계의 중요성
- 🔍 불필요한 상속으로 인한 복잡성 증가 가능성
- 🛑 `final` 키워드를 통한 상속 제한 가능성

---

## 🎓 결론

> 자바에서의 상속은 객체 지향 프로그래밍의 핵심 개념으로, 코드 재사용성과 확장성을 높이는 강력한 도구. 그러나 적절한 사용과 설계가 중요한 개념.
>

---

💡 **Tip**: 상속을 사용할 때는 항상 "is-a" 관계를 고려. 예를 들어, "개는 동물이다"와 같은 관계가 성립할 때 상속을 사용하는 것이 적절하다.