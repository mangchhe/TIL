# `@Profile`, `@ActiveProfiles`

## `@Profile`

- 하나 이상의 profile을 가질 때 특정 profile의 Bean으로만 등록시키고자 할 때 사용한다.

```java
public class DBConfig {

	private String url;
	private String driver;
	private String username;
	private String password;
}
```

- profile이 mysql일 때는 mysqlConfig, postgres일 때는 postgresConfig가 동작하여 Bean으로 등록된다.
- 다양한 표현식을 제공한다.
    - `"!mysql"` : profile이 mysql이 아닐 때
    - `{"mysql", "postgres"}` : profile이 mysql이거나 postgres일 때
    - `"!mysql & !postgres"` : profile이 mysql과 postgres가 아닐 때

```java
@Configuration
public class DBConfiguration {

	@Profile("mysql")
	@Bean
	public DBConfig mysqlConfig() {
		DBConfig dbConfig = new DBConfig();
		dbConfig.setUrl("mysql://");
		dbConfig.setDriver("mysql");
		dbConfig.setUsername("mysqlUsername");
		dbConfig.setPassword("mysqlPassword");
		return dbConfig;
	}

	@Profile("postgres")
	@Bean
	public DBConfig postgresConfig() {
		DBConfig dbConfig = new DBConfig();
		dbConfig.setUrl("postges://");
		dbConfig.setDriver("postges");
		dbConfig.setUsername("postgesUsername");
		dbConfig.setPassword("postgesPassword");
		return dbConfig;
	}
}
```

## `@ActiveProfiles`

- 테스트 환경에서 profile을 설정할 때 사용한다.
- `@ActiveProfiles("mysql")`이면 profile이 mysql으로 설정되어 테스트를 진행한다.

```java
@SpringBootTest
// @ActiveProfiles("mysql")
@ActiveProfiles("postgres")
class AppConfigTest {

}
```

## Reference

- [https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Profile.html](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Profile.html)