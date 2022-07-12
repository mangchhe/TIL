# Intellij .http 사용 방법

## 목차

- [Separator](#Separator)
- [환경 및 프로퍼티 설정](#환경-및-프로퍼티-설정)
- [GET](#GET)
- [POST](#POST)
- [PUT](#PUT)
- [DELETE](#DELETE)
- [PATH VARIABLE](#PATH-VARIABLE)
- [MODEL(Request Param)](#MODEL(Request-Param))
- [BODY(JSON)](#BODY(JSON))
- [Cookie 발급](#Cookie-발급)
- [Cookie 전달](#Cookie-전달)
- [권한 인증](#권한-인증)
- [메타데이터](#메타데이터)

## Separator

- 각 엔드포인트는 `###`로 구분한다.

```r
GET http://localhost:8080

###

POST http://localhost:8080
```

## 환경 및 프로퍼티 설정

- `rest-client.env.json` 파일에 파일에 값을 정의

```r
{
  "local" : {
    "domain" : "http://localhost:8080"
  },
  "test" : {
    "domain" : "http://localhost:9090"
  }
}
```

- `{{value}}` 설정값을 감싸서 설정하고 env 별로 값을 주입할 수 있다.
- 서로 다른 환경에서 다른 환경 세팅을 해야 할 경우 유용하다.
 
```r
GET {{domain}}
```

## GET

```java
@GetMapping
public String get() {
    return "Hello World!";
}
```

```r
GET http://localhost:8080
```

## POST

```java
@PostMapping
public String post() {
    return "Hello World!";
}
```

```r
POST http://localhost:8080
```

## PUT

```java
@PutMapping
public String put() {
    return "Hello World!";
}
```

```r
PUT http://localhost:8080
```

## DELETE

```java
@DeleteMapping
public String delete() {
    return "Hello World!";
}
```

```r
DELETE http://localhost:8080
```

## PATH VARIABLE

```java
@GetMapping("path-variable/{value}")
public String getPathVariable(@PathVariable String value) {
    return value;
}
```

```r
GET http://localhost:8080/path-variable/hello world!!
```

## MODEL(Request Param)

```java
@PostMapping("model")
public String getModel(@ModelAttribute UserDto userDto) {
    return userDto.getUsername() + " " + userDto.getPassword();
}
```

```r
POST http://localhost:8080/model?username=홍길동&password=패스워드
```

## BODY(JSON)

```java
@PostMapping("body")
public String getBody(@RequestBody UserDto userDto) {
    return userDto.getUsername() + " " + userDto.getPassword();
}
```

```r
POST http://localhost:8080/body
Content-Type: application/json

{
  "username" : "홍길동",
  "password" : "길동이 패스워드"
}
```

## Cookie 발급

```java
@PostMapping("res-cookie")
public String getResCookie(HttpServletResponse response) {
    response.addCookie(new Cookie("token", "token-value"));
    return "쿠키 발급";
}
```

```r
POST http://localhost:8080/res-cookie
```

## Cookie 전달

```java
@PostMapping("req-cookie")
public String getReqCookie(HttpServletRequest request) {
    String token = WebUtils.getCookie(request, "token").getValue();
    return "쿠키 value : " + token;
}
```

```r
POST http://localhost:8080/req-cookie
Content-Type: application/json
Cookie: token=test
```

### 다른 방법

- `http-client.cookies` 파일에 값을 정의
- 파일 설정 값이 우선순위가 더 높음

```r
# domain	path	name	value	date
localhost	/	token	token-value	-1
```

## 권한 인증

```java
@PostMapping("req-authorization")
public String getAuthorization(HttpServletRequest request) {
    String value = request.getHeader("Authorization");
    return "Authorization value : " + value;
}
```

```r
POST http://localhost:8080/req-authorization
Authorization: Bearer test
```

## 메타데이터

- @no-cookie-jar : 요청을 날려 쿠키를 받게 되면 자동으로 저장되어 세팅되게 되는데 그것을 방지할 때 사용
- @no-log : 로그를 남기지 않을 때 사용
- @no-redirect : 요청을 리다이렉트 시키려고 하는 것을 막을 때 사용

### 사용 방법

- `//` 뒤에 적용하기 원하는 기능 작성
- 여러 개 적용을 원할 경우 아래처럼 여러 개 작성하면 된다.

```r
// @no-cookie-jar
// @no-log
POST http://localhost:8080/res-cookie
```