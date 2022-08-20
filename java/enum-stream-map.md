# Enum 조회 성능 높이기

## Stream

- 보통 조회할 때 Stream으로 반복문을 돌려서 찾아낸다. 즉, 한번 조회할 때마다 O(n)이 걸리게 될 것이다.
- 매번 찾아서 조회해야 하므로 시간이 오래 걸리게 되고 Enum 값이 많아질수록 더욱 느려질 것이다.

```java
@Getter
public enum Role {

    ADMIN("관리자"),
    USER("사용자");

    private String value;

    Role(String value) {
        this.value = value;
    }

    public static Role getByArraysStream(String value) {
        return Arrays.stream(Role.values())
            .filter(v -> value.equals(v.getValue()))
            .findAny()
            .orElseThrow();
    }
}
```

## Map

- Enum 값을 처음에 Map 자료구조를 이용해서 저장해놓게 된다면 한번 조회할 때마다 O(1)의 속도가 걸리게 된다.

```java
@Getter
public enum Role {

    ADMIN("관리자"),
    USER("사용자");

    private String value;

    Role(String value) {
        this.value = value;
    }

    public static final Map<String, Role> values = Collections.unmodifiableMap(
        Stream.of(Role.values())
            .collect(Collectors.toMap(Role::getValue, Function.identity())));

    public static Role findByMap(String value) {
        return Optional.ofNullable(values.get(value))
            .orElseThrow();
	}
}
```

## References

- [https://pjh3749.tistory.com/279](https://pjh3749.tistory.com/279)