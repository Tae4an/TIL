# MyBatis 동적 SQL 태그
## ⚡ 개요

- MyBatis의 동적 SQL은 조건에 따라 유연하게 SQL 쿼리를 생성하는 기능.
- XML 태그를 활용하여 SQL의 동적 생성을 지원하며, 이를 통해 코드의 유지보수성과 재사용성이 향상된다.

## 🏷️ 주요 태그 설명

### 1️⃣ IF 태그

- **역할**: 조건부 SQL 구문 포함
- **특징**: 단일 조건문 처리에 적합
- **사용 예시**:

    ```xml
    <if test="name != null">
      AND name = #{name}
    </if>
    ```


### 2️⃣ CHOOSE-WHEN-OTHERWISE 태그

- **역할**: 다중 조건 분기 처리
- **특징**: Java의 switch-case문과 유사한 동작
- **사용 예시**:

    ```xml
    <choose>
      <when test="type == 'admin'">
        AND role = 'ADMIN'
      </when>
      <when test="type == 'user'">
        AND role = 'USER'
      </when>
      <otherwise>
        AND role = 'GUEST'
      </otherwise>
    </choose>
    ```


### 3️⃣ WHERE 태그

- **역할**: WHERE 절 자동 처리
- **특징**: 조건의 유무에 따른 WHERE 키워드 자동 추가/제거
- **사용 예시**:

    ```xml
    <where>
      <if test="name != null">
        name = #{name}
      </if>
      <if test="age != null">
        AND age = #{age}
      </if>
    </where>
    ```


### 4️⃣ TRIM 태그

- **역할**: SQL 구문의 접두사/접미사 제어
- **주요 속성**:
    - ⭐ prefix: 구문 앞에 추가할 문자열
    - ⭐ suffix: 구문 뒤에 추가할 문자열
    - ⭐ prefixOverrides: 제거할 접두사
    - ⭐ suffixOverrides: 제거할 접미사
- **사용 예시**:

    ```xml
    <trim prefix="WHERE" prefixOverrides="AND |OR ">
      ...
    </trim>
    ```


### 5️⃣ SET 태그

- **역할**: UPDATE 구문의 SET 절 처리
- **특징**: 불필요한 쉼표 자동 제거
- **사용 예시**:

    ```xml
    <set>
      <if test="name != null">
        name = #{name},
      </if>
      <if test="age != null">
        age = #{age},
      </if>
    </set>
    ```


### 6️⃣ FOREACH 태그

- **역할**: 컬렉션 요소 반복 처리
- **주요 속성**:
    - 📌 collection: 반복 대상 컬렉션명
    - 📌 item: 현재 요소 변수명
    - 📌 index: 현재 인덱스 변수명
    - 📌 open: 시작 문자열
    - 📌 close: 종료 문자열
    - 📌 separator: 구분자
- **사용 예시**:

    ```xml
    <foreach collection="list" item="id" open="(" separator="," close=")">
      #{id}
    </foreach>
    ```


## 💡 활용 효과

1. 코드의 가독성 향상
2. SQL 쿼리 생성의 유연성 확보
3. 유지보수 용이성 증대
4. 반복적인 SQL 작성 감소
5. 동적 조건 처리의 편의성

## 🔍 주의사항

- 태그의 중첩 사용 시 구조 파악 필요
- 조건문의 논리적 오류 주의
- 성능을 고려한 적절한 태그 선택