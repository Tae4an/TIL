# ON CASCADE
## 📌 정의

ON CASCADE는 SQL에서 외래 키(foreign key) 제약 조건과 함께 사용되는 옵션으로, 주로 데이터의 일관성을 유지하기 위해 사용된다.

## 🔑 주요 특징

### 1. ON DELETE CASCADE

- 부모 테이블 레코드 삭제 시 관련 자식 테이블 레코드 자동 삭제
- 데이터 일관성 유지 및 고아 레코드 방지

### 2. ON UPDATE CASCADE

- 부모 테이블 기본 키 업데이트 시 관련 자식 테이블 외래 키 자동 업데이트
- 데이터 일관성 및 참조 무결성 보장

## ⚠️ 사용 시 주의사항

- 데이터 손실 위험으로 신중한 사용 필요
- ON DELETE CASCADE: 의도치 않은 대규모 데이터 삭제 가능성

## 🔄 트리거와의 관계

- CASCADE 옵션 설정 테이블에 INSTEAD OF 트리거 생성 불가

## 🚀 성능 고려사항

- CASCADE 작업: 추가 데이터베이스 연산 필요로 성능 영향 가능성

## 🔀 대안적 옵션

| 옵션 | 설명 |
| --- | --- |
| ON DELETE SET NULL | 부모 레코드 삭제 시 자식 레코드 외래 키를 NULL로 설정 |
| ON DELETE NO ACTION | 기본 동작, 자식 레코드 존재 시 부모 레코드 삭제 불가 |

## 🏗️ 데이터베이스 설계

- 설계 단계에서 신중한 고려 필요
- 비즈니스 로직과 데이터 무결성 요구사항에 맞는 적절한 사용

## 💡 요약

ON CASCADE 옵션은 데이터베이스의 참조 무결성을 자동으로 유지하는 강력한 도구이지만, 잠재적인 위험성이 있어 신중한 사용이 요구됨.

## 📚 예제 코드

```sql
CREATE TABLE departments (
    dept_id INT PRIMARY KEY,
    dept_name VARCHAR(50)
);

CREATE TABLE employees (
    emp_id INT PRIMARY KEY,
    emp_name VARCHAR(50),
    dept_id INT,
    FOREIGN KEY (dept_id)
    REFERENCES departments(dept_id)
    ON DELETE CASCADE
    ON UPDATE CASCADE
);

```

이 예제에서 부서가 삭제되면 해당 부서의 모든 직원 records도 자동으로 삭제됨.