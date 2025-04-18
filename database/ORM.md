# ORM(Object-Relational Mapping)
## 정의 📚

ORM은 객체 지향 프로그래밍의 객체와 관계형 데이터베이스의 데이터를 자동으로 매핑해주는 기술

```mermaid
graph LR
    A[객체] <--> B[ORM] <--> C[관계형 DB]

```

## 목적 🎯

- 객체 지향 프로그래밍과 관계형 데이터베이스 간의 패러다임 불일치 해결
- 개발자가 SQL 쿼리 대신 객체 지향적인 코드로 데이터베이스를 조작할 수 있게 함

## 장점 👍

1. **직관적인 비즈니스 로직** 💡
    - 객체 지향적인 코드로 작성 가능
2. **재사용성 및 유지보수성** 🔄
    - 코드의 재사용 및 유지보수가 용이
3. **DBMS 종속성 감소** 🔓
    - 다양한 데이터베이스 시스템에 대한 추상화 제공
4. **생산성 향상** ⚡
    - SQL 작성 시간 감소 및 객체 중심의 개발 가능

## 단점 👎

1. **성능 이슈** 🐢
    - 복잡한 쿼리의 경우 성능 저하 가능성
2. **학습 곡선** 📈
    - 초기 학습에 시간 투자 필요
3. **세밀한 제어의 어려움** 🎛️
    - 복잡한 쿼리나 성능 최적화가 필요한 경우 제어가 어려울 수 있음

## 대표적인 ORM 프레임워크 🌟

| 언어 | ORM 프레임워크 |
| --- | --- |
| Java | Hibernate, JPA |
| Python | SQLAlchemy, Django ORM |
| .NET | Entity Framework |

## SQL Mapper와의 차이 🔍

| ORM | SQL Mapper |
| --- | --- |
| 객체와 테이블을 자동으로 매핑 | SQL을 직접 작성하고 결과를 객체에 매핑 |
| 높은 추상화 수준 | 낮은 추상화 수준 |
| 객체 중심 | SQL 중심 |

## ORM 사용 예시 (Java with JPA) 💻

```java
@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;

    private String name;
    private String email;

    // getters and setters
}

// 사용 예
User user = new User();
user.setName("John Doe");
user.setEmail("john@example.com");

entityManager.persist(user); // 데이터베이스에 저장

```

## ORM 선택 시 고려사항 🤔

1. **프로젝트 복잡도**
    - 단순한 CRUD 작업이 대부분인 경우 ORM이 유리
    - 복잡한 쿼리가 많은 경우 SQL Mapper나 직접 SQL 작성 고려
2. **팀의 기술 스택**
    - ORM에 익숙한 팀원이 많은 경우 도입 용이
    - SQL에 능숙한 팀원이 많다면 SQL Mapper도 고려
3. **성능 요구사항**
    - 고도의 성능 최적화가 필요한 경우 ORM 사용에 주의 필요
4. **개발 생산성**
    - 빠른 개발이 필요한 경우 ORM이 유리할 수 있음

## 결론 💡

- ORM은 객체 지향 프로그래밍과 관계형 데이터베이스 사이의 간극을 효과적으로 해소해주는 기술이다.
- 개발 생산성을 크게 향상시킬 수 있지만, 성능과 세밀한 제어가 필요한 상황에서는 주의가 필요하다.