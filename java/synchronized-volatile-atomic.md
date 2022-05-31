# Synchronized, Volatile, Atomic Type

- 자바에서 race condition 문제를 해결하기 위한 대표적인 세 가지 방법

## 목차

- [Synchronized](#Synchronized)
    - [Synchronized Method](#Synchronized-Method)
    - [Static Synchronized Method](#Static-Synchronized-Method)
    - [Synchronized Block](#Synchronized-Block)
    - [Static Synchronized Block](#Static-Synchronized-Block)
- [Volatile](#Volatile)
    - [사용하는 이유](#사용하는-이유)
    - [언제가 적합한가?](#언제가-적합한가?)
    - [단점](#단점)
    - [주의사항](#주의사항)
    - [예제](#예제)
- [Atomic Type](#Atomic-Type)

## Synchronized

- 멀티 스레드 환경에서 두 개 이상의 스레드가 하나의 변수에 동시 접근할 때 race condition이 발생하지 않도록 한다.
- 각 상황에 따라 공유되는 범위를 synchronized method, synchronized block, static synchronized method, static synchronized block으로 나눌 수 있다.

### Synchronized Method

- 키워드를 메소드에 달 경우
- 같은 인스턴스끼리만 동기화를 보장한다. 즉, 다른 인스턴스가 온다면 동기화 X

```
// before
thread2 lock
thread1 lock
thread1 unlock
thread2 unlock
// after
thread1 lock
thread1 unlock
thread2 lock
thread2 unlock
```

```java
public class Main {

	public static void main(String[] args) {
		A a = new A();
		Thread thread1 = new Thread(() -> {
			a.syncRun("thread1");
		});
		Thread thread2 = new Thread(() -> {
			a.syncRun("thread2");
		});
		thread1.start();
		thread2.start();
	}
}

class A {
	public void run(String name) {
		System.out.println(name + " lock");
		System.out.println(name + " unlock");
	}

	public static void syncRun(String name) {
		synchronized (A.class) {
			System.out.println(name + " lock");
			System.out.println(name + " unlock");
		}
	}
}
```

### Static Synchronized Method

- 정적 메서드에 키워드를 달 경우
- 클래스 단위로 동기화를 보장한다.

```java
public class Main {

	public static void main(String[] args) {
		A a = new A();
		Thread thread1 = new Thread(() -> {
			a.syncRun("thread1");
		});
		Thread thread2 = new Thread(() -> {
			a.syncRun("thread2");
		});
		thread1.start();
		thread2.start();
	}
}

class A {

	public synchronized void syncRun(String name) {
		System.out.println(name + " lock");
		System.out.println(name + " unlock");
	}
}
```

### Synchronized Block

- Block 단위로 키워드를 달 경우
- 블록에 인자로 받는 객체를 모니터 객체라 하고 이 객체를 기준으로 동기화 수준이 정해진다.
- this를 넣으면 인스턴스 단위, 클래스를 넣으면 그 클래스 단위로 동기화를 보장한다.

```java
public class Main {

	public static void main(String[] args) {
		A a = new A();
		A b = new A();
		Thread thread1 = new Thread(() -> {
			a.syncRun("thread1");
		});
		Thread thread2 = new Thread(() -> {
			b.syncRun("thread2");
		});
		thread1.start();
		thread2.start();
	}
}

class A {

	public void syncRun(String name) {
		// 인스턴스 단위
        synchronized (this) {
			System.out.println(name + " lock");
			System.out.println(name + " unlock");
		}
        // 클래스 단위
        synchronized (A.class) {
			System.out.println(name + " lock");
			System.out.println(name + " unlock");
		}
	}
}
```

### Static Synchronized Block

- 정적 메서드에 동기화 block을 걸 경우
- 클래스 단위로 동기화를 보장하고 Static Synchronized Method와 차이점은 동기화하고 싶은 범위를 지정할 수 있다는 점이다.

```java
public class Main {

	public static void main(String[] args) {
		A a = new A();
		A b = new A();
		Thread thread1 = new Thread(() -> {
			a.syncRun("thread1");
		});
		Thread thread2 = new Thread(() -> {
			b.syncRun("thread2");
		});
		thread1.start();
		thread2.start();
	}
}

class A {

	public static void syncRun(String name) {
		synchronized (this) {
			System.out.println(name + " lock");
			System.out.println(name + " unlock");
		}
	}
}
```

## Volatile

- Main Memorty에 값을 저장하겠다는 것을 명시하는 키워드이다.
- 매번 값을 READ 할 때 CPU cache에 저장된 값이 아니라 Main Memory에서 읽고 WRITE 할 때도 Main Memory에 작성한다.

```java
public class A {
    public volatile int counter = 0;
}
```

### 사용하는 이유

![memory](https://user-images.githubusercontent.com/50051656/171189221-6147e06c-155a-4170-b926-15aeee210e8a.png)

- **멀티 스레드 환경**에서 각 Task를 수행하는 동안 **성능 향상을 위해 Main Memory에서 읽은 값을 CPU Cache에 저장**한다.
- 각각의 CPU Cache에 저장되어 있는 변수의 값이 다르기 때문에 스레드마다 참조하는 캐시가 다르므로 **값 불일치**가 생길 수 있다.

### 언제가 적합한가?

- 하나의 스레드만 WRITE하고 나머지 스레드가 READ 하는 상황이 가장 적합

### 단점

- 메인 메모리에 값을 읽고 쓰는 행위는 캐시에 있는 값을 읽고 쓰는 것에 비해 비용이 비싸다.

### 주의사항

- 만약 여러 개의 Thread가 WRITE 하는 경우 동기화하여 원자성을 보장해야 한다.

### 예제

- Thread1이 시작되고 Thread2가 뒤늦게 시작되면 무한루프가 풀릴 것 같지만, 그렇지 않고 계속 무한 반복한다.
- 이유는 캐시를 이용하기 때문이고 해결 방법은 volatile 키워드를 붙여 해결한다.

```java
public class Main {

    boolean isLoop = true;
	// volatile boolean isLoop = true;

	public static void main(String[] args) {
		new Main().startThread();
	}

	public void startThread() {
		final Thread thread1 = new Thread(() -> {
			int count = 0;
			while (isLoop) {
				count++;
			}
			System.out.println("쓰레드 끝 " + count);
		});

		final Thread thread2 = new Thread(() -> {
			try {
				Thread.sleep(10);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
			isLoop = false;
		});
		thread1.start();
		thread2.start();
	}
}
```

## Atomic Type

- CAS 방식을 기반하여 동기화 문제를 해결한다.
- Java에서 제공하는 Atomic Type은 CAS를 하드웨어(CPU)의 도움을 받아 한 번에 하나의 스레드만 변수를 변경할 수 있도록 한다.
- java.util.concurrent.atomic 패키지에서 제공한다.

> CAS(Compare And Swap)
>
> 변수의 값을 변경하기 전에 기존에 가지고 있던 값이 내가 예상하던 값과 같은 경우에만 새로운 값으로 할당하는 방법

- 구현된 소스를 보게 되면 값은 volatile 키워드로 서로 다른 스레드가 같은 값을 참조할 수 있게 되어있다.
- compareAndSet 메소드를 보게 되면 CAS 방식으로 구현된 것을 확인할 수 있다.

```java
public class AtomicBoolean implements java.io.Serializable {
    ...
    private volatile int value;

    public final boolean compareAndSet(boolean expectedValue, boolean newValue) {
        return VALUE.compareAndSet(this,
                                   (expectedValue ? 1 : 0),
                                   (newValue ? 1 : 0));
    }
}
```

# Reference

- [synchronized](https://jgrammer.tistory.com/entry/Java-%ED%98%BC%EB%8F%99%EB%90%98%EB%8A%94-synchronized-%EB%8F%99%EA%B8%B0%ED%99%94-%EC%A0%95%EB%A6%AC)
- [volatile](https://ttl-blog.tistory.com/238)
- [atomic type](https://readystory.tistory.com/53)