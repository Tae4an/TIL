## 1. 🛠 Jasypt 설정 (JasyptConfig.java)

```java
@Configuration
@EnableEncryptableProperties
public class JasyptConfig {
    private static final String KEY = "mykey";
    private static final String ALGORITHM = "PBEWithMD5AndDES";

    @Bean("jasyptStringEncryptor")
    public StringEncryptor stringEncryptor() {
        PooledPBEStringEncryptor encryptor = new PooledPBEStringEncryptor();
        SimpleStringPBEConfig config = new SimpleStringPBEConfig();
        config.setPassword(KEY);
        config.setAlgorithm(ALGORITHM);
// ... (기타 설정)
        encryptor.setConfig(config);
        return encryptor;
    }
}
```

## 2. 📊 암호화된 데이터 사용 (application.yml)

```yaml
datasource:
  username: ENC(9L1Pn8gfE2k9tCRoqsn0KA==)
  password: ENC(O9wmREUiJMK9npIJuWNWQg==)
```

## 3. 🔒 암호화 프로세스

1. 📝 평문 준비: 암호화할 텍스트 (예: 데이터베이스 비밀번호)
2. 🔑 암호화 키 선택: `mykey` (설정에서 정의)
3. 🧮 알고리즘 선택: `PBEWithMD5AndDES` (설정에서 정의)
4. 🔐 암호화 실행: Jasypt 라이브러리 또는 온라인 도구 사용
5. 📋 결과 사용: `ENC(암호화된_텍스트)` 형식으로 설정 파일에 저장

## 4. 🔓 복호화 프로세스

1. 🔍 암호화된 텍스트 확인: `ENC()` 형식의 텍스트
2. 🔑 복호화 키 사용: 암호화에 사용된 것과 동일한 키
3. 🧮 알고리즘 적용: 암호화에 사용된 것과 동일한 알고리즘
4. 🔓 복호화 실행: Spring Boot 애플리케이션이 자동으로 수행
5. 📋 결과 사용: 애플리케이션에서 평문으로 사용

## 5. 🌐 온라인 Jasypt 암호화/복호화 도구 사용법

1. 🔗 https://www.devglan.com/online-tools/jasypt-online-encryption-decryption 접속
2. 📝 "Input Text" 필드에 암호화할 텍스트 입력
3. 🔑 "Secret Key" 필드에 암호화 키 입력 (예: `mykey`)
4. 🧮 "Algorithm" 드롭다운에서 `PBEWithMD5AndDES` 선택
5. 🔢 "Iteration Count"를 1000으로 설정 (설정과 일치)
6. 🔐 "Encrypt" 버튼 클릭하여 암호화 수행
7. 📋 생성된 암호화 텍스트를 `ENC()` 형식으로 설정 파일에 사용

## 6. 💡 주요 고려사항

1. 🔒 보안성: 암호화 키는 절대 소스 코드나 공개 저장소에 포함시키지 않음
2. 🔄 키 관리: 환경 변수나 별도의 보안 저장소를 통해 키 관리
3. 🏗 개발 환경: 개발 시 평문 사용, 운영 환경에서만 암호화 적용 고려
4. 🔍 디버깅: 암호화된 값의 복호화가 필요할 경우 온라인 도구 활용

## 7. 🚀 장점

- 📊 설정 파일의 보안 강화
- 🔄 환경별 설정 용이 (개발, 테스트, 운영)
- 🛡 중요 정보 노출 방지

이렇게 Jasypt를 활용하면 애플리케이션의 중요 설정 정보를 안전하게 관리할 수 있다. 온라인 도구를 통해 쉽게 암호화/복호화를 테스트할 수 있으며, 실제 운영 환경에서는 보안을 위해 암호화된 값을 사용하는 것이 좋다.