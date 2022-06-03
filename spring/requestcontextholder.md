# RequestContextHolder

- Spring Framework에서 전 구간(Layer)에서 HttpServletRequest에 접근할 수 있게 해준다.
- 원래라면 번거롭게 컨트롤러에서 값을 받아와 파라미터로 서비스로 넘겨줘야 했지만 그럴 필요가 없어졌다.

## 사용 방법

```java
final HttpServletRequest request = ((ServletRequestAttributes)RequestContextHolder.getRequestAttributes()).getRequest();
```

## 디버깅

- 모르는 부분은 많지만 어디서 사용되는지 따라가보자.

<img width="714" alt="requestcontextholder" src="https://user-images.githubusercontent.com/50051656/171878488-444c2c61-8792-455e-8818-376675049c54.png">

- RequestContextHolder는 ThreadLocal 객체로 RequestAttributes를 값으로 가진다.

> ThreadLocal
>
> 각 스레드가 독립적인 저장 공간을 가질 수 있도록 함
> 개념 정리 -> [링크](https://github.com/mangchhe/TIL/blob/main/java/threadlocal.md)

- 그러면 이 값들은 언제 할당되고 해제되는 것일까?

<img width="903" alt="requestcontextholder2" src="https://user-images.githubusercontent.com/50051656/171880209-3f4f3c59-da23-42fd-818b-3da25da5953f.png">

- RequestContextFilter 클래스로 찾아 들어가면 initContextHolders가 필터 체인을 타고 다음 필터로 가기 전에 호출되고 ServletRequestAttributes 값을 인자로 넣어 설정하는 것을 알 수 있다.
- 그리고 비즈니스 로직이 모두 끝나고 resetContextHolders를 호출하여 해당 스레드 ThreadLocal 객체를 비우게 된다.

<img width="751" alt="requestcontextholder3" src="https://user-images.githubusercontent.com/50051656/171880722-ded7dad9-77ee-4c9b-9e41-13251554d7cb.png">

- 해당 필터는 JerseyAutoConfiguration 클래스에 filter로 등록된 것을 확인할 수 있다.

## 실습

### Controller

- 쿠키 정보와 Requset Body를 가져오는 EndPoint 두 개 구현

```java
@RestController
@RequiredArgsConstructor
public class RequestContextHolderController {

	private final RequestContextHolderService requestContextHolderService;

	@GetMapping
	public Map<String, String> getCookie() {
		return requestContextHolderService.getCookies();
	}

	@PostMapping
	public String getBody() {
		return requestContextHolderService.getBody();
	}
}
```

### Service

- RequestContextHolder를 이용하여 HttpServletRequest를 얻어 쿠키와 Request Body를 가져오는 로직 구현

```java
@Service
public class RequestContextHolderService {

	public Map<String, String> getCookies() {
		final HttpServletRequest request = ((ServletRequestAttributes)RequestContextHolder.getRequestAttributes()).getRequest();
		final HashMap<String, String> ret = new HashMap<>();
		for (Cookie cookie : request.getCookies()) {
			ret.put(cookie.getName(), cookie.getValue());
		}
		return ret;
	}

	public String getBody() {
		final HttpServletRequest request = ((ServletRequestAttributes)RequestContextHolder.getRequestAttributes()).getRequest();
		try {
			return StreamUtils.copyToString(request.getInputStream(), StandardCharsets.UTF_8);
		} catch (IOException e) {
			e.printStackTrace();
		}
		return "";
	}
}
```

### 결과

```
GET /
{
    "cookieName2": "cookieValue2",
    "cookieName1": "cookieValue1"
}
POST / 
{
    "username" : "username",
    "age" : 26
}
```

### 정적 유틸 클래스 구현

- 자주 사용될 수 있으므로 유틸 클래스로 만들어 중복 코드를 제거하자.
- 아래 예제뿐 아니라 쿠키나 Request Body 요청에 대한 값을 가져올 때 중복되는 것이 코드가 있다면 메소드로 추출하자.

```java
public class RequestUtils {

	public static HttpServletRequest getRequest() {
		return ((ServletRequestAttributes)RequestContextHolder.getRequestAttributes()).getRequest();
	}
}
```

```java
@Service
public class RequestContextHolderService {

	public Map<String, String> getCookies() {
		final HttpServletRequest request = RequestUtils.getRequest();
		...
	}

	public String getBody() {
		final HttpServletRequest request = RequestUtils.getRequest();
		...
	}
}
```

