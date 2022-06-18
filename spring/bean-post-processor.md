# 빈 후처리기

- `@Bean`이나 컴포넌트 스캔을 통해 스프링 컨테이너에 빈이 등록된다.
- 빈 후처리기는 빈이 등록되기 전에 빈을 등록하고 싶을 떄 어떤 로직을 처리할 때 사용된다.
- 즉, 빈 객체를 조작하거나 다른 객체로 바꿔치기를 할 수 있다.

## 후처리기 구현

- `BeanPostProcessor` 인터페이스를 구현 후, 스프링 빈으로 등록한다.
- `postProcessBeforeInitialization` : 객체 생성 이후 `@PostContsruct` 같은 초기화 발생하기 전에 호출되는 포스트 프로세서
- `postProcessAfterInitialization` : 겍체 생성 이후 `@PostConstruct` 같은 초기화 발생한 후 호출되는 포스트 프로세서

### 실습

- 예제 클래스 두 개 생성

```java
static class A {
    public void helloA() {
        log.info("hello A");
    }
}
static class B {
    public void helloB() {
        log.info("hello B");
    }
}
```

- `postProcessAfterInitialization` 오버라이딩하여 만약 빈이 A 클래스 타입일 경우 기존 A 객체는 버리고 B 객체를 생성 후 빈으로 등록

```java
static class AToBPostProcessor implements BeanPostProcessor {

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        log.info("beanName = {} bean = {}", beanName, bean);
        if (bean instanceof A) {
            return new B();
        }
        return bean;
    }
}
```

- A 클래스를 빈으로 등록하고 후처리기도 함께 빈으로 등록

```java
@Configuration
static class BeanPostProcessorConfig {
    @Bean(name = "beanA")
    public A a() {
        return new A();
    }

    @Bean
    public AToBPostProcessor aToBPostProcessor() {
        return new AToBPostProcessor();
    }
}
```

### 테스트

- 스프링 컨테이너를 가져와 테스트
- beanA 이름을 가지는 빈을 가져오면 B 객체로 변경된 것을 확인할 수 있다.
- 클래스 A가 빈 객체로 등록되었는지 조회하게 되었을 때 `NoSuchBeanDefinitionException`이 발생한다.

```java
@Test
void configTest() throws Exception {
    ApplicationContext applicationContext = new AnnotationConfigApplicationContext(BeanPostProcessorConfig.class);

    B b = applicationContext.getBean("beanA", B.class);
    b.helloB();

    Assertions.assertThrows(NoSuchBeanDefinitionException.class, () -> applicationContext.getBean(A.class));
}
```

## 프록시를 적용하는 후처리기 구현

- 후처리기를 통해 프록시를 등록하는 방법에 대해서 알아보자

### 실습

- `BeanPostProcessor`를 구현하여 `postProcessAfterInitialization`를 오버라이딩한다.
- 빈의 패키지 이름을 가져와 해당하는 객체만 프록시 객체를 만들어 반환하고 그 외에는 본래 객체를 반환한다.

```java
static class PostProcessor implements BeanPostProcessor {

    private final Advisor advisor;

    public PostProcessor(Advisor advisor) {
        this.advisor = advisor;
    }

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        log.info("beanName = {}, bean = {}", bean, beanName);

        String packageName = bean.getClass().getPackageName();
        if (packageName.startsWith("me.hajoo.beanpostprocessor.service")) {
            ProxyFactory proxyFactory = new ProxyFactory(bean);
            proxyFactory.addAdvisor(advisor);
            Object proxy = proxyFactory.getProxy();
            log.info("create proxy bean = {} proxy = {}", bean.getClass(), proxy.getClass());
            return proxy;
        }

        return bean;
    }
}
```

- 프록시 객체를 만들기 위해 필요한 Advisor 생성
- Pointcut은 메서드 이름이 hello 일 경우, Advice는 해당 메소드 이름을 출력하는 것을 구현했다.

```java
private Advisor getAdvisor() {
    NameMatchMethodPointcut pointcut = new NameMatchMethodPointcut();
    pointcut.setMappedName("hello");
    return new DefaultPointcutAdvisor(pointcut, new TestAdvice());
}

static class TestAdvice implements MethodInterceptor {

    @Override
    public Object invoke(MethodInvocation invocation) throws Throwable {
        String methodName = invocation.getMethod().getName();
        log.info("create advice methodName = {}", methodName);
        return invocation.proceed();
    }
}
```

