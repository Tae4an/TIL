# SQL 조인 총정리: Equi Join, Non-Equi Join, Self Join, Outer Join

---

## 🔹 1. Equi Join (이큐조인) 🟰

### 📌 정의
- 두 테이블 간 **칼럼 값이 정확히 일치하는 경우** 사용하는 조인
- 주로 **기본키(PK)-외래키(FK)** 관계에 기반
- **등호(=)** 연산자 사용
- **INNER JOIN의 기본 형태**

### 💻 예시

```sql
SELECT e.name, d.department_name
FROM employee e, department d
WHERE e.department_id = d.department_id;
```

### ✅ 특징
- 가장 일반적으로 사용되는 조인
- 인덱스 활용 가능 → **성능 우수**
- SQL 표준에서 `INNER JOIN`으로 표현 가능

---

## 🔹 2. Non-Equi Join (논이큐조인) 🔢

### 📌 정의
- 두 테이블의 값이 **정확히 일치하지 않을 때** 사용하는 조인
- **BETWEEN, >, <, >=, <= 등** 비교 연산자 사용
- **범위 기반 매칭**에 적합

### 💻 예시

```sql
SELECT e.name, e.salary, s.grade
FROM employee e, salary_grade s
WHERE e.salary BETWEEN s.min_salary AND s.max_salary;
```

### ✅ 특징
- **급여 등급 매칭**, **시간대별 조건 분석** 등에 적합
- 전체 테이블 스캔 발생 가능 → **성능 주의 필요**
- 일부 DBMS에서 최적화 어려울 수 있음

---

## 🔹 3. Self Join (셀프조인) 🔄

### 📌 정의
- **자기 자신과 조인하는 방식**
- 같은 테이블을 두 번 사용하는 것처럼 alias(별칭)를 활용
- **계층 구조 또는 내부 비교 분석**에 사용

### 💻 예시

```sql
SELECT e.name AS employee, m.name AS manager
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.employee_id;
```

### ✅ 활용 시나리오
- **직원-관리자 관계**
- **연속 날짜 비교**
- **카테고리/조직도 구조 분석**
- **그래프 탐색 (노드 간 관계)**

### ✅ 장단점
- 복잡한 관계 표현에 유리
- 쿼리 복잡성 증가 가능성 있음
- 인덱스 및 서브쿼리 최적화 필요

---

## 🔹 4. Outer Join (아우터조인) 🌐

### 📌 정의
- 조인 조건을 **만족하지 않는 데이터도 결과에 포함**하는 방식
- 일치하지 않는 테이블의 값은 **NULL로 대체**

### 🔸 아우터조인의 종류

| 유형 | 설명 |
|------|------|
| **LEFT OUTER JOIN** ⬅️ | 왼쪽 테이블의 모든 행 + 오른쪽의 일치하는 행 (없으면 NULL) |
| **RIGHT OUTER JOIN** ➡️ | 오른쪽 테이블의 모든 행 + 왼쪽의 일치하는 행 (없으면 NULL) |
| **FULL OUTER JOIN** ↔️ | 양쪽 테이블의 모든 행 포함, 일치하지 않는 쪽은 NULL |

### 💻 예시 (LEFT OUTER JOIN)

```sql
SELECT c.customer_name, o.order_id
FROM customers c
LEFT OUTER JOIN orders o ON c.customer_id = o.customer_id;
```

👉 고객이 주문을 하지 않았더라도 모든 고객을 결과에 포함

---

## 🔸 LEFT vs RIGHT OUTER JOIN 비교

| 항목 | LEFT JOIN | RIGHT JOIN |
|------|-----------|------------|
| 기준 테이블 | 왼쪽(첫 번째) 테이블 | 오른쪽(두 번째) 테이블 |
| 포함 범위 | 왼쪽 테이블의 전체 행 | 오른쪽 테이블의 전체 행 |
| NULL 발생 위치 | 오른쪽 테이블 | 왼쪽 테이블 |
| 사용 빈도 | 더 자주 사용됨 | 덜 사용됨 |
| 대체 가능성 | 대부분 RIGHT JOIN → LEFT JOIN으로 변환 가능 |

---

## 🔸 OUTER JOIN 사용 시 팁 💡

- **NULL 처리**: `COALESCE()` 또는 `IFNULL()`로 가독성과 결과 향상
- **인덱스 사용**: 조인 키에 인덱스를 적용하여 성능 개선
- **조인 순서 최적화**: 큰 테이블을 왼쪽에 두는 것이 일반적
- **쿼리 실행 계획 확인**: `EXPLAIN` 활용

---

## 🔚 전체 요약 비교표

| 조인 유형 | 매칭 방식 | 주요 용도 | NULL 처리 | 성능 고려 |
|-----------|------------|-----------|------------|--------------|
| **Equi Join** | 정확한 값 일치 | 관계형 테이블 연결 | 없음 | 인덱스 활용, 빠름 |
| **Non-Equi Join** | 범위/조건 기반 | 급여, 시간대, 등급 | 없음 | 범위 검색 → 느릴 수 있음 |
| **Self Join** | 자기 자신과의 조인 | 계층 구조, 내부 비교 | 필요에 따라 | 복잡한 조건 최적화 필요 |
| **Outer Join** | 조건 불일치 포함 | 누락된 정보까지 포함 | 필수 | NULL 처리, 대용량 주의 |

---

## 🎯 조인 선택 가이드

- **정확한 관계 매칭** → Equi Join
- **범위 기반 비교** → Non-Equi Join
- **자기 자신과 비교/계층 표현** → Self Join
- **모든 데이터 포함 + 관계 분석** → Outer Join (LEFT JOIN이 가장 일반적)

---