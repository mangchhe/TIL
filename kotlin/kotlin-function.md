# 코틀린 함수

## 기본 구조

- 파라미터 타입은 타입 추론이 불가능하므로 무조건 작성

```kotlin
fun add(src: Int, target: Int): Int {
    return src + target
}
```

- 코틀린은 자바와 달리 도달할 수 없는 코드를 작성해도 오류가 발생하지 않는다.
- 불필요한 코드이기 때문에 IDE에서 Unreachable code라고 알려준다.

```kotlin
fun add(src: Int, target: Int): Int {
    return src + target
    println("Hello World!")
}
```

- 함수의 파라미터는 무조건 불변이기 때문에 아래와 같이 값을 변경할 수 없다.

```kotlin
fun add(src: Int, target: Int): Int {
    src = 5 // error
    src++ // error
    return src + target
}
```

- 반환 타입을 생략할 수 있다.
- 생략 가능한 두 가지 경우
    - 리턴 타입이 Unit. java) void
    - 식이 본문인 함수. 단일 식으로 구현할 수 있다면 중괄호 생략이 가능
- 주의할 점으로 `fun add(src: Int, target: Int) = {src + target}` 괄호 안에 =을 붙일 경우 람다식으로 판단하기 때문에 원하는 결과가 나올 수 없다.

```kotlin
fun add(src: Int, target: Int): Int = src + target
fun add(src: Int, target: Int) = src + target
fun hello(): Unit = println("Hello World!")
fun hello() = println("Hello World!")
```

## Named Argument

- 파라미터 이름순으로 말고 파라미터 이름을 이용한 대입식을 넣어 값을 전달할 수 있다.

```java
fun main() {
    println(add(2, 3)) // positional argument
    println(add(target = 3, src = 2)) // named argument
}

fun add(src: Int, target: Int): Int {
    return src + target
}
```

## 오버로딩

- 컴파일러는 아래와 같은 규칙으로 호출할 함수를 결정한다.
    - 파라미터의 개수와 타입을 기준으로 호출할 수 있는 모든 함수를 찾는다.
    - 덜 구체적인 함수를 제외시킴. 덜 추상화된 타입으로 지정
    - 후보가 하나로 압축되면 호출. 두 개 이상이면 에러

## vararg

- 인자의 개수가 정해지지 않았을 경우 사용할 수 있다.
- 스프레드 연산자인 `*`를 사용하면 가변 인자 대신 넘길 수 있다.
- 맨 마지막 파라미터로 전달가능하고 그게 아니라면 이름을 붙여 인자로 전달할 수 있다.

```kotlin
fun main() {
    sum(1, 2, 3, 4, 5)
    sum(*intArrayOf(1, 2, 3, 4, 5))
    sum(*intArrayOf(1, 2, 3, 4, 5), 6)
}

fun sum(vararg nums: Int): Int {
    return nums.sum()
}
```

## 디폴트 값

```kotlin
fun main() {
    hello()
}

fun hello(message: String = "Hello World!") = println(message)
```

## 가시성

- public : default
- private : 함수가 정의된 파일 내에서만 사용 가능
- internal : 함수가 적용된 모듈 내부에서만 함수 사용 가능

## 조건문

- if문을 식으로 사용 가능. 3항 연산자를 사용할 필요가 없다.

```kotlin
fun main() {
    equal(1, 1)
}

fun equal(a: Int, b: Int) = if (a == b) true else false
```

- return문은 존재하지 않는 값을 뜻하는 Nothing이라는 특별한 타입의 값으로 간주한다.
- Nothing 타입은 프로그램의 순차적 제어 흐름이 그 부분에서 끝나되 어떤 잘 정의된 값에 도달하지 못한다는 뜻이다.
- 모든 코틀린 타입의 하위 타입으로 간주하기 때문에 아래와 같이 사용해도 에러가 발생하지 않는다.

```kotlin
fun main() {
    equal(1, 1)
}

fun equal(a: Int, b: Int): Boolean {
    if (a == b) true else return false
    return true
}
```

## 범위, 진행, 연산

```kotlin
fun main() {
    // 범위
    'a'..'h'
    10..99
    15 in 10..99
    15 !in 10..99
    10 until 100
    10 downTo 1

    // 진행
    1..10 step 3
    10 downTo 1 step 3
    
    // 연산
    "Hello World!".substring(0..4)
    IntArray(10) {it * it}.sliceArray(4..6)
}
```

## when

- 비슷한 개념으로 자바의 switch가 있다. fall through라는 의미를 제공하고 명시적으로 break를 만날 때까지 모든 가지를 실행한다.
- when은 만족하는 가지만 실행하고 fall through 하지 않는다. 그 외에도 범위 검사, 상수가 아닌 임의의 식도 사용할 수 있다.

```kotlin
fun getGrade(n: Int): String {
    return when (n) {
        1 -> "1학년"
        2 -> "2학년"
        3 -> "3학년"
        else -> "유급생"
    }
}
```

## for

- 코틀린 문자열에 대해서 각 문자에 대해서 루프를 직접 수행할 수 있다.

```kotlin
fun main() {
    for (c in "Hello World!") {
        // TODO
    }
}
```

## Break, Continue, throws

- return과 마찬가지로 Nothing 타입의 식으로 사용할 수 있다.

## 레이블

- `outerLoop@ for`, `continue@outerLoop`
- `loop@ while(true)`, `break@loop`

## 꼬리 재귀 함수

- `tailrec`를 붙이게 되면 컴파일러가 재귀 함수를 비재귀적인 코드로 자동으로 변환해줘서 성능 이점을 얻을 수 있다.
- 이러한 변환을 적용하려면 재귀 호출한 다음 함수가 아무 동작을 하지 않아야 한다.
- 만약 꼬리재귀가 아니라면 컴파일러는 경고를 표시하고 일반 재귀 함수로 컴파일한다.

## References

- [코틀린 완벽 가이드](https://books.google.co.kr/books/about/%EC%BD%94%ED%8B%80%EB%A6%B0_%EC%99%84%EB%B2%BD_%EA%B0%80%EC%9D%B4%EB%93%9C.html?id=851kEAAAQBAJ&source=kp_book_description&redir_esc=y)