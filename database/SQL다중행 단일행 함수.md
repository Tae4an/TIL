# 단일행 함수 vs 다중행(그룹) 함수
## 단일행 함수 🔸

- 입력: 하나의 행 → 하나의 결과 반환 1️⃣
- 각 행마다 개별 적용 📊
- 사용 가능 위치: SELECT, WHERE, ORDER BY 절 등 🔀
- 예시: UPPER(), LOWER(), SUBSTR(), LENGTH() 🔤

## 다중행(그룹) 함수 🔹

- 입력: 여러 행 → 하나의 결과 반환 🔢
- 행 그룹에 대해 계산 수행 📈
- 주로 GROUP BY 절과 함께 사용 👥
- 사용 가능 위치: SELECT 절, HAVING 절 🔍
- 예시: SUM(), AVG(), MAX(), MIN(), COUNT() 🧮

## 주요 차이점 비교 ⚖️

| 특성 | 단일행 함수 | 다중행(그룹) 함수 |
| --- | --- | --- |
| 처리 대상 | 개별 행 | 여러 행 그룹 |
| 반환 결과 | 각 행마다 결과 | 그룹당 하나의 결과 |
| 사용 위치 | 다양한 절에서 사용 가능 | SELECT, HAVING 절로 제한 |
| 주요 목적 | 개별 데이터 처리 및 변환 | 데이터 집계 및 요약 |

## 추가 정보 💡

### 성능 고려사항

- 단일행 함수: 대량의 데이터에 적용 시 행 별로 처리하므로 성능 저하 가능성 ⚠️
- 다중행 함수: 적절한 인덱스와 함께 사용 시 대용량 데이터 처리에 효율적 🚀

### 중첩 사용

- 단일행 함수: 다른 단일행 함수 내에서 중첩 사용 가능 🎭
  예: `SELECT UPPER(SUBSTR(column_name, 1, 3)) FROM table;`
- 다중행 함수: 일반적으로 중첩 사용 불가, 단 분석 함수에서는 예외 존재 🔒

### NULL 처리

- 단일행 함수: 함수에 따라 NULL 처리 방식 다름 (e.g., NVL, COALESCE) ❓
- 다중행 함수: 대부분 NULL을 자동으로 무시 (COUNT(*) 제외) 🚫

### 응용 분야

- 단일행 함수: 데이터 정제, 형식 변환, 조건부 로직에 유용 🧹
- 다중행 함수: 리포팅, 통계 분석, 데이터 마이닝에 필수 📊

## 선택 기준 🎯

- 데이터 처리 단위: 개별 행 vs 행 그룹
- 원하는 결과 형태: 상세 데이터 vs 요약 데이터
- 쿼리의 목적: 데이터 변환 vs 데이터 집계
- 성능 요구사항: 행 별 처리 허용 vs 대량 데이터 빠른 처리 필요

적절한 함수 선택으로 효율적인 쿼리 작성과 원하는 결과 도출이 가능. 💪