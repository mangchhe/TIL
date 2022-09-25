# 코틀린 기본 문법

## 타입 추론

- 타입 추론 기능을 제공하기 때문에 컴파일러가 코드 문맥에서 타입을 도출해준다.
- 초깃값을 주지 않으면 타입을 명시해주어야 한다.

```kotlin
fun main() {
    var text = "Hello World!"
    var num = 1

    val text2: String
    val num2: Int
}
```

## 작은역따옴표(`) 식별자

- **`**를 감싸면 그 자체가 식별자가 된다.
- 빈 문자열을 제외한 아무 문자열이나 와도 상관없다.

```kotlin
fun main() {
    val `hello world` = "Hello World!"
}
```

## var vs val

- `var` : mutable type
- `val` : immutable type

```kotlin
fun main() {
    var text = "Hello World!"
    val text2 = "Hello World!"

    text = "수정"
    text2 = "수정2" // val cannot be reassigned
}
```

## 기본 타입

- 코틀린은 똑같은 타입이 문맥에 따라 원시 타입과 참조 타입을 가리키기 때문에 구분이 모호하다.
- 자바와 달리 코틀린 타입은 근본적으로 어떤 클래스 정의를 기반을 만들어진다. 즉, Int와 같이 원시 타입과 비슷한 타입들도 메서드와 프로퍼티를 제공한다.
- 타입은 하위 타입이라는 개념으로 계층화할 수 있다. 상위 타입에 하위 타입의 값을 넣어도 아무 문제 없다.

```kotlin
val n: Any = 1 // 모든 코틀린 타입은 NULL을 허용하지 않는 Any라는 내장 타입의 직/간접적인 하위 타입이다.
```

### Char

- 자바에서 char 타입의 산술 연산은 암시적으로 정수로 변환되지만, 코틀린에서는 char 타입 결과로 돌려준다.

```kotlin
fun main() {
    print('a' + 1) // b
    print('a' + 2) // c
}
```

### 수 변환

- 범위가 큰 타입이 사용돼야 하는 문맥에 범위가 작은 타입은 쓸 수 없다. 이유는 암시적 박싱 때문이다.
- Int 값은 원시 타입으로 값을 표현된다는 보장이 없다.

```kotlin
fun main() {
    val n = 100
    val l: Long = n // n.toLong()
}
```

### 논리 연산

- ||, && : 지연 계산
- or, and, xor : 즉시 계산. java) |, &, ^

> 지연 계산 (lazy), 즉시 계산 (eager)
>
> 왼쪽 피연산자만으로 결과가 나게 되면 오른쪽은 더 이상 계산하지 않는 것이 지연 계산
> 결과가 나더라도 모든 조건식을 계산하는 것을 즉시 계산

### 동등성

- 코틀린 타입은 두 인자가 모두 같은 타입일 경우에 ==. != 을 허용한다.
- 이유는 앞서 말했듯이 박싱이 암시적으로 진행되기 때문에 이러한 비교를 허용하게 되면 혼란이 생긴다.
- 자바는 원시 타입과 참조 타입이 명백하게 구분되어있기 때문에 ==, !=도 가능하지만, 코틀린은 그렇지 않다.

```kotlin
fun main() {
    val l = 10L
    val i = 5

    print(l == i)
}
```

## 문자열

### 문자열 템플릿

- 코틀린은 문자열을 합성해내는 강력한 방법을 지원한다.
- `${}`를 이용하면 어떤 식이든 문자열이든 넣을 수 있고 간단한 변수 참조인 경우 괄호 없이 `$` 뒤에 변수를 붙여 나타낼 수 있다.

```kotlin
fun main() {
    val l = 10L
    val i = 5

    print("$i, $l, ${Date()}")
}
```

### 기본 문자열 연산

- 자바의 ==, !=는 기본적으로 동일성 비교지만 코틀린은 기본적으로 동등성 비교이다.
- 코틀린에서 동일성 비교를 하기 위해서는 ===. !==를 사용하면 된다.

```kotlin
fun main() {
    val text = "Hello World!"
    val text2 = "Hello World!"

    println(text.length)
    println(text.lastIndex)
    println(text[4])
    println(text == text2)
    println(text.isEmpty())
    println(text.isNotEmpty())
    println(text.substring(0, 5))
    println(text.startsWith("Hello"))
    println(text.endsWith("World!"))
    println(text.indexOf('o'))
}
```

## 배열

- 배열 구조를 구현하는 일반적인 타입 `Array<T>`

```kotlin
fun main() {
    val a = emptyArray<String>()
    val b = arrayOf<String>()
    val c = arrayOf("Hello World!")
    val d = arrayOf("Hello World!", 3)
    val e = Array(5) { it * it }
    val f = IntArray(5) { it * it }
}
```

### 배열 사용법

```kotlin
fun main() {
    val a = arrayOf(1, 2, 3, 4)
    a.size
    a.lastIndex
    a[3]
    val ints = intArrayOf(1, 2, 3) + intArrayOf(1, 2, 3) // 1, 2, 3, 1, 2, 3
}
```

### 배열 복제

```kotlin
fun main() {
    val a = arrayOf(1, 2, 3, 4)
    val b = a
    b[3] = 5 // a[3] == 5
    val c = a.copyOf()
    c[3] = 6 // a[3] == 4

    val strings = arrayOf("one", "two", "three")
    val objects: Array<Any> = strings
}
```

### 배열 계층 구조

- 코틀린 배열 타입은 모든 배열 타입과 서로 하위 타입 관계가 성립되지 않는다.

```kotlin
fun main() {
    val texts = arrayOf("one", "two", "three")
    val objs: Array<Any> = texts
}
```

### 동등성

```kotlin
fun main() {
    println(intArrayOf(1, 2, 3) == intArrayOf(1, 2, 3)) // false
    println(intArrayOf(1, 2, 3) === intArrayOf(1, 2, 3)) // false
    println(intArrayOf(1, 2, 3).equals(intArrayOf(1, 2, 3))) // false
    println(intArrayOf(1, 2, 3).contentEquals(intArrayOf(1, 2, 3))) // true
}
```

## References

- [코틀린 완벽 가이드](https://books.google.co.kr/books/about/%EC%BD%94%ED%8B%80%EB%A6%B0_%EC%99%84%EB%B2%BD_%EA%B0%80%EC%9D%B4%EB%93%9C.html?id=851kEAAAQBAJ&source=kp_book_description&redir_esc=y)