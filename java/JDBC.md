# 🔍 JDBC 소개

JDBC(Java Database Connectivity)는 자바 애플리케이션이 관계형 데이터베이스와 상호작용할 수 있게 해주는 표준 API입니다.

- SQL 문 실행 및 결과 처리 기능 제공
- 데이터베이스 독립적인 인터페이스 제공

## 🛠️ 주요 구성 요소

1. **DriverManager**: 데이터베이스 드라이버 로드 및 연결 생성
2. **Connection**: 특정 데이터베이스와의 연결 표현
3. **Statement**: SQL 쿼리 실행
4. **PreparedStatement**: 미리 컴파일된 SQL 문 실행
5. **ResultSet**: 데이터베이스 쿼리 결과 집합 표현

## 📌 JDBC 사용 기본 단계

1. JDBC 드라이버 로드 및 등록
2. 데이터베이스 연결 설정
3. Statement 객체 생성
4. SQL 쿼리 실행
5. 결과 처리
6. 연결 종료

## 💻 예제 코드

```java
Class.forName("oracle.jdbc.OracleDriver");
Connection con = DriverManager.getConnection("jdbc:oracle:thin:@localhost:1521:XE", "사용자명", "비밀번호");
Statement stmt = con.createStatement();
ResultSet rs = stmt.executeQuery("SELECT * FROM Students");
while(rs.next()) {
    System.out.println(rs.getString("name"));
}
con.close();
```

## 🌟 JDBC의 장점

- 🔄 데이터베이스 독립성: 다양한 데이터베이스에서 동일 코드 실행 가능
- 🚀 데이터베이스 프로그래밍 단순화
- 🛡️ 자바의 예외 처리 등 기능 활용 가능

## 🚗 JDBC 드라이버 유형

1. JDBC-ODBC 브리지 드라이버
2. 네이티브-API 드라이버
3. 네트워크 프로토콜 드라이버
4. 씬 드라이버

> "JDBC는 자바 애플리케이션에서 데이터베이스 작업을 효율적으로 수행하고, 다양한 데이터베이스 시스템과의 호환성을 확보하는 핵심 기술이다."
