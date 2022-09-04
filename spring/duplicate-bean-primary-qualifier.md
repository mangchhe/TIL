# 중복 타입 Bean 솔루션 - @Primary, @Qualifier

- 스프링에서 우리가 사용하는 대부분의 객체는 Bean으로 정의해서 하나만 생성해서 사용한다.
- 만약에 같은 타입의 객체를 Bean으로 두 번 등록하게 된다면 오류가 발생한다.
- 같은 타입의 객체라도 다른 설정을 해서 여러 개의 객체를 사용하고 싶을 때가 있는데 어떻게 해야 하는지 알아보자.

```java

@Configuration
public class ServiceConfig {
    ...
}

public class HelloService {

	private String helloMsg;

	public HelloService(String helloMsg) {
		this.helloMsg = helloMsg;
	}

	public String hello() {
		return helloMsg;
	}
}

public class HelloController {
    ...
}
```

## Bean 이름을 필드명과 일치

- Bean을 수동으로 등록하면 메서드명에 따라 Bean이 등록된다.
- `@Bean("Bean 이름")` : 값을 넣어주면 메서드 이름 말고 해당 값으로 Bean 이름이 설정된다.

```java
@Configuration
public class ServiceConfig {

	@Bean
	public HelloService helloService() {
		return new HelloService("Hello World!");
	}

	@Bean
	public HelloService helloService2() {
		return new HelloService("Hello World!!!!!!!!!!!!!!!!!!!!!");
	}
}
```

- 등록된 Bean을 확인하면 helloServoce, helloService2가 출력되는 것을 확인할 수 있다.

```java
for (String beanDefinitionName : applicationContext.getBeanDefinitionNames()) {
    if (beanDefinitionName.startsWith("hello")) {
        System.out.println(beanDefinitionName);
    }
}
```

- 이때 사용할 객체의 Bean 이름을 필드명으로 주입해주면 해당 Bean이 주입되는 것을 확인할 수 있다.

```java
@RestController
@RequiredArgsConstructor
public class HelloController {

	private final HelloService helloService;
	private final HelloService helloService2;

	@GetMapping("/hello")
	public String hello() {
		return helloService.hello();
	}

	@GetMapping("/hello2")
	public String hello2() {
		return helloService2.hello();
	}
}
```

## Qualifier

- `@Qualifier(value)`를 붙여주게 되면 같은 타입의 여러 개의 Bean을 찾았을 때 value에 따라 주입해줄 Bean을 지정할 수 있다.
- 이때 `@RequiredArgsConstructor`를 같이 사용할 수 없다. (이유는 해당 어노테이션이 생성자를 만들고 주입할 때 Qulifier를 고려하지 않음)
- 해결 방법 → [stackoverflow](https://stackoverflow.com/questions/38549657/is-it-possible-to-add-qualifiers-in-requiredargsconstructoronconstructor)

```java
@Configuration
public class ServiceConfig {

	@Bean
	@Qualifier("helloService")
	public HelloService helloService() {
		return new HelloService("Hello World!");
	}

	@Bean
	@Qualifier("helloService2")
	public HelloService helloService2() {
		return new HelloService("Hello World!!!!!!!!!!!!!!!!!!!!!");
	}
}
```

```java
@RestController
public class HelloController {
	private final HelloService service;
	private final HelloService service2;

	public HelloController(@Qualifier("helloService") HelloService service, @Qualifier("helloService2") HelloService service2) {
		this.service = service;
		this.service2 = service2;
	}

    ...
}
```

## Primary

- `@Primary`를 붙여주게 되면 같은 타입의 Bean이 여러 개 있을 때 먼저 주입되어야 할 Bean을 지정할 수 있다.
- 기본적으로 `@Primary`가 기본적으로 주입되고 필요에 따라 `@Qualifier`를 이용해 유동적으로 사용할 수 있다.

```java
@Configuration
public class ServiceConfig {

	@Bean
	@Primary
	public HelloService helloService() {
		return new HelloService("Hello World!");
	}

	@Bean
	@Qualifier("helloService2")
	public HelloService helloService2() {
		return new HelloService("Hello World!!!!!!!!!!!!!!!!!!!!!");
	}
}
```