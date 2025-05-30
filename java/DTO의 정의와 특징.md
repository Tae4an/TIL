# DTO의 정의와 특징
1. 📦 개념
    - 🔄 계층 간 데이터 전달용 객체
    - 🧼 순수한 데이터 객체 (비즈니스 로직 미포함)
    - 🔑 주로 getter와 setter 메서드만 보유
2. 🎯 용도
    - 🌐 여러 계층 간 데이터 교환
    - 🖥️ View와 Controller 간 활용
    - 💾 데이터베이스 통신 및 소켓 메시지 구문 분석
3. 📂 패키지 위치
    - 📁 dto 패키지 내 배치
    - 🎛️ 컨트롤러나 서비스 계층에서 주로 사용
4. 🛠️ 구현 방식
    - 🔄 가변 객체: setter 메서드 포함
    - 🔒 불변 객체: 생성자를 통한 초기화, getter 메서드만 제공
5. 🆚 다른 객체와의 비교
    - 📊 VO(Value Object): DTO는 가변 가능, VO는 불변
    - 🗄️ Entity: DTO는 View 계층, Entity는 DB 계층 사용
6. ⚠️ 주의사항
    - 🚫 비즈니스 로직 포함 금지
    - 🔄 필요시 toEntity() 메서드로 Entity 변환 가능
7. 💡 역할
    - 🔀 계층 간 효율적 데이터 전달 관리
    - 🧩 애플리케이션 계층 책임 명확화