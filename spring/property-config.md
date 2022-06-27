# Configuration 값 가져오는 방법

## `@Value`

- `@Value`를 사용하여 설정값을 가져온다.

```yml
info:
  name: joohyun
  age: 27
  hobby: movie, bowling, webtoon
```

- Bean으로 등록 후 `@Value` 값으로 `${가져오려는 property path}`를 넣고 계층은 .을 구분자로 사용해 작성하여 주입하면 된다.
- 값이 여러 개일 경우 List로 받을 수 있고 (Set도 가능), 정수나 실수형일 때 String 외에 Integer, Long, Float, Double 다 가능하다.

```java
@Component
public class AppConfig {

	@Value("${info.name}")
	private String name;

	@Value("${info.age}")
	private String age;

	@Value("${info.hobby}")
	private List<String> hobbies;

  ... getter
}
```

## `@ConfigurationProperties`

- `@ConfigurationProperties`를 사용하여 설정값을 가져온다.

```yml
info:
  name: joohyun
  age: 27
  hobbies: movie, bowling, webtoon
  address:
    country: korea
    city: seoul
```

- 의존성 추가

```yml
annotationProcessor "org.springframework.boot:spring-boot-configuration-processor"
```

- Bean으로 등록해야 하고, 계층 구조를 가진다면 prefix에 적용할 상위 타입 property 이름을 작성해주면 된다.
- .을 구분자로 계층 구조를 나타낼 수 있고 옵션으로 value와 prefix 값이 하는 역할이 동일하다. `prefix = "info"` = `value = "info"`
- 값을 넣으려고 하는 대상의 setter가 없다면 BindException이 발생하므로 setter는 필수다.
	- Exception이 아니라 무시하기 위해서는 `ignoreInvalidFields` 옵션을 true로 주게 되면 예외가 발생하지 않는다.

```java
@Component
@ConfigurationProperties(prefix = "info")
public class AppConfig {

	private String name;

	private String age;

	private List<String> hobbies;

	private Map<String, String> address;

	... getter, setter
}
```