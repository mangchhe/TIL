# ThreadLocal

- 각각의 스레드가 독립적으로 접근할 수 있는 저장소를 말한다.

## 사용하는 이유

- **동시성 문제를 해결**하기 위해서 사용한다.
- 서로 다른 두 스레드가 하나의 공유 변수에 접근할 경우 동기화를 보장하지 않는다.
- 그렇기 때문에 서로 다른 스레드가 추가하거나 업데이트했던 값들이 다른 값이 되어 예외를 일으킬 수 있다.

## 주의사항

- 톰캣같은 WAS를 사용하여 애플리케이션을 구축했을 때 멀티 스레드를 사용하는데 스레드 생성 비용이 많이 들기 때문에 스레드 풀에 미리 일정량의 스레드를 생성한 후에 가져다가 쓰게 된다. 스레드를 매번 생성하는 것이 아니라 재사용하기 때문에 이전 값이 남아 있으면 안 된다.
- 스레드 사용이 끝났다면 remove를 이용해 저장소를 비워줘야 예기치 못한 예외 상황이 발생하지 않는다.

## 실습

### ThreadLocal 적용 전

- nameStorage에 값을 저장하는 로직을 가지는 서비스 구현

```java
public class Service {

	private String nameStorage;

	public void logic(String name) {
		final String threadName = Thread.currentThread().getName();
		System.out.println("[" + threadName + "] " + "name : " + name + ", nameStorage : " + nameStorage);
		nameStorage = name;
		sleep(1000);
		System.out.println("[" + threadName + "] " + "nameStorage : " + nameStorage);
	}

	private void sleep(int ms) {
		try {
			Thread.sleep(ms);
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
	}
}
```

- 두 개의 스레드를 생성하고 실행

```java
class ThreadLocalServiceTest {

	private final Service service = new Service();

	@Test
	void testWithoutThreadLocal() throws Exception {
	    // given
		final Thread thread = new Thread(() -> service.logic("userA"));
		final Thread thread2 = new Thread(() -> service.logic("userB"));

		thread.setName("Thread-A");
		thread2.setName("Thread-B");

		// when
		thread.start();
		thread2.start();

		thread.join();
		thread2.join();
	}
}
```

#### 테스트 결과

- Thread A, B가 서로 같은 값을 가지는 것을 볼 수 있다.

``` bash
[Thread-B] name : userB, nameStorage : null
[Thread-A] name : userA, nameStorage : null
[Thread-B] nameStorage : userA
[Thread-A] nameStorage : userA
```

### ThreadLocal 적용 후

- nameStorage에 값을 저장하는 로직을 가지는 서비스 구현, 단 변수를 ThreadLocal 객체로 생성

```java
public class ThreadLocalService {

	private ThreadLocal<String> nameStorage = new ThreadLocal<>();

	public void logic(String name) {
		final String threadName = Thread.currentThread().getName();
		System.out.println("[" + threadName + "] " + "name : " + name + ", nameStorage : " + nameStorage.get());
		nameStorage.set(name);
		sleep(1000);
		System.out.println("[" + threadName + "] " + "nameStorage : " + nameStorage.get());
	}

	private void sleep(int ms) {
		try {
			Thread.sleep(ms);
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
	}
}
```

- 두 개의 스레드를 생성하고 실행

```java
@Test
void testWithThreadLocal() throws Exception {
    // given
    final Thread thread = new Thread(() -> threadLocalService.logic("userA"));
    final Thread thread2 = new Thread(() -> threadLocalService.logic("userB"));

    thread.setName("Thread-A");
    thread2.setName("Thread-B");

    // when
    thread.start();
    thread2.start();

    thread.join();
    thread2.join();
}
```

#### 테스트 결과

- 각각의 스레드가 서로 다른 독립적으로 값을 가지는 것을 볼 수 있다.

```bash
[Thread-A] name : userA, nameStorage : null
[Thread-B] name : userB, nameStorage : null
[Thread-B] nameStorage : userB
[Thread-A] nameStorage : userA
```

## Reference

[스프링 핵심 원리 - 고급편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B3%A0%EA%B8%89%ED%8E%B8/dashboard)