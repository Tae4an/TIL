# 자바 애너테이션
`자바 애너테이션(java annotaion)`은 자바로 작성한 코드에 추가하는 표식을 말한다.
보통 애너테이션은 @ 기호를 사용하며 JDK 1.5 버전부터 사용할 수 있다. 애너테이션은 다양한 목적으로 사용하지만 보통은 메타 데이터로 사용하는 경우가 가장 많다. 예를 들면 다음과 같은 애너테이션이 대표적인 자바 코드에서 흔하게 볼 수 있는 예다.

## 애너테이션 정리

### Lombok 애너테이션
- `@Data`: 클래스에 대해 getter, setter, toString, equals, hashCode 메서드를 자동으로 생성한다.
- `@AllArgsConstructor`: 모든 필드를 포함한 생성자를 생성한다.
- `@NoArgsConstructor`: 매개변수가 없는 기본 생성자를 생성한다.
- `@Builder`: 빌더 패턴 클래스를 생성한다.
- `@RequiredArgsConstructor`: final 필드 또는 @NonNull 필드를 포함한 생성자를 생성한다.
- `@Getter`: 필드에 대해 getter 메서드를 생성한다.
- `@Setter`: 필드에 대해 setter 메서드를 생성한다.
- `@Slf4j`: 로깅을 위한 로거 필드를 생성한다.

### Spring 애너테이션
- `@Component`: 스프링의 빈으로 등록되는 클래스임을 나타낸다.
- `@Service`: 서비스 계층의 클래스를 나타내며, `@Component`와 동일한 기능을 한다.
- `@Repository`: 데이터 접근 계층의 클래스를 나타내며, `@Component`와 동일한 기능을 한다.
- `@Controller`: 컨트롤러 계층의 클래스를 나타내며, `@Component`와 동일한 기능을 한다.
- `@RestController`: RESTful 웹 서비스를 위한 컨트롤러 클래스를 나타내며, `@Controller`와 `@ResponseBody`를 합친 것이다.
- `@RequestMapping`: HTTP 요청을 특정 메서드에 매핑한다.
- `@PostMapping`: HTTP POST 요청을 특정 메서드에 매핑한다.
- `@PutMapping`: HTTP PUT 요청을 특정 메서드에 매핑한다.
- `@DeleteMapping`: HTTP DELETE 요청을 특정 메서드에 매핑한다.
- `@InitBinder`: 특정 컨트롤러의 요청을 처리하기 전에 바인딩 설정을 초기화한다.
- `@Autowired`: 스프링의 의존성 주입(DI)을 위해 사용된다.
- `@Qualifier`: 특정 빈을 주입할 때 사용할 빈을 명시한다.
- `@Primary`: 동일한 타입의 여러 빈이 있을 때 우선적으로 주입될 빈을 지정한다.
- `@Transactional`: 트랜잭션 처리를 위해 사용되며, 메서드 또는 클래스에 적용된다.
- `@EnableGlobalMethodSecurity`: 메서드 수준에서의 보안 설정을 활성화한다.
- `@Configuration`: 스프링 설정 파일을 나타낸다.
- `@EnableWebSecurity`: 스프링 시큐리티 설정을 활성화한다.
- `@Modifying`: 데이터베이스 쿼리 메서드에서 데이터를 수정하는 작업임을 나타낸다.
- `@Query`: 메서드에 대한 쿼리를 정의한다.

### Hibernate 애너테이션
- `@EntityListeners`: 엔티티 리스너 클래스를 지정한다.
- `@PrePersist`: 엔티티가 영속성 컨텍스트에 저장되기 전에 호출된다.
- `@PreUpdate`: 엔티티가 업데이트되기 전에 호출된다.
- `@LastModifiedDate`: 마지막 수정 날짜를 자동으로 설정한다.
- `@Column`: 데이터베이스의 컬럼을 매핑한다.
- `@Table`: 데이터베이스의 테이블을 매핑한다.
- `@OneToMany`: 일대다 관계를 매핑한다.
- `@ManyToOne`: 다대일 관계를 매핑한다.
- `@OneToOne`: 일대일 관계를 매핑한다.
- `@ManyToMany`: 다대다 관계를 매핑한다.
- `@Id`: 엔티티의 기본 키를 지정한다.
- `@GeneratedValue`: 기본 키 생성을 지정한다.
- `@JoinColumn`: 외래 키 컬럼을 지정한다.
- `@MappedSuperclass`: 매핑 정보만을 제공하는 슈퍼 클래스를 지정한다.
- `@Inheritance`: 상속 전략을 지정한다.
- `@DiscriminatorColumn`: 상속된 엔티티 간의 구분 컬럼을 지정한다.

### Bean Validation 애너테이션
- `@Pattern`: 문자열이 정규 표현식과 일치하는지 검증한다.
- `@NotBlank`: 문자열이 null이 아니고 공백이 아닌지 검증한다.
- `@NotNull`: 값이 null이 아닌지 검증한다.
- `@Size`: 컬렉션 또는 배열의 크기, 문자열의 길이를 검증한다.
- `@Min`: 값의 최소값을 검증한다.
- `@Max`: 값의 최대값을 검증한다.
- `@Email`: 이메일 형식이 유효한지 검증한다.
- `@Past`: 과거 날짜인지 검증한다.
- `@Future`: 미래 날짜인지 검증한다.
- `@Valid`: 객체의 유효성을 검증한다.

### Java 기본 애너테이션
- `@Override`: 메서드가 슈퍼 클래스의 메서드를 오버라이드하는 것임을 나타낸다.
- `@Deprecated`: 더 이상 사용되지 않는 필드, 메서드, 클래스임을 나타낸다.
- `@SuppressWarnings`: 컴파일러 경고를 억제한다.
- `@SafeVarargs`: 가변 인수를 사용하는 메서드에서 타입 안전성을 보장한다.
- `@FunctionalInterface`: 인터페이스가 함수형 인터페이스임을 나타낸다.

### Meta 애너테이션
- `@Target`: 애너테이션이 적용될 수 있는 위치를 지정한다.
- `@Retention`: 애너테이션의 보존 정책을 지정한다.
- `@Documented`: 애너테이션이 Javadoc에 포함되어야 함을 나타낸다.
- `@Inherited`: 애너테이션이 하위 클래스에 상속됨을 나타낸다.
