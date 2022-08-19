# Enumerated, AttributeConverter

```java
public enum Role {
	ADMIN, USER;
}
```

## ORDINAL

- 데이터를 enum에 정의되어있는 값들의 순서를 DB값으로 넣는다.
- 저장 공간은 효율적이지만, ENUM 값 순서가 바뀌거나 중간에 추가 또는 삭제하기 까다롭다.
- 참고로 Enumerated를 설정해주지 않으면 ORDINAL과 동일하게 동작한다.

```java
@Entity
public class User {

	@Id @GeneratedValue
	private Long id;

	@Enumerated(EnumType.ORDINAL)
	private Role role;

	... constructors
}
```

![ordinal](https://user-images.githubusercontent.com/50051656/185636718-bf16114e-9d9e-471b-ba85-51767b6a50c2.png)

## STRING

- 데이터를 enum에 정의되어있는 값 그 자체를 DB값으로 넣는다.
- 저장 공간이 비효율적이지만, 언제든지 ENUM 값의 추가와 삭제에 자유롭다.
- 요즘은 DB가 워낙 좋아져 이 방식을 이용한다고 해서 크게 문제 되지 않고 상황에 따라 대처하면 된다.

```java
@Entity
public class User {

	@Id @GeneratedValue
	private Long id;

	@Enumerated(EnumType.STRING)
	private Role role;

	... constructors
}
```

![string](https://user-images.githubusercontent.com/50051656/185636711-ff8720ae-9660-4f14-b045-3774c1f631e0.png)

## AttributeConverter

- 컨버터를 구현해서 컬럼과 연결해주면 DB -> Enum, Enum -> DB 데이터를 자유롭게 설정할 수 있다.

```java
@Getter
public enum Role {
    
	ADMIN("관리자", "3"),
	USER("사용자", "1");

	private String key;
	private String value;

	Role(String key, String value) {
		this.key = key;
		this.value = value;
	}

	public static Role from(String value) {
		return Arrays.stream(Role.values())
			.filter(v -> value.equals(v.value))
			.findAny()
			.orElseThrow();
	}
}
```

- convertToDatabaseColumn : Enum -> db data
- convertToEntityAttribute : db data -> Enum

```java
@Converter
public class RoleConverter implements AttributeConverter<Role, String> {

	@Override
	public String convertToDatabaseColumn(Role attribute) {
		return attribute.getValue();
	}

	@Override
	public Role convertToEntityAttribute(String dbData) {
		return Role.from(dbData);
	}
}
```

```java
public class User {

    ...

	@Convert(converter = RoleConverter.class)
	private Role role;
}
```

![converter](https://user-images.githubusercontent.com/50051656/185641631-6415f8a1-509d-4e11-8022-86e606856873.png)

## References

- [https://techblog.woowahan.com/2600/](https://techblog.woowahan.com/2600/)