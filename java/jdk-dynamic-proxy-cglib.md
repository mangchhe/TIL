# JDK Dynamic Proxy, CGLib

- 예제 소스 [→](https://github.com/TIL-Repo/spring-study/tree/main/Proxy)
- 프록시를 적용하기 위해서는 프록시를 적용할 클래스의 개수만큼 프록시 클래스를 만들었다.
- 숫자가 많아지면 이 방식은 관리 포인트가 많아지고 생산성이 떨어지게 된다.
- 만약 **프록시의 로직은 같은데 적용 대상만 다르다면 동적 프록시 기술**을 이용할 수 있다.
- 동적 프록시 기술을 이용하면 **직접 클래스를 만들지 않아도 런타임 때 동적으로 만들어 줄 수 있기 때문에 단점을 해결할 수 있다.**

## JDK Dynamic Proxy

- 인터페이스를 기반으로 프록시를 동적으로 만들어준다.
- 즉, **인터페이스가 필수라는 것을 명심해야 한다.**
- com.sun.proxy.$Proxy임의숫자 형태로 프록시 생성

### InvocationHandler

- JDK 동적 프록시에 적용할 로직은 InvocationHandler 인터페이스를 구현하여 작성한다.

```java
public interface InvocationHandler {

    public Object invoke(Object proxy, Method method, Object[] args)
        throws Throwable;
}
```

### 실습

- A 인터페이스 AInterface와 인터페이스를 구현한 AImpl 클래스 생성

```java
@Slf4j
public class AImpl implements AInterface {

	@Override
	public String call() {
		log.info("A 호출");
		return " A";
	}
}
public interface AInterface {

	String call();
}
```

- B 인터페이스 BInterface와 인터페이스를 구현한 BImpl 클래스 생성

```java
@Slf4j
public class BImpl implements BInterface {

	@Override
	public String call() {
		log.info("B 호출");
		return "B";
	}
}
public interface BInterface {

	String call();
}
```

- InvocationHandler 인터페이스를 구현한 클래스
- 해당 메서드가 걸리는 시간을 구하는 로직을 구현
- 프록시 클래스는 실제 로직을 수행할 타깃 인스턴스를 가져야 한다.
- method.invoke()이라는 리플렉션 기술을 이용해서 동적으로 각 상황에 맞는 메서드를 생성하여 호출할 수 있다.

```java
@Slf4j
public class TimeInvocationHandler implements InvocationHandler {

	private final Object target;

	public TimeInvocationHandler(Object target) {
		this.target = target;
	}

	@Override
	public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
		log.info("TimeProxy 실행");
		final long startTime = System.currentTimeMillis();

		final Object result = method.invoke(target, args);

		final long endTime = System.currentTimeMillis();
		long resultTime = endTime - startTime;
		log.info("TimeProxy 종료 resultTime = {}", resultTime);
		return result;
	}
}
```

- 실행시킬 target 클래스 인스턴스를 TimeInvocationHanlder 인자로 전달
- JDK에서 제공하는 `Proxy.newProxyInstance(ClassLoader loader, Class<?>[] interfaces, InvocationHandler h)`를 이용하여 프록시 생성
    - target 클래스의 ClassLoader
    - target 클래스의 Class Type
    - InvocationHandler 인터페이스를 구현한 클래스 인스턴스

```java
@Slf4j
public class JdkDynamicProxyTest {
	
	@Test
	public void dynamicA() throws Exception {
		// given
		final AInterface target = new AImpl();
		final TimeInvocationHandler handler = new TimeInvocationHandler(target);
		// when
		final AInterface proxy = (AInterface) Proxy.newProxyInstance(AInterface.class.getClassLoader(), new Class[] {AInterface.class},
			handler);
		// then
		log.info("targetClass = {}", target.getClass());
		log.info("proxyClass = {}", proxy.getClass());
		proxy.call();
	}

	@Test
	public void dynamicB() throws Exception {
		// given
		final BInterface target = new BImpl();
		final TimeInvocationHandler handler = new TimeInvocationHandler(target);
		// when
		final BInterface proxy = (BInterface) Proxy.newProxyInstance(BInterface.class.getClassLoader(), new Class[] {BInterface.class},
			handler);
		// then
		log.info("targetClass = {}", target.getClass());
		log.info("proxyClass = {}", proxy.getClass());
		proxy.call();
	}
}
```

### 결과

- 각각 메서드를 호출할 때 프록시가 자동으로 생성이 되고 시간이 측정되는 것을 확인할 수 있다.
- 클래스마다 인터페이스에 프록시 클래스, 구현 클래스를 만들어줄 필요 없이 InvocationHandler에 로직을 구현해주면 Proxy 클래스는 동적으로 생성하게 된다.

![jdkDynamicProxyTC](https://user-images.githubusercontent.com/50051656/172663657-c418f66a-8275-4cdc-af7a-4f4cb0163b99.png)


## CGLib

- 바이트코드를 조작하여 동적으로 클래스를 생성하는 기술을 제공하는 라이브러리이다.
- CGLib는 **인터페이스가 없어도 구체 클래스만 가지고도 동적 프록시를 만들어낼 수 있다.**
- 대신 클래스를 상속받아 사용하기에 클래스에 final, 메서드에 final이 붙으면 사용이 불가능하다.
- CGLib는 외부 라이브러리이지만 스프링 내부에 포함되어 있다.
- 대상클래스$$EnhancerByCGLIB$$임의코드 형태로 프록시 생성

### MethodInterceptor

- CGlib에서 적용할 로직은 InvocationHandler 인터페이스를 구현하여 작성한다.

```java
public interface MethodInterceptor extends Callback {
    Object intercept(Object var1, Method var2, Object[] var3, MethodProxy var4) throws Throwable;
}
```

### 실습

- 구체클래스 생성

```java
@Slf4j
public class ConcreteService {

	public void call() {
		log.info("call 호출");
	}
}
```

- MethodInterceptor를 구현하는 클래스를 만들어 로직 구현
- 프록시 클래스는 실제 로직을 수행할 타깃 인스턴스를 가져야 한다.
- Method 대신 MethodProxy를 이용하면 좀 더 성능이 빠르다.

```java
@Slf4j
public class TimeMethodInterceptor implements MethodInterceptor {

	private final Object target;

	public TimeMethodInterceptor(Object target) {
		this.target = target;
	}

	@Override
	public Object intercept(Object o, Method method, Object[] args, MethodProxy methodProxy) throws Throwable {
		log.info("TimeProxy 실행");
		final long startTime = System.currentTimeMillis();

		final Object result = methodProxy.invoke(target, args);

		final long endTime = System.currentTimeMillis();
		long resultTime = endTime - startTime;
		log.info("TimeProxy 종료 resultTime = {}", resultTime);
		return result;
	}
}
```

- CGLib는 프록시를 생성하기 위해 `Enhancer`를 이용한다.
- setSuperclass로 구체클래스의 Class Type
- setCallback으로 MethodInterceptor를 구현한 클래스 인스턴스

```java
@Slf4j
public class CglibTest {

	@Test
	public void cglib() throws Exception {
	    // given
		final ConcreteService target = new ConcreteService();
		// when
		final Enhancer enhancer = new Enhancer();
		enhancer.setSuperclass(ConcreteService.class);
		enhancer.setCallback(new TimeMethodInterceptor(target));
		final ConcreteService proxy = (ConcreteService) enhancer.create();
		// then
		log.info("targetClass = {}", target.getClass());
		log.info("proxyClass = {}", proxy.getClass());
		proxy.call();
	}
}
```

### 결과

- 프록시가 동적으로 생성이 되고 실행되는 것을 확인할 수 있다.

![CglibProxyTC](https://user-images.githubusercontent.com/50051656/172674157-dd79959b-4281-46c5-a005-7d45d1ee7c53.png)
