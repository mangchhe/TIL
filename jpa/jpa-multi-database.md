# JPA Multi Database 설정

## 데이터베이스 준비

```bash
# DB1
docker run --name multi-mysql -e MYSQL_ROOT_PASSWORD=1234 -d -p 3307:3306 mysql
docker exec -it multi-mysql bash
mysql -uroot -p
create database test;

# DB2
docker run --name multi-postgres -e POSTGRES_PASSWORD=1234 -d -p 3308:5432 postgres
psql -U postgres
create database test;
```

## 패키지 구조

- 서로 다른 데이터베이스 설정 클래스를 두고 각 DB가 가리킬 엔티티와 저장소를 구분하여 위치하게 하였다.

![package-structure](https://user-images.githubusercontent.com/50051656/188316853-6a2e1ebd-0687-430b-b11e-3931fe63bbe0.png)

## YML

```yml
spring:
  datasource-mysql:
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://localhost:3307/test
    username: root
    password: 1234

    jpa:
      ddl-auto: create-drop
	  
  datasource-postgres:
    driver-class-name: org.postgresql.Driver
    url: jdbc:postgresql://localhost:3308/test
    username: postgres
    password: 1234

    jpa:
      ddl-auto: create-drop
```

## Mysql 설정 클래스

- `@EnableJpaRepositories`
    - basePackages : JPA 저장소 패키지 위치 지정
    - entityManagerFactoryRef : EntityManager Bean 이름
    - transactionManagerRef : TransactionManager Bean 이름
- `@Primary` : 트랜잭션 관리자 이름을 지정하지 않았을 때 암묵적으로 Mysql 설정한 빈을 주입하도록 지정한다.
- LocalContainerEntityManagerFactoryBean : 스프링에서 제공하는 컨테이너, EntityManager를 위한 EntityManagerFactory 생성
- DataSource : 물리적인 데이터와 커넥션으로 위한 팩토리이다.
- PlatformTransactionManager : TransactionManager의 최상위 인터페이스로 각자 환경에 맞는 TransactionManager를 주입한다.
- `@EnableTransactionManagement` : 트랜잭션을 활성화하는 어노테이션

```java
@Configuration
@EnableTransactionManagement
@PropertySource("classpath:application.yml")
@EnableJpaRepositories(
	basePackages = "me.hajoo.test3.repository",
	entityManagerFactoryRef = "mysqlEntityManager",
	transactionManagerRef = "mysqlTransactionManger"
)
public class MysqlConfig {

	@Autowired
	private Environment environment;

	@Primary
	@Bean
	public LocalContainerEntityManagerFactoryBean mysqlEntityManager() {
		LocalContainerEntityManagerFactoryBean em = new LocalContainerEntityManagerFactoryBean();
		em.setDataSource(mysqlDataSource());
		em.setPackagesToScan(new String[] {"me.hajoo.test3.entity"});

		HibernateJpaVendorAdapter vendorAdapter = new HibernateJpaVendorAdapter();
		em.setJpaVendorAdapter(vendorAdapter);

		HashMap<String, Object> properties = new HashMap<>();
		properties.put("hibernate.hbm2ddl.auto", environment.getProperty("spring.datasource-mysql.jpa.ddl-auto"));
		em.setJpaPropertyMap(properties);

		return em;
	}

	@Primary
	@Bean
	public DataSource mysqlDataSource() {
		DriverManagerDataSource ds = new DriverManagerDataSource();
		ds.setDriverClassName(environment.getProperty("spring.datasource-mysql.driver-class-name"));
		ds.setUrl(environment.getProperty("spring.datasource-mysql.url"));
		ds.setUsername(environment.getProperty("spring.datasource-mysql.username"));
		ds.setPassword(environment.getProperty("spring.datasource-mysql.password"));
		return ds;
	}

	@Primary
	@Bean
	public PlatformTransactionManager mysqlTransactionManger() {
		JpaTransactionManager jpaTransactionManager = new JpaTransactionManager();
		jpaTransactionManager.setEntityManagerFactory(mysqlEntityManager().getObject());
		return jpaTransactionManager;
	}
}
```

## Postgresql 설정 클래스

```java
@Configuration
@EnableTransactionManagement
@PropertySource("classpath:application.yml")
@EnableJpaRepositories(
	basePackages = "me.hajoo.test3.repository2",
	entityManagerFactoryRef = "postgresEntityManager",
	transactionManagerRef = "postgresTransactionManger"
)
public class PostgresConfig {

	@Autowired
	private Environment environment;

	@Bean
	public LocalContainerEntityManagerFactoryBean postgresEntityManager() {
		LocalContainerEntityManagerFactoryBean em = new LocalContainerEntityManagerFactoryBean();
		em.setDataSource(postgresDataSource());
		em.setPackagesToScan(new String[] {"me.hajoo.test3.entity2"});

		HibernateJpaVendorAdapter vendorAdapter = new HibernateJpaVendorAdapter();
		em.setJpaVendorAdapter(vendorAdapter);

		HashMap<String, Object> properties = new HashMap<>();
		properties.put("hibernate.hbm2ddl.auto", environment.getProperty("spring.datasource-postgres.jpa.ddl-auto"));
		em.setJpaPropertyMap(properties);

		return em;
	}

	@Bean
	public DataSource postgresDataSource() {
		DriverManagerDataSource ds = new DriverManagerDataSource();
		ds.setDriverClassName(environment.getProperty("spring.datasource-postgres.driver-class-name"));
		ds.setUrl(environment.getProperty("spring.datasource-postgres.url"));
		ds.setUsername(environment.getProperty("spring.datasource-postgres.username"));
		ds.setPassword(environment.getProperty("spring.datasource-postgres.password"));
		return ds;
	}

	@Bean
	public PlatformTransactionManager postgresTransactionManger() {
		JpaTransactionManager jpaTransactionManager = new JpaTransactionManager();
		jpaTransactionManager.setEntityManagerFactory(postgresEntityManager().getObject());
		return jpaTransactionManager;
	}
}
```

## References

- [https://www.baeldung.com/spring-data-jpa-multiple-databases](https://www.baeldung.com/spring-data-jpa-multiple-databases)