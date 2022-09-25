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