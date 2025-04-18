# Wrapper Class 정의와 특징
1. 📌 개념
    - 🎁 기본 데이터 타입을 객체로 감싸는 클래스
    - 🔄 기본 타입 데이터의 객체 처리 가능
2. 📊 Wrapper Class 종류
    - byte → Byte
    - short → Short
    - int → Integer
    - long → Long
    - float → Float
    - double → Double
    - char → Character
    - boolean → Boolean
3. 🌟 주요 특징
    - 🧩 객체로 처리 가능
    - 🔒 값 불변성 (내부 값 변경 불가)
    - 📦 java.lang 패키지에 포함
4. 🔄 Boxing과 Unboxing
    - 📥 Boxing: 기본 타입 → Wrapper 객체
    - 📤 Unboxing: Wrapper 객체 → 기본 타입
    - 🚀 Java 5부터 Auto-Boxing/Unboxing 지원
5. 💼 주요 용도
    - 📚 컬렉션 등 객체 처리 필요 시
    - 🔗 객체 요구 매개변수에 사용
    - 🔢 → 📝 기본 타입 값의 문자열 변환
    - 📝 → 🔢 문자열의 기본 타입 값 변환
6. 💡 활용 의의
    - 🌉 기본 타입과 객체 지향 간 간극 해소
    - 🧩 제네릭, 컬렉션 프레임워크와 연계 사용
    - 🚀 Java 프로그래밍의 유연성 증대
7. 🛠️ 사용 예시

    ```java
    Integer num = 10;// Auto-boxing
    int value = num;// Auto-unboxing
    ```

8. 🎯 주의사항
    - 💾 메모리 사용량 증가 가능성
    - 🐢 기본 타입 대비 약간의 성능 저하 가능성