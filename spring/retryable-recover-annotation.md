# Retryable, Recover 어노테이션 사용법

- Retryable : 예외가 발생하면 설정만큼 재시도
- Recover : 설정만큼 재시도해도 실패했다면 실행

## Dependency

- spring-aspects, starter-aop 중 택 1

```yml
implementation 'org.springframework:spring-aspects:5.3.20'
implementation 'org.springframework.boot:spring-boot-starter-aop:2.7.0'

implementation 'org.springframework.retry:spring-retry:1.3.3'
```

## Configuration

```java
@SpringBootApplication
@EnableRetry
public class TestApplication {

	public static void main(String[] args) {
		SpringApplication.run(TestApplication.class, args);
	}
}
```

## Controller

```java
@RestController
@RequiredArgsConstructor
public class TestController {

	private final TestService testService;

	@GetMapping
	public void test() {
		try {
			testService.test();
		} catch (Exception e) {}
	}
}
```

## Service

```java
@Service
public class TestService {

    /*
    value, include : 해당 Exception이 발생한다면 재시도
    exclude : 해당 Exception 발생할 시 재시도 제외
    maxAttempts : 재시도 횟수
    backoff : 재시도 시 딜레이
    */
	@Retryable(value = Exception.class, maxAttempts = 3, backoff = @Backoff(2000))
	void test() throws Exception {
		System.out.println("Hello World!");
		throw new Exception();
	}

    /*
    리턴 타입이 같
    파라미터는 선택 사항이고 첫 번째는 Throwable, 두 번째부터는 실패한 메서드의 파라미터 목록을 따라간다.
    */
    @Recover
	public void recoverTest(Exception e) {
		System.out.println(e.getMessage());
		System.out.println("Recover");
	}
}
```