# Advisor, Advice, Pointcut 개념

- AOP에서 사용되는 개념들
- 포인트컷(Pointcut) : 부가 기능을 어디에 적용할 지 정의
- 어드바이스(Advice) : 부가 기능 구현(프록시 로직)
- 어드바이저(Advisor) : 포인트컷과 어드바이스를 포함하는 개념, pointcut + advice

> 포인트컷, 어드바이스 분리 이유
>
> 포인트컷은 적용할 대상, 어드바이스는 로직 구현으로 역할과 책임을 명확히 분리하기 위함

## 실습

- 예제 소스 : [→](https://github.com/TIL-Repo/spring-study/tree/main/ProxyFactory)
- ServiceImpl, TimeAdvice 클래스는 [참고](https://github.com/mangchhe/TIL/blob/main/spring/proxy-factory.md)

### Advisor 적용

- Advisor를 생성하기 위해 DefaultPointcutAdvisor 객체를 만들고 인자로 Pointcut, Advice를 전달한다.
    - Pointcut.TRUE : 모든 곳에 적용
- 프록시 팩토리에 advisor를 등록한 후 프록시 객체를 얻고 실행

```java
@Test
void advisorTest() throws Exception {
    Service target = new ServiceImpl();
    ProxyFactory proxyFactory = new ProxyFactory(target);
    DefaultPointcutAdvisor advisor = new DefaultPointcutAdvisor(Pointcut.TRUE, new TimeAdvice());
    proxyFactory.addAdvisor(advisor);
    Service proxy = (Service)proxyFactory.getProxy();
    proxy.hello();
    proxy.world();
}
```

### 커스텀 Pointcut 적용

- 커스텀 Pointcut을 만들기 위해 Poincut 인터페이스를 구현
- 필터 로직은 MethodMatcher를 구현하여 객체로 Pointcut에 등록
- Pointcut.TRUE가 아닌 커스텀하여 만든 Pointcut 클래스를 생성하여 Advisor 객체 인자로 전달

```java
@Test
void PointcutTest() throws Exception {
    Service target = new ServiceImpl();
    ProxyFactory proxyFactory = new ProxyFactory(target);
    DefaultPointcutAdvisor advisor = new DefaultPointcutAdvisor(new MyPointcut(), new TimeAdvice());
    proxyFactory.addAdvisor(advisor);
    Service proxy = (Service)proxyFactory.getProxy();
    proxy.hello();
    proxy.world();
}

static class MyPointcut implements Pointcut {

    @Override
    public ClassFilter getClassFilter() {
        return ClassFilter.TRUE;
    }

    @Override
    public MethodMatcher getMethodMatcher() {
        return new MyMethodMacher();
    }
}

static class MyMethodMacher implements MethodMatcher {

    final String methodName = "hello";


    @Override
    public boolean matches(Method method, Class<?> targetClass) {
        boolean result = method.getName().equals(methodName);
        log.info("포인트컷 호출 method={} targetClass={}", method.getName(),targetClass);
        log.info("포인트컷 결과 result={}", result);
        return result;
    }

    @Override
    public boolean isRuntime() {
        return false;
    }

    @Override
    public boolean matches(Method method, Class<?> targetClass, Object... args) {
        return false;
    }
}
```

### 스프링에서 제공하는 Pointcut

- NameMatchMethodPointcut을 이용하여 매칭할 메소드 이름을 손쉽게 설정
- 그 외에도 AnnotationMatchingPointcut 등 존재하고 가장 많이 사용되는 표현식은 `AspectJExpressionPointcut`이다.

```java
@Test
void PointcutInSpring() throws Exception {
    Service target = new ServiceImpl();
    ProxyFactory proxyFactory = new ProxyFactory(target);
    NameMatchMethodPointcut pointcut = new NameMatchMethodPointcut();
    pointcut.setMappedName("hello");
    DefaultPointcutAdvisor advisor = new DefaultPointcutAdvisor(pointcut, new TimeAdvice());
    proxyFactory.addAdvisor(advisor);
    Service proxy = (Service)proxyFactory.getProxy();
    proxy.hello();
    proxy.world();
}
```

### 두 개 이상의 Advisor 적용

#### 예제 Advisor 구현

```java
@Slf4j
static class Advice1 implements MethodInterceptor {
    @Override
    public Object invoke(MethodInvocation invocation) throws Throwable {
        log.info("advice1 호출");
        return invocation.proceed();
    }
}

@Slf4j
static class Advice2 implements MethodInterceptor {
    @Override
    public Object invoke(MethodInvocation invocation) throws Throwable {
        log.info("advice2 호출");
        return invocation.proceed();
    }
}
```

#### Case 1

- 핵심 로직의 target 클래스에 Advisor를 하나 적용하고 적용된 프록시 객체를 넣어 또 다른 Advisor 객체를 적용하는 방법
- 이렇게 되면 프록시 객체를 적용 Advisor 개수만큼 생성하게 된다.

```java
@Test
void multiAdvisorTest() throws Exception {
    Service target = new ServiceImpl();

    // proxy1
    ProxyFactory proxyFactory = new ProxyFactory(target);
    DefaultPointcutAdvisor advisor = new DefaultPointcutAdvisor(Pointcut.TRUE, new Advice1());
    proxyFactory.addAdvisor(advisor);
    Service proxy = (Service)proxyFactory.getProxy();

    // proxy2
    ProxyFactory proxyFactory2 = new ProxyFactory(proxy);
    DefaultPointcutAdvisor advisor2 = new DefaultPointcutAdvisor(Pointcut.TRUE, new Advice2());
    proxyFactory.addAdvisor(advisor2);
    Service proxy2 = (Service)proxyFactory.getProxy();

    proxy2.hello();
    proxy2.world();
}
```

#### Case 2

- 프록시 팩토리는 여러 개의 Advisor를 적용하여 프록시 객체를 얻을 수 있다.

```java
@Test
void oneProxyWithAdvisor() throws Exception {
    DefaultPointcutAdvisor advisor = new DefaultPointcutAdvisor(Pointcut.TRUE, new Advice1());
    DefaultPointcutAdvisor advisor2 = new DefaultPointcutAdvisor(Pointcut.TRUE, new Advice2());

    Service target = new ServiceImpl();
    ProxyFactory proxyFactory = new ProxyFactory(target);

    proxyFactory.addAdvisor(advisor);
    proxyFactory.addAdvisor(advisor2);
    Service proxy = (Service)proxyFactory.getProxy();

    proxy.hello();
}
```

## Reference

- [스프링 핵심 원리 - 고급편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B3%A0%EA%B8%89%ED%8E%B8/dashboard)