# HttpOnly, Secure - Cookie

- HTTP는 Stateless 특징을 가지기 때문에 서로의 상태를 저장하지 않기 때문에 쿠키라는 것을 이용한다.
- 쿠키는 여러 가지의 상태 정보를 담을 수 있고 그중에서 일부는 사용자 인증을 위해서도 사용이 되기도 한다.
- 이때 쿠키에 보안 설정을 해주지 않으면 해커가 쿠키를 탈취해서 악용할 수 있다. 그중 대표적으로 하이재킹 공격이 있다.

> 세션 하이재킹 공격(Session Hijacking Attack)
>
> 로그인된 상태를 가로채는 것을 말한다. 즉 인증 쿠키를 탈취해 해커가 서버와 통신할 수 있는 것을 말한다.

## HttpOnly

```html
Set-Cookie: key=value; path=/; HttpOnly
```

- 자바스크립트를 통해서 쿠키에 접근하는 것을 할 수 없도록 하는 것을 말한다.
- 대표적인 공격으로 XSS(Cross Site Scripting)로 게시판이나 이미지에 자바스크립트를 주입하여 쿠키를 탈취할 수 있기에 방지하려면 HttpOnly 옵션을 설정해야 한다.
- 다음은 네이버에서 생성된 쿠키들이고 몇몇 쿠키들이 HttpOnly 설정이 된 것을 확인할 수 있다.

![httponly](https://user-images.githubusercontent.com/50051656/172626070-0800665a-7a3f-41d5-9873-d2709b738a91.png)

### 적용 방법

```java
Cookie cookie = new Cookie(key, value);
cookie.setHttpOnly(true);
```

## Secure

```html
Set-Cookie: key=value; path=/; Secure
```

- HttpOnly로 자바스크립트로 접근하는 것은 막았지만 네트워크를 감청하여 탈취하는 것은 막을 수 없다.
- Secure 옵션을 주게 되면 HTTPS에서만 쿠키를 전달할 수 있기 때문에 네트워크 통신 중에도 안전하다.

### 적용 방법

```java
Cookie cookie = new Cookie(key, value);
cookie.setSecure(true);
```