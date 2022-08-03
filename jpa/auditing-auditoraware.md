# Auditing, AuditorAware

- Spring Data는 엔티티의 생성과 변경을 감지하기 위한 기능을 제공한다.

## 기본적으로 제공하는 어노테이션

- `@EntityListeners` : 엔티티의 상태를 감지하기 위해서는 해당 어노테이션으로 Listener를 등록해야 함
- `@CreatedBy` : 엔티티 생성 시 작성자
- `@CreatedDate` : 엔티티 생성 시 시간
- `@LastModifiedBy` : 엔티티 수정 시 수정자
- `@LastModifiedDate` : 엔티티 수정 시 시간

```java
@Entity
@EntityListeners(AuditingEntityListener.class)
@Data
public class User {

	@Id
	@GeneratedValue
	private Long id;

	private String username;
	private String password;

	@CreatedBy
	private String createdBy;
	@CreatedDate
	private LocalDateTime createdAt;
	@LastModifiedBy
	private String modifiedBy;
	@LastModifiedDate
	private LocalDateTime modifiedAt;
}
```

- `@EnableJpaAuditing` : 프로젝트 메인 클래스에 JPA 감시 기능을 활성화함 (없으면 동작하지 않음)

```java
@SpringBootApplication
@EnableJpaAuditing
public class JpaTestApplication {
	public static void main(String[] args) {
		SpringApplication.run(JpaTestApplication.class, args);
	}

}
```

## AuditorAware

- `@CreatedBy`, `@LastModifiedBy`는 현재 엔티티를 생성하고 수정하는 대상이 누구인지에 대해서 알고 있어야 한다.
- AuditorAware는 애플리케이션과 상호작용하는 현재 사용자 또는 시스템이 누구인지 알리기 위해서 사용된다. 
- AuditorAware 인터페이스를 구현해 getCurrentAuditor 오버라이딩하여 현재 사용자로 식별될 데이터를 return 해주게 되면 감지될 때마다 해당 데이터로 설정되는 것을 확인할 수 있다.
- Spring Security를 사용했으면 ContextHolder에서 값을 가져와 넣을 수도 있고 본인 프로젝트에 맞게 사용하면 된다.

```java
@Component
public class UserAuditorAware implements AuditorAware<String> {

	@Override
	public Optional<String> getCurrentAuditor() {
		return Optional.of("username");
	}
}
```

## References

- [https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#auditing](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#auditing)