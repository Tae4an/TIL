# JSON Object와 JSON Array
## JSONObject

🔹 구조: 중괄호 {}로 둘러싸인 키-값 쌍의 집합
🔹 특징:

- 각 키-값 쌍은 콜론(:)으로 구분
- 여러 키-값 쌍은 쉼표(,)로 구분
- 순서가 중요하지 않은 데이터 표현에 사용
  🔹 예시:

```json
{
  "name": "John",
  "age": 30
}
```

## JSONArray

🔸 구조: 대괄호 []로 둘러싸인 값들의 순서가 있는 목록
🔸 특징:

- 배열의 각 요소는 쉼표(,)로 구분
- 순서가 중요한 데이터 표현에 사용
- 다양한 타입의 요소 포함 가능 (문자열, 숫자, 객체 등)
  🔸 예시:

```json
["apple", "banana", "orange"]
```

## 🔍 주요 차이점

1. **구조**
    - JSONObject: 키-값 구조
    - JSONArray: 순서가 있는 목록 구조
2. **데이터 접근**
    - JSONObject: 키를 사용하여 값에 접근
    - JSONArray: 인덱스를 사용하여 값에 접근
3. **사용 목적**
    - JSONObject: 관련된 데이터 그룹화
    - JSONArray: 유사한 객체나 값들의 나열
4. **중첩 구조**
    - JSONObject와 JSONArray는 서로를 포함할 수 있어 복잡한 데이터 구조 표현 가능