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

## Data 클래스

- 데이터를 저장하기 위한 목적으로 주로 쓰이는 클래스이다.
- 클래스로 선언하면 유용한 기능들을 제공하고 컴파일러가 동등성 비교나 String으로 변환 등 기본 연산에 대한 구현을 자동으로 생성한다.
- 주생성자에 선언된 모든 프로퍼티에 의존해 `equals()`, `hashCode()`, `toString()` 등 메서드 구현을 생성해준다.

```kotlin
fun main() {
    val person = Person("Joohyun", "M")
    val person2 = Person("Joohyun", "M")
    println(person == person2) // true
    println(person) // Person(name=Joohyun, sex=M)
}

data class Person(var name: String = "", var sex: String = "") {
}
```

- 명심해야 할 것은 앞서 말한 함수들은 주생성자 프로퍼티를 기준으로 생성해주기 때문에 그 외 프로퍼티들은 영향을 미치지 못한다.

```kotlin
fun main() {
    val person = Person("Joohyun", "M")
    val person2 = Person("Joohyun", "M").apply {
        address = "Seoul"
    }
    println(person == person2) // true
}

data class Person(var name: String = "", var sex: String = "") {
    var address = ""
}
```

- `copy()`를 이용해 프로퍼티를 간단하게 복사할 수 있다.
- 기본적으로 원본 객체의 프로퍼티 값을 그대로 가져가고 이름 붙은 인자 구문으로 다른 값으로 설정할 수도 있다.

```koltin
fun main() {
    val person = Person("Joohyun", "M")
    
    val person2 = person.copy()
    println("person2 = ${person2}")
    val person3 = person.copy(name = "Hajoo")
    println("person3 = ${person3}")
}
```

- 코틀린 표준 라이브러리에는 두 가지 범용 데이터 클래스가 들어있다.
- `Pair`, `Triple`라는 클래스이고 두 개의 값과 세 개의 값을 저장할 수 있는 데이터 클래스이다.

```kotlin
fun main() {
    val pair = Pair(1, 2)
    println(pair.first)
    println(pair.second)

    val triple = Triple(1, 2, 3)
    println(triple.first)
    println(triple.second)
    println(triple.third)
}
```

### 구조 분해 선언

- 객체 프로퍼티를 추출할 때 간결한 구문으로 작성할 수 있다.

```kotlin
fun main() {
    val person = Person("Joohyun", "M")
    
    // before
    val name = person.name
    val sex = person.sex

    // after
    val (name, sex) = person
}
```

- 프로퍼티 이름으로 매칭되는 것이 아니라 생성자에 있는 각 프로퍼티의 위치에 따라서 결정된다.

```kotlin
fun main() {
    val person = Person("Joohyun", "M")

    val (name, sex) = person
    println("name = $name, sex = $sex") // Joohyun, M

    val (sex, name) = person
    println("name = $name, sex = $sex") // M, Joohyun
}
```

- 데이터 클래스의 프로퍼티보다 더 적은 수의 변수를 선언할 수 있고 사용하지 않는 변수는 `_`로 표현할 수 있다.

```kotlin
fun main() {
    val person = Person("Joohyun", "M")

    val (name) = person
    val (_, sex) = person
}
```

- 불변 변수를 가변 변수로 선언할 수 있고 부분적으로 불변/가변을 구분할 수는 없다.

```kotlin
fun main() {
    val person = Person("Joohyun", "M")

    var (name, sex) = person
}

data class Person(val name: String = "", val sex: String = "") {
}
```

- 구조 분해는 for문에서도 사용 가능하고 람다 파라미터에서도 사용할 수 있다.

```kotlin
fun main() {
    val persons = arrayOf(Person("Joohyun", "M"))
    for ((name, sex) in persons) {
    }
}
```