- 빈 후처리기를 빈으로 등록

```java
@Configuration
public class BeanPostProcessorConfig {

	@Bean
	public PostProcessor postProcessor() {
		return new PostProcessor(getAdvisor());
	}
}
```

### 테스트

- hello(), world() 메서드를 가지는 service 클래스 생성

```java
@Service
public class HelloService {

	public void hello() { log.info("hello"); }

	public void world() { log.info("world"); }
}
```

```java
@SpringBootTest
public class RegistBeanPostProcessorTest {

	@Autowired
	private HelloService helloService;

	@Test
	void test() throws Exception {
		helloService.hello();
		helloService.world();
	}
}
```

- HelloService가 CGLIB 동적 프록시로 생성된 것을 확인할 수 있고 Pointcut 조건에 맞는 hello 메서드가 실행되었을 때 프록시가 정상 작동하는 것을 확인할 수 있다.

<img width="1063" alt="beanPostProcessorTest" src="https://user-images.githubusercontent.com/50051656/174427895-38a0e439-2e31-4bcf-9e40-bd3bf8e91495.png">
<img width="592" alt="beanPostProcessorTest2" src="https://user-images.githubusercontent.com/50051656/174427890-0e117c13-637f-4dd6-a82d-14acd18e06d0.png">

## 스프링에서 제공하는 빈 후처리기

- 부트에서 제공하는 `AnnotationAwareAspectJAutoProxyCreator`라는 빈이 빈 후처리기가 스프링 빈에 자동으로 등록된다.
- 부트가 아니면 `@EnableAspectJAutoProxy`를 사용해야한다.

### 자동 프록시 생성 동작 과정

1. 스프링 빈 객체를 생성
1. 생성된 객체를 등록하기 전에 빈 후처리기에 전달
    1. 모든 Advisor 빈 조회
    1. Advisor에 등록된 Pointcut 조건을 보고 만족하는 클래스라면 프록시 객체 생성 (단 하나라도 만족하는 대상이 있을 경우 해당 클래스의 프록시 객체 생성)
1. 빈 후처리기에 걸러진 빈이나 적용된 빈을 스프링 컨테이너에 등록

### 중요한 점

- 한 클래스에 여러 Advisor가 적용될 때 Advisor 마다 해당 클래스에 프록시 객체를 생성하는 것이 아니다.
- proxyFactory.addAdvisor()를 통해 여러 개의 advisor를 적용할 수 있고 결국 여러 개의 advisor가 적용된 하나의 프록시 클래스가 생성된다.

### 실습

- dependency 추가

```yml
implementation 'org.springframework.boot:spring-boot-starter-aop'
```

- Advisor를 만들어 빈으로 등록하면 스프링 실행 시 자동으로 조건에 맞는 클래스의 프록시 객체를 만들게 된다.
- 두 번째 advisor는 AspectJ가 제공하는 포인트컷 표현식으로 자주 사용된다.
    - 맨 처음에는 반환 타입을 말하고 *는 어떤 것이 와도 상관없다는 말이다.
    - me.hajoo는 적용할 메서드 위치를 가리키고 () 안에는 인자 값이고 ..은 아무 값이나 와도 상관없다는 말이다.
        - ..은 현재 패키지와 그 하위 패키지 모두 적용하는 것을 말한다.
- 그 외에도 &&(and), !(not)같은 표현식도 가능하다.

```java
@Configuration
public class AutoProxyConfig {

	@Bean
	public Advisor advisor() {
		NameMatchMethodPointcut pointcut = new NameMatchMethodPointcut();
		pointcut.setMappedName("hello");
		return new DefaultPointcutAdvisor(pointcut, new BeanPostProcessorConfig.TestAdvice());
	}

    @Bean
	public Advisor advisor2() {
		AspectJExpressionPointcut pointcut = new AspectJExpressionPointcut();
		pointcut.setExpression("execution(* me.hajoo.beanpostprocessor.service..*(..)) && !execution(* me.hajoo.beanpostprocessor.service..world(..))");
		return new DefaultPointcutAdvisor(pointcut, new BeanPostProcessorConfig.TestAdvice());
	}
}
```

## Reference

- [스프링 핵심 원리 - 고급편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B3%A0%EA%B8%89%ED%8E%B8/dashboard)