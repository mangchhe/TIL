# AOP 구현에 사용되는 어노테이션 사용법

## 목차

- [@Around](#Around)
- [@Pointcut](#Pointcut)
- [@Order](#Order)
- [@Before, @AfterReturning, @AfterThrowing, @After](#그-외)

## 실습 준비

- 실습 패키지 경로 기준 : me.hajoo.aop._2_after.aop, - me.hajoo.aop._2_after.order
- 실습 저장소 : [→](https://github.com/TIL-Repo/spring-study/tree/main/AOP/Java/src)

```java
@Service
@RequiredArgsConstructor
public class OrderService {

	private final OrderRepository orderRepository;

	public void orderItem(String itemId) {
		log.info("[orderService] 실행");
		orderRepository.save(itemId);
	}
}
```

```java
@Repository
public class OrderRepository {

	public String save(String itemId) {
		log.info("[orderRepository] 실행");
		if (itemId.equals("ex")) {
			throw new IllegalStateException("예외 발생");
		}
		return " ";
	}
}
```

```java
@SpringBootTest
@Import("빈으로 등록할 클래스")
public class AopTest {

	@Autowired
	private OrderService orderService;

	@Autowired
	private OrderRepository orderRepository;

	@Test
	void aopInfo() throws Exception {
		log.info("isAopProxy, orderService = {}", AopUtils.isAopProxy(orderService));
		log.info("isAopProxy, orderRepository = {}", AopUtils.isAopProxy(orderRepository));
	}

	@Test
	void success() throws Exception {
		orderService.orderItem("itemA");
	}

	@Test
	void exception() throws Exception {
		Assertions.assertThrows(IllegalStateException.class, () -> orderService.orderItem("ex"));
	}
}
```

## Around

- AspectJ 표현식을 이용해서 프록시가 적용될 지점을 설정할 수 있다.

```java
@Aspect
public class AspectV1 {

	@Around("execution(* me.hajoo.aop._2_after.order..*(..))")
	public Object doLog(ProceedingJoinPoint joinPoint) throws Throwable {
		log.info("[log] {}", joinPoint.getSignature());
		return joinPoint.proceed();
	}
}
```

### 결과

![aopTraining](https://user-images.githubusercontent.com/50051656/175320740-ba5d98d3-c50d-4237-8a41-ca30d52c4f77.png)

## Pointcut

- 사용될 Pointcut(적용 시점)을 마치 함수처럼 사용할 수 있게 한다.

```java
public class Pointcuts {

	@Pointcut("execution(* me.hajoo.aop._2_after.order..*(..))")
	public void allOrder(){}

	@Pointcut("execution(* *..*Service.*(..))")
	public void allService(){}

	@Pointcut("allOrder() && allService()")
	public void orderAndAllService(){}
}
```

- 풀 패키지 명으로 적용할 함수를 `@Around`에 값으로 전달하면 적용된다.

```java
@Slf4j
@Aspect
public class AspectV2 {

	@Around("me.hajoo.aop._2_after.aop.Pointcuts.allOrder()")
	public Object doLog(ProceedingJoinPoint joinPoint) throws Throwable {
		log.info("[log] {}", joinPoint.getSignature());
		return joinPoint.proceed();
	}

	@Around("me.hajoo.aop._2_after.aop.Pointcuts.orderAndAllService()")
	public Object doTransaction(ProceedingJoinPoint joinPoint) throws Throwable {
		try {
			log.info("[transaction begin] {}", joinPoint.getSignature());
			Object result = joinPoint.proceed();
			log.info("[transaction end] {}", joinPoint.getSignature());
			return result;
		} catch (Exception e) {
			log.info("[transaction rollback] {}", joinPoint.getSignature());
			throw e;
		} finally {
			log.info("[release] {}", joinPoint.getSignature());
		}
	}
}
```

### 결과

#### 성공

![aopTraining2-1](https://user-images.githubusercontent.com/50051656/175320749-39b58fbf-273f-4387-aab9-c3e1c657c286.png)

#### 예외 발생

![aopTraining2-2](https://user-images.githubusercontent.com/50051656/175320754-c4834458-684c-4f28-9f1e-d0986e6e4cb7.png)

## Order

- `@Aspect`의 적용 순서를 지정할 수 있다.
- 단, 클래스 단위로만 순서를 보장하기 때문에 클래스를 따로 분리하거나 inner class를 이용해서 구현해야 한다.

```java
public class AspectV3 {

	@Aspect
	@Order(1)
	public static class LogAspect {
		@Around("me.hajoo.aop._2_after.aop.Pointcuts.allOrder()")
		public Object doLog(ProceedingJoinPoint joinPoint) throws Throwable {
			log.info("[log] {}", joinPoint.getSignature());
			return joinPoint.proceed();
		}
	}

	@Aspect
	@Order(0)
	public static class TransactionAspect {
		@Around("me.hajoo.aop._2_after.aop.Pointcuts.orderAndAllService()")
		public Object transaction(ProceedingJoinPoint joinPoint) throws Throwable {
			try {
				log.info("[transaction begin] {}", joinPoint.getSignature());
				Object result = joinPoint.proceed();
				log.info("[transaction end] {}", joinPoint.getSignature());
				return result;
			} catch (Exception e) {
				log.info("[transaction rollback] {}", joinPoint.getSignature());
				throw e;
			} finally {
				log.info("[release] {}", joinPoint.getSignature());
			}
		}
	}
}
```

### 결과

![aopTraining3](https://user-images.githubusercontent.com/50051656/175320768-29a1dc46-f2d7-4930-a9f7-f90e3d28c593.png)

## 그 외

- `@Before` : 타겟 메서드가 실행되기 전
- `@AfterReturning` : 타겟 메서드가 실행된 후
- `@AfterThrowing` : 예외가 발생했을 때
- `@After` : 프록시 메서드가 완전히 종료될 때

> **`@Around` 하나로 되는데 왜 분기해서 여러 메서드로 또 제공하나?**
>
> 의미가 부여되기 때문에 역할이 분명해진다.
> 타겟 메서드를 개발자 실수로 호출하지 않아 발생하는 오류를 방지할 수 있다.

```java
@Slf4j
@Aspect
public class AspectV4 {

	@Around("me.hajoo.aop._2_after.aop.Pointcuts.orderAndAllService()")
	public Object doTransaction(ProceedingJoinPoint joinPoint) throws Throwable {
		try {
			log.info("[transaction begin] {}", joinPoint.getSignature());
			// @Before
			Object result = joinPoint.proceed( );
			// @AfterReturning
			log.info("[transaction end] {}", joinPoint.getSignature());
			return result;
		} catch (Exception e) {
			// @AfterThrowing
			log.info("[transaction rollback] {}", joinPoint.getSignature());
			throw e;
		} finally {
			// @After
			log.info("[release] {}", joinPoint.getSignature());
		}
	}

	@Before("me.hajoo.aop._2_after.aop.Pointcuts.orderAndAllService()")
	public void doBefore(JoinPoint joinPoint) {
		log.info("[before] {}", joinPoint.getSignature());
	}

	@AfterReturning(value = "me.hajoo.aop._2_after.aop.Pointcuts.orderAndAllService()", returning = "result")
	public void doReturn(JoinPoint joinPoint, Object result) {
		log.info("[afterReturning] {} return {}", joinPoint.getSignature(), result);
	}

	@AfterThrowing(value = "me.hajoo.aop._2_after.aop.Pointcuts.orderAndAllService()", throwing = "ex")
	public void doThrowing(JoinPoint joinPoint, Exception ex) {
		log.info("[afterThrowing] {} exception {}", joinPoint.getSignature(), ex.getMessage());
	}

	@After("me.hajoo.aop._2_after.aop.Pointcuts.orderAndAllService()")
	public void doAfter(JoinPoint joinPoint) {
		log.info("[after] {}", joinPoint.getSignature());
	}
}
```

### 결과

![aopTraining4](https://user-images.githubusercontent.com/50051656/175320765-7a51f388-0ee1-4090-9dbb-a1faccaec242.png)