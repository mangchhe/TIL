# 프록시 팩토리

- 인터페이스가 있으면 JDK Dynamic Proxy, 없을 때는 CGLIB를 사용할 수 있다.
- 두 기술을 같이 사용할 때 특정 조건에 맞는 프록시 로직을 적용하기 위해 추상화된 프록시 팩토리를 제공한다.

## Advice

- 이때 InvocationHandler(JDK), MethodInterceptor(CGLIB)를 중복으로 구현을 해놔야 하나?
- 그렇지 않고 Advice라는 추상화된 개념을 도입하게 되면 중복으로 구현할 필요가 없다.

### 실습

- 인터페이스를 가지는 클래스, 그렇지 않은 구체 클래스, 그리고 CGLIB으로 강제하는 설정에 대해 실습해보자.

#### 준비

- MethodIntercetor를 구현한 Advice를 생성
- 이때 CGLIB로 동적 프록시를 구현할 때 구현하는 인터페이스와 같은데 패키지명을 잘 구분해야 한다.
    - org.springframework.cglib.proxy.MethodInterceptor (CGLIB)
    - org.aopalliance.intercept.MethodInterceptor (Advice)

```java
@Slf4j
public class TimeAdvice implements MethodInterceptor {

	@Override
	public Object invoke(MethodInvocation invocation) throws Throwable {
		log.info("TimeAdvice 실행");
		long startTime = System.currentTimeMillis();

		Object result = invocation.proceed();

		long endTime = System.currentTimeMillis();
		long resultTime = endTime - startTime;
		log.info("TimeAdvice 종료 resultTime = {}", resultTime);

		return result;
	}
}
```

#### 1. 인터페이스가 존재하는 프록시 (JDK Dynamic Proxy)

- 인터페이스가 존재하는 서비스 구현

```java
public interface Service {

	void hello();

	void world();
}

@Slf4j
public class ServiceImpl implements Service {

	@Override
	public void hello() {
		log.info("Hello");
	}

	@Override
	public void world() {
		log.info("World!");
	}
}
```

- ProxyFactory를 생성하고 인자로 실제 로직이 담긴 target 클래스를 전달
- advice로 아까 만든 TimeAdvice를 추가

```java
@Test
void jdk_dynamic_proxy() {
    Service target = new ServiceImpl();
    ProxyFactory proxyFactory = new ProxyFactory(target);
    proxyFactory.addAdvice(new TimeAdvice());
    Service proxy = (Service) proxyFactory.getProxy();
    log.info("targetClass = {}", target.getClass());
    log.info("proxyClass = {}", proxy.getClass());
    proxy.hello();

    Assertions.assertTrue(AopUtils.isAopProxy(proxy));
    Assertions.assertTrue(AopUtils.isJdkDynamicProxy(proxy));
    Assertions.assertFalse(AopUtils.isCglibProxy(proxy));
}
```

- 프록시 객체를 얻고 메소드를 실행하면 JDK 동적 프록시로 적용이 된 것을 확인할 수 있다.

<img width="613" alt="jdkdynamicproxy" src="https://user-images.githubusercontent.com/50051656/174108063-4ac80757-e73f-42bc-9d4d-6be4dc0b35e4.png">


#### 2. 인터페이스가 없는 구체 클래스

- 구체 클래스 생성

```java
@Slf4j
public class ConcreteService {

	public void hello() {
		log.info("Hello World!");
	}
}
```

- 위와 동일하게 적용하고 target 클래스만 변경

```java
@Test
void cglib_proxy() throws Exception {
    ConcreteService target = new ConcreteService();
    ProxyFactory proxyFactory = new ProxyFactory(target);
    proxyFactory.addAdvice(new TimeAdvice());
    ConcreteService proxy = (ConcreteService) proxyFactory.getProxy();
    log.info("targetClass = {}", target.getClass());
    log.info("proxyClass = {}", proxy.getClass());
    proxy.hello();

    Assertions.assertTrue(AopUtils.isAopProxy(proxy));
    Assertions.assertFalse(AopUtils.isJdkDynamicProxy(proxy));
    Assertions.assertTrue(AopUtils.isCglibProxy(proxy));
}
```

- 테스트를 실행시키면 CGLIB으로 프록시 객체가 생성된 것을 확인할 수 있다.

![cglib](https://user-images.githubusercontent.com/50051656/174107779-50cc2eb6-cdf3-46f3-a2f5-21d6d96ba51e.png)

#### 3. 인터페이스가 존재해도 CGLIB으로 강제

- setProxyTargetClass에 인자로 true를 전달하게 되면 인터페이스가 있어도 CGLIB으로 강제한다.
- 스프링 부트 최신버전은 CGLIB을 Default로 사용한다.
    - ProxyFactory에 getProxy 메서드 참고

```java
@Test
void cglib_proxy_exists_interface() throws Exception {
    Service target = new ServiceImpl();
    ProxyFactory proxyFactory = new ProxyFactory(target);
    proxyFactory.setProxyTargetClass(true);
    proxyFactory.addAdvice(new TimeAdvice());
    Service proxy = (Service) proxyFactory.getProxy();
    log.info("targetClass = {}", target.getClass());
    log.info("proxyClass = {}", proxy.getClass());
    proxy.hello();

    Assertions.assertTrue(AopUtils.isAopProxy(proxy));
    Assertions.assertFalse(AopUtils.isJdkDynamicProxy(proxy));
    Assertions.assertTrue(AopUtils.isCglibProxy(proxy));
}
```

- 인터페이스를 가지고 있지만 CGLIB 동적 프록시가 생성된 것을 확인할 수 있다.

![cglib2](https://user-images.githubusercontent.com/50051656/174107805-c883d8ba-a042-45f8-8144-98e58587aad2.png)

## Reference

- [스프링 핵심 원리 - 고급편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B3%A0%EA%B8%89%ED%8E%B8/dashboard)