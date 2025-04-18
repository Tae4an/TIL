# What is Java

## 프로그래밍 언어의 역할

- 사람과 컴퓨터에서의 동작하는 애플리케이션을 만들기 위한 과정
- 자연어와 기계어 사이의 다리 역할
- 컴파일 언어와 스크립트 언어

| **특성** | **컴파일 언어** | **스크립트 언어** |
| --- | --- | --- |
| **실행 방식** | 소스 코드를 기계어로 변환 후 실행 | 소스 코드를 한 줄씩 해석하여 실행 |
| **성능** | 빠른 실행 속도 | 상대적으로 느림 |
| **개발 속도** | 느린 개발 (정적 타입 검사) | 빠른 개발 (동적 타이핑) |
| **메모리 관리** | 개발자가 직접 관리 | 자동 메모리 관리 (가비지 컬렉션) |
| **보안** | 코드가 기계어로 변환되어 소스 코드 숨김 가능 | 코드가 노출되기 쉬움 |
| **대표 언어** | C, C++, Java | Python, JavaScript, Ruby |


## 자바의 특징
## **플랫폼 독립적**

- Java는 Java Virtual Machine (JVM) 위에서 실행되기 때문에 운영체제에 독립적
- Java 코드는 바이트 코드로 컴파일되어 JVM에서 실행되므로, 다양한 운영체제에서 동일하게 동작 가능

## **객체지향 언어**

- 현실 세계에 있는 모든 요구사항을 프로그램화 가능
- Java는 객체지향 프로그래밍(OOP)을 지원하며, 코드의 재사용성과 유지보수성을 높이기 위해 클래스와 객체를 사용하여 프로그램을 구성. 이는 캡슐화, 상속, 다형성 등의 OOP 개념을 통해 구현


## **함수형 코딩 지원**

- Java 8부터 람다 표현식과 같은 함수형 프로그래밍 요소를 도입하여, 함수형 코딩을 지원하여, 코드의 간결성과 가독성을 높이는 데 기여

    ```java
    public class Lambda {
        public static void main(String[] args) {
            List<String> names = Arrays.asList("홍길동", "최태산", "김철수");
    
            // 람다 표현식 사용
            names.forEach(name -> System.out.println(name));
        }
    }
    ```


## **분산 처리 지원**

- Java는 네트워크를 통해 여러 시스템 간의 분산 처리를 지원
- Java RMI(Remote Method Invocation)와 같은 기술을 통해 구현되며, 분산 환경에서의 애플리케이션 개발이 용이함

## **멀티 쓰레드 지원**

- Java는 멀티 쓰레딩을 지원하여, 하나의 프로그램 내에서 여러 작업을 동시에 수행 가능
- Thread 클래스와 Runnable 인터페이스를 통해 구현되며, 자바 API를 사용하여 쉽게 멀티 쓰레드 환경을 구축 가능

```java
public class MultiThread {
    public static void main(String[] args) {
        // Thread 클래스를 상속받아 사용
        Thread thread1 = new ThreadExample();
        thread1.start();

        // Runnable 인터페이스를 구현하여 사용
        Thread thread2 = new Thread(new RunnableExample());
        thread2.start();
    }
}

// Thread 클래스를 상속받아 쓰레드 구현
class ThreadExample extends Thread {
    @Override
    public void run() {
        System.out.println("Thread 1 is running");
    }
}

// Runnable 인터페이스를 구현하여 쓰레드 구현
class RunnableExample implements Runnable {
    @Override
    public void run() {
        System.out.println("Thread 2 is running");
    }
}

```
# Java의 단점

## **메모리 소비**

- Java는 가상 머신(JVM)에서 실행되기 때문에 다른 언어에 비해 더 많은 메모리를 소비할 수 있음

## **성능**

- JVM의 중간 단계 컴파일 및 가비지 컬렉션 프로세스 때문에 네이티브 언어(C, C++)에 비해 실행 속도가 느릴 수 있음

## **복잡한 코드**

- Java는 비교적 장황한 문법을 가지고 있어, 특히 간단한 작업을 수행할 때 코드가 길어질 수 있음

## **초기 실행 속도**

- JVM을 로드하는 초기 단계에서 프로그램이 느리게 시작될 수 있음

## **가비지 컬렉션 제어 부족**

- Java는 자동 가비지 컬렉션을 제공하지만, 개발자가 메모리 관리를 직접 제어하기 어려워 특정 상황에서 비효율적일 수 있음

## **플랫폼 종속성**

- "Write Once, Run Anywhere"라는 철학에도 불구하고, 실제로는 JVM의 구현 차이로 인해 플랫폼 간 호환성 문제가 발생할 수 있음

## **라이브러리의 수**

- 오픈소스가 아니다 보니 개발자들이 라이브러리를 직접 만들어 등록할 수 없어서 라이브러리의 수가 상대적으로 적고, 데이터 과학, 인공지능, 기계 학습 등의 최신 기술에 특화된 라이브러리가 적음

## **데이터 분석**:

- Python이나 R과 같은 언어들은 데이터 분석에 특화된 풍부한 라이브러리와 더불어 간결하고 직관적인 문법을 제공해주는 반면, Java는 이와 같은 데이터 분석 작업을 수행할 때 더 복잡한 코딩이 필요하고, 이를 위한 전용 도구나 라이브러리가 적어 생산성이 떨어질 수 있음