# WITH절과 CTE (Common Table Expression)
## 📌 기본 개념

- WITH절을 사용하여 정의되는 임시 명명 결과 집합
- 하나의 쿼리 내에서만 존재하고 해당 쿼리 실행 동안만 유효
- SELECT, INSERT, UPDATE, DELETE, MERGE 문에서 사용 가능

## 🖋️ 구문

```sql
WITH cte_name [(column_name [,...])] AS (
    CTE_query_definition
)
SELECT * FROM cte_name;

```

## 🌟 주요 특징

### 1. 📖 가독성 향상

- 복잡한 쿼리를 논리적 부분으로 나누어 이해하기 쉽게 만듦

### 2. ♻️ 재사용성

- 동일한 쿼리 내에서 여러 번 참조 가능

### 3. 🎭 중첩 가능

- 여러 CTE를 정의하고 서로 참조 가능

### 4. 🌳 재귀적 쿼리

- 재귀 CTE를 사용하여 계층적 데이터를 쉽게 처리

## 💡 사용 예시

### 기본 CTE 사용

```sql
WITH sales_summary AS (
    SELECT product_id, SUM(quantity) AS total_sales
    FROM sales
    GROUP BY product_id
)
SELECT p.product_name, s.total_sales
FROM products p
JOIN sales_summary s ON p.product_id = s.product_id;

```

### 재귀적 CTE 사용

```sql
WITH RECURSIVE employee_hierarchy AS (
    SELECT employee_id, manager_id, name, 1 AS level
    FROM employees
    WHERE manager_id IS NULL
    UNION ALL
    SELECT e.employee_id, e.manager_id, e.name, eh.level + 1
    FROM employees e
    JOIN employee_hierarchy eh ON e.manager_id = eh.employee_id
)
SELECT * FROM employee_hierarchy;

```

## 🚀 CTE의 장점

- 복잡한 쿼리를 더 관리하기 쉽고 이해하기 쉬운 형태로 작성 가능
- 재귀적 쿼리나 계층적 데이터 처리에 매우 유용
- 코드의 모듈화와 유지보수성 향상

## 📊 활용 분야

- 데이터 분석 및 리포팅
- 계층 구조 데이터 처리 (예: 조직도, 카테고리 구조)
- 복잡한 비즈니스 로직 구현

## 🔑 주의사항

- 과도한 사용은 쿼리 복잡성 증가 가능
- 성능 최적화 시 실행 계획 확인 필요
- 데이터베이스 시스템에 따라 지원 여부와 구문이 다를 수 있음