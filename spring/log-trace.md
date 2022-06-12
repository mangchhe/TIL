# 로그 추적기

- 서비스를 동작시킬 때 로그를 기록하는 것은 매우 중요하다!
- 로그가 없다면 장애가 발생했을 때 어디서 발생했는지 어디서부터 따라가야 하는지 찾기 쉽지 않다.
- 이번에는 로그를 보기 편리하게 기록하는 방법에 대해서 알아보자.
- 로그 추적기를 구현하면 끝으로 아래와 같이 로그가 표현되는 것을 볼 수 있을 것이다.
- 예제 소스 : [→](https://github.com/TIL-Repo/spring-study/tree/main/logtrace)

<img width="412" alt="logtraceResult" src="https://user-images.githubusercontent.com/50051656/173230218-3749a515-b871-4ba1-877b-ca71d587f231.png">

- 로그 추적기를 구현하기 위해서는 ThreadLocal에 대한 개념을 알고 있어야 동시성 문제를 해결할 수 있다.
- 참고 : [→](https://github.com/mangchhe/TIL/blob/main/java/threadlocal.md)

## 구현

### TraceId

- 스레드가 가지는 고유 값으로 UUID 앞 8자리 id와 현재 계층 depth인 level을 가지는 TraceId 구현


```java
public class TraceId {

	private String id;
	private int level;

	public TraceId() {
		this.id = createId();
		this.level = 0;
	}

	private TraceId(String id, int level) {
		this.id = id;
		this.level = level;
	}

	private String createId() {
		return UUID.randomUUID().toString().substring(0, 8);
	}

	public TraceId createNextId() {
		return new TraceId(id, level + 1);
	}

	public TraceId createPreviousId() {
		return new TraceId(id, level - 1);
	}

	public boolean isFirstLevel() {
		return level == 0;
	}

	... getter
}
```

### TraceStatus

- 위에서 구현한 스레드 고유 ID 값과 depth를 가지는 TraceId와 현재 시간, 현재 위치 등 필요한 정보를 담는 message를 가지는 TraceStatus 구현

```java
public class TraceStatus {

	private TraceId traceId;
	private Long startTimeMs;
	private String message;

	public TraceStatus(TraceId traceId, Long startTimeMs, String message) {
		this.traceId = traceId;
		this.startTimeMs = startTimeMs;
		this.message = message;
	}

	... getter
}
```

- 로그 추적기 동작 구현
- ThreadLocal<TraceId> 를 만들어 스레드별로 다른 TraceId를 가질 수 있도록 한다. 이렇게 하지 않으면 동시성 문제가 발생할 수 있다.
- begin, end, exception : 로그 시작과 끝, 그리고 예외가 발생했을 때 호출되는 메소드이다.
- complete : end, exception 시에 로그 로직 구현
- syncTraceId : 존재하는 로그 추적이 시작되었으면 즉, traceId가 있다면 level+1을 하고 TraceId를 생성하여 저장하고 그렇지 않다고 새로 생성한다.
- releaseTraceId : 로그 추적이 종료된다면 ThreadLocal을 remove를 통해 비워야 다음 요청이 올 때 문제가 발생하지 않는다.
- addSpace : level 별로 log indent 설정

```java
public interface LogTrace {

	TraceStatus begin(String message);

	void end(TraceStatus status);

	void exception(TraceStatus status, Exception e);
}

@Slf4j
public class FieldLogTraceImpl implements LogTrace {

	private static final String START_PREFIX = "-->";
	private static final String COMPLETE_PREFIX = "<--";
	private static final String EX_PREFIX = "<X-";

	private ThreadLocal<TraceId> traceIdHolder = new ThreadLocal<>();

	@Override
	public TraceStatus begin(String message) {
		syncTraceId();
		TraceId traceId = traceIdHolder.get();
		Long startTimeMs = System.currentTimeMillis();
		log.info("[{}] {}{}", traceId.getId(), addSpace(START_PREFIX, traceId.getLevel()), message);
		return new TraceStatus(traceId, startTimeMs, message);
	}

	private void syncTraceId() {
		TraceId traceId = traceIdHolder.get();
		if (traceId == null) {
			traceIdHolder.set(new TraceId());
		} else {
			traceIdHolder.set(traceId.createNextId());
		}
	}

	@Override
	public void end(TraceStatus status) {
		complete(status, null);
	}

	@Override
	public void exception(TraceStatus status, Exception e) {
		complete(status, e);
	}

	private void complete(TraceStatus status, Exception e) {
		long stopTimeMs = System.currentTimeMillis();
		long resultTimeMs = stopTimeMs - status.getStartTimeMs();
		TraceId traceId = status.getTraceId();
		if (e == null) {
			log.info("[{}] {}{} time={}ms", traceId.getId(), addSpace(COMPLETE_PREFIX, traceId.getLevel()),
				status.getMessage(), resultTimeMs);
		} else {
			log.info("[{}] {}{} time={}ms ex={}", traceId.getId(), addSpace(EX_PREFIX, traceId.getLevel()),
				status.getMessage(), resultTimeMs, e.toString());
		}

		releaseTraceId();
	}

	private void releaseTraceId() {
		TraceId traceId = traceIdHolder.get();
		if (traceId.isFirstLevel()) {
			traceIdHolder.remove();
		} else {
			traceIdHolder.set(traceId.createPreviousId());
		}
	}

	private static String addSpace(String prefix, int level) {
		StringBuilder sb = new StringBuilder();
		for (int i = 0; i < level; i++) {
			sb.append((i == level - 1) ? "|" + prefix : "|  ");
		}
		return sb.toString();
	}
}
```

### Bean 등록

```java
@Configuration
public class LogTraceConfig {

	@Bean
	public LogTrace logTrace() {
		return new FieldLogTraceImpl();
	}
}
```

### 계층별 로그 기록

- 기본 뼈대는 아래와 같고 주석 부분에 비즈니스 로직을 넣으면 된다.
- 예외 처리 부분에서 다시 예외를 던져주는 이유는 로그를 남기기 위해서 애플리케이션의 정상 흐름을 방해해서는 안 되기 때문이다.
- AOP를 적용하면 주요 비즈니스 로직과 분리하여 관리하고 중복 코드를 줄일 수 있을 것으로 보인다.

```java
TraceStatus status = null;
try {
    status = trace.begin("로그 남길 정보");
    // 비즈니스 로직
    trace.end(status);
} catch (Exception e) {
    trace.exception(status, e);
    throw e;
}
```

#### Controller

```java
public class OrderControllerV2 {

	private final OrderServiceV2 orderServiceV2;
	private final LogTrace trace;

	@GetMapping("v2/request")
	public String request(String itemId) {
		TraceStatus status = null;
		try {
			status = trace.begin("OrderController.request()");
			orderServiceV2.orderItem(itemId);
			trace.end(status);
		} catch (Exception e) {
			trace.exception(status, e);
			throw e;
		}
		return "ok";
	}
}
```

#### Service

```java
public class OrderServiceV2 {

	private final OrderRepositoryV2 orderRepositoryV2;
	private final LogTrace trace;

	public void orderItem(String itemId) {
		TraceStatus status = null;
		try {
			status = trace.begin("OrderService.orderItem()");
			orderRepositoryV2.save(itemId);
			trace.end(status);
		} catch (Exception e) {
			trace.exception(status, e);
			throw e;
		}
	}
}
```

#### Repository

```java
public class OrderRepositoryV2 {

	private final LogTrace trace;

	public void save(String itemId) {
		TraceStatus status = null;
		try {
			status = trace.begin("OrderRepository.save()");

			if (itemId.equals("ex")) {
				throw new IllegalStateException("예외 발생");
			}

			trace.end(status);
		} catch (Exception e) {
			trace.exception(status, e);
			throw e;
		}
	}
}
```

## Reference

- [스프링 핵심 원리 - 고급편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B3%A0%EA%B8%89%ED%8E%B8/dashboard)