# 특별한 클래스 (Enum, Data, Inline)

## Enum 클래스

- `enum class` 키워드를 붙여서 사용한다.
- 전역 상수로 사용할 수 있는 방법이 없는 위치에서는 사용할 수 없다. 즉, 내부 클래스나 함수 본문에서는 선언할 수 없다.

```kotlin
fun main() {
    println(StoreStatus.OPEN)
    println(StoreStatus.CLOSE)
}

enum class StoreStatus {
    OPEN, CLOSE;
}
```

### when

- 모든 Enum 상수를 다루는 경우 else를 생략할 수 있다.
- 이점은 새로운 Enum 상수가 추가되게 되면 when 절에 상숫값을 추가하거나 else 절을 넣어야 컴파일 에러가 안 나기 때문에 그런 실수를 사전에 방지할 수 있다.

```kotlin
fun main() {
    isOpen(StoreStatus.OPEN)
}

fun isOpen(storeStatus: StoreStatus): Boolean = when(storeStatus) {
    StoreStatus.OPEN -> true
    StoreStatus.CLOSE -> false
    StoreStatus.BREAK -> true
}

enum class StoreStatus {
    OPEN, CLOSE, BREAK;
}
```

### 커스텀 멤버 정의

- 상숫값 이외에 함수 프로퍼티 등을 정의할 수 있고 상숫값 뒤에 `;`를 적고 정의하면 된다.

```kotlin
fun main() {
    println(StoreStatus.OPEN.isOpen())
    println(StoreStatus.CLOSE.isOpen())
}

enum class StoreStatus {
    OPEN, CLOSE;

    fun isOpen(): Boolean {
        return this == OPEN
    }
}
```

```kotlin
fun main() {
    println(StoreStatus.OPEN.isOpen)
    println(StoreStatus.OPEN.isClose)
}

enum class StoreStatus(val isOpen: Boolean){
    OPEN(true), CLOSE(false), BREAK(true);

    val isClose get() = !isOpen
}
```

### Enum 공통 멤버

- enum은 kotlin.enum의 하위 타입이고 몇 가지 공통 함수와 프로퍼티를 제공한다.
- enum 값의 비교는 ordinal 값에 따라 비교되고 동등성은 각각의 식별자에 따라 정해진다.

```kotlin
fun main() {
    println(StoreStatus.OPEN.name) // 상숫값
    println(StoreStatus.OPEN.ordinal) // 정의된 상수 순서에 따른 인덱스

    StoreStatus.valueOf("OPEN") // 상숫값을 문자열로 넘기면 일치하는 enum 값 반환
    for (value in StoreStatus.values()) { // 정의된 enum 값 반환 OPEN, CLOSE, BREAK
        value
    }

    enumValueOf<StoreStatus>("OPEN") // valueOf
    enumValues<StoreStatus>() // values
}
```