# Repository의 정의와 특징
1. 📌 개념
    - 🗄️ 데이터 접근 계층 추상화 인터페이스
    - 💾 데이터베이스 상호작용 담당
    - 🔄 데이터 저장, 조회, 수정, 삭제 작업 수행
2. 🌟 주요 특징
    - 🧩 데이터 접근 로직 추상화
    - ♻️ 코드 재사용성 증대
    - 🧪 단위 테스트 용이성 향상
3. 🆚 Repository vs DAO
    - 🏗️ Repository: 객체 지향적 데이터 처리
    - 🔧 DAO: 낮은 수준의 데이터 접근 처리
    - 🔗 Repository: 도메인 모델 연관
    - 📊 DAO: 데이터베이스 테이블 밀접 연관
4. 🍃 Spring Data JPA의 Repository

    ```java
    public interface UserRepository extends JpaRepository<User, Long> {
        List<User> findByLastName(String lastName);
    }
    ```

    - 🛠️ 기본 CRUD 연산 자동 제공
    - 📄 페이징, 정렬 기능 포함
5. 💼 Repository 패턴 장점
    - 🔍 관심사 분리
    - 🔧 유지보수성 향상
    - 🔄 데이터 접근 일관성 유지
6. 🚀 활용 의의
    - 🏗️ 애플리케이션 구조 개선
    - ♻️ 코드 재사용성 증가
    - 🛠️ 유지보수성 향상
    - 📈 개발 생산성 증대 (특히 Spring 프레임워크)