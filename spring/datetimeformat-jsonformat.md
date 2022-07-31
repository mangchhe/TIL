# 날짜 다루기 - @DateTimeFormat, @JsonFormat

- 해당 예제는 `spring-boot:2.7.1`를 기준으로 실습함.

## Deserialization

- `@ModelAttribute`, `@RequestParam`, `@RequestBody` 세 가지 방법을 이용해 사용자가 전달한 값을 받을 수 있다.

### `@ModelAttribute`

- `@DateTimeFormat`가 없으면 `ConversionFailedException`이 발생하기 때문에 반드시 필요하다.

```java
public class TimeDto {

	@DateTimeFormat(pattern = "yyyy-MM-dd'T'HH:mm:ss")
	private LocalDateTime date;
}
```

```java
@GetMapping("/hello")
public void hello(@ModelAttribute TimeDto date) {
}
```

```http
GET localhost:8080/hello?date=2020-12-02T10:00:00
```

### `@RequestParam`

- `@DateTimeFormat`가 없으면 `ConversionFailedException`이 발생하기 때문에 반드시 필요하다.

```java
public class TimeDto {

	@DateTimeFormat(pattern = "yyyy-MM-dd'T'HH:mm:ss")
	private LocalDateTime date;
}
```

```java
@GetMapping("/hello")
public void hello(@RequestParam @DateTimeFormat(pattern = "yyyy-MM-dd'T'HH:mm:ss") LocalDateTime date) {
}
```

```http
GET localhost:8080/hello?date=2020-12-02T10:00:00
```

### `@RequestBody`

- `@DateTimeFormat`가 없어도 직렬화가 가능하다.
- 원하는 포맷이 필요할 때는 `@JsonFormat`를 이용해 작성하면 된다.

```java
public class TimeDto {

	@JsonFormat(shape = JsonFormat.Shape.STRING, pattern = "yyyy-MM-dd'T'HH:mm:ss", timezone = "Asia/Seoul")
	private LocalDateTime date;
}
```

```java
@GetMapping("/hello")
public void hello(@RequestBody TimeDto date) {
}
```

```http
GET localhost:8080/hello
Content-Type: application/json

{
  "date" : "2020-12-02T10:00:00"
}
```

## Serialization

- 기본적으로 아무 설정을 해주지 않으면 `2022-07-30T23:32:12.604042` 형태로 값을 전달해준다.
- 원하는 포맷이 필요할 때는 `@JsonFormat`를 이용해 작성하면 된다.

```java
public class TimeDto {

	@JsonFormat(shape = JsonFormat.Shape.STRING, pattern = "yyyy-MM-dd'T'HH:mm:ss", timezone = "Asia/Seoul")
	private LocalDateTime date;
}
```

```java
@GetMapping("/hello")
public TimeDto hello() {
    return new TimeDto(LocalDateTime.now());
}
```

```http
GET localhost:8080/hello
```

## @DateTimeFormat vs @JsonFormat

- `@DateTimeFormat`은 속한 패키지가 `org.springframework.format.annotation`로 스프링 어노테이션이다.
- `@JsonFomrat`은 속한 패키지가 `com.fasterxml.jackson.annotation`로 jackson 어노테이션이다.
- 그렇다면 위에서 방법마다 사용했던 어노테이션이 다른 이유를 설명할 수 있다.
- `@RequestBody`, `@ResponseBody`는 jackson objectmapper에 의해 직/역직렬화가 발생하기 때문에 `@JsonFormat`에 의해 영향을 받는다.
- 그 외 `@ModelAttribute`, `@RequestParam`는 그렇지 않기 때문에 `@DateTimeFormat`를 이용해야 한다.

## References

- [https://jojoldu.tistory.com/361#recentComments](https://jojoldu.tistory.com/361#recentComments)