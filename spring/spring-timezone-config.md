# Spring Boot Timezone 설정

## 첫 번째 방법

- jar 파일 실행시킬 때 TimeZone 옵션 설정.

```java
java -jar -Duser.timezone=Asia/Seoul <>.jar
```

## 두 번째 방법

- 애플리케이션이 실행 후 TimeZone 설정.

```java
@SpringBootApplication
public class TimezoneApplication {

	@PostConstruct
	public void init() {
		TimeZone.setDefault(TimeZone.getTimeZone("UTC"));
	}

	public static void main(String[] args) {
		SpringApplication.run(TimezoneApplication.class, args);
	}

}
```

- Java 8에 추가된 java.time 패키지에 LocalXXXXXX 클래스들은 TimeZone 설정값에 의해 계산된다.

### TimeZone 설정

```java
public static void setDefault(TimeZone zone)
{
    @SuppressWarnings("removal")
    SecurityManager sm = System.getSecurityManager();
    if (sm != null) {
        sm.checkPermission(new PropertyPermission
                            ("user.timezone", "write"));
    }
    // by saving a defensive clone and returning a clone in getDefault() too,
    // the defaultTimeZone instance is isolated from user code which makes it
    // effectively immutable. This is important to avoid races when the
    // following is evaluated in ZoneId.systemDefault():
    // TimeZone.getDefault().toZoneId().
    defaultTimeZone = (zone == null) ? null : (TimeZone) zone.clone();
}
```

### LocalDateTIme

```java
LocalDateTime.now();

// LocalDateTime Class
public static LocalDateTime now() {
    return now(Clock.systemDefaultZone());
}

// Clock Class
public static Clock systemDefaultZone() {
    return new SystemClock(ZoneId.systemDefault());
}

// ZoneId Class
public static ZoneId systemDefault() {
    return TimeZone.getDefault().toZoneId();
}

// TimeZone Class
public static TimeZone getDefault() {
    return (TimeZone) getDefaultRef().clone();
}

// TimeZone Class
static TimeZone getDefaultRef() {
    TimeZone defaultZone = defaultTimeZone;
    if (defaultZone == null) {
        defaultZone = setDefaultZone();
        assert defaultZone != null;
    }
    return defaultZone;
}
```