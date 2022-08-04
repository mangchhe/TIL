# Entity Lifecycle Events

- 엔티티를 대상으로 생명 주기 동안 발생할 수 있는 이벤트를 리스너를 이용해 처리할 수 있다.

## 종류

- `@PrePersist` : 영속성 컨텍스트에서 관리되기 직전에 호출된다.
- `@PreUpdate` : 엔티티를 수정하기 직전에 호출된다.
- `@PreRemove` : 엔티티를 삭제하기 직전에 호출된다.
- `@PostPersist` : 영속성 컨텍스트에서 엔티티를 관리하기 시작한 직후에 호출된다.
- `@PostUpdate` : 엔티티를 수정하고 난 후 호출된다.
- `@PostRemove` : 엔티티를 삭제한 후 호출된다.
- `@PostLoad` : 엔티티가 로드된 후에 호출된다. (조회된 직후)

## 구현

### 리스너 구현 후 적용

- Listener를 생성한 후 생명주기 이벤트 로직을 구현한다.
- 이때 매개변수로 엔티티 타입을 반드시 적어줘야 한다.

```java
@Slf4j
public class CustomAuditListener {

	@PrePersist
	private void prePersist(User user) {
		log.info("PrePersist");
	}

	@PreUpdate
	private void preUpdate(User user) {
		log.info("PreUpdate");
	}

	@PreRemove
	private void preRemove(User user) {
		log.info("PreRemove");
	}

	@PostPersist
	private void postPersist(User user) {
		log.info("postPersist");
	}

	@PostUpdate
	private void postUpdate(User user) {
		log.info("postUpdate");
	}

	@PostRemove
	private void postRemove(User user) {
		log.info("postRemove");
	}

	@PostLoad
	private void postLoad(User user) {
		log.info("postLoad");
	}
}
```

- 엔티티 리스너로 구현했던 커스텀 리스너를 등록하게 되면 엔티티 생명주기를 감지해 이벤트를 발생시킨다.
- `@EntityListeners(CustomAuditListener.class)`

```java
@Entity
@EntityListeners(CustomAuditListener.class)
@Data
public class User {

	@Id
	@GeneratedValue
	private Long id;
}
```

### 엔티티에 직접 구현

```java
@Entity
@Data
public class User {

	@Id
	@GeneratedValue
	private Long id;

	@PrePersist
	private void prePersist() {
		log.info("PrePersist");
	}

	@PreUpdate
	private void preUpdate() {
		log.info("PreUpdate");
	}

	@PreRemove
	private void preRemove() {
		log.info("PreRemove");
	}

	@PostPersist
	private void postPersist() {
		log.info("postPersist");
	}

	@PostUpdate
	private void postUpdate() {
		log.info("postUpdate");
	}

	@PostRemove
	private void postRemove() {
		log.info("postRemove");
	}

	@PostLoad
	private void postLoad() {
		log.info("postLoad");
	}
}
```

## References

- [https://www.baeldung.com/jpa-entity-lifecycle-events](https://www.baeldung.com/jpa-entity-lifecycle-events)
- [https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.entity-persistence.saving-entites](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.entity-persistence.saving-entites)