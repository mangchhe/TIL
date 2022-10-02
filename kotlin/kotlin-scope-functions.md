# 영역 함수 (run, with, let, apply, also)

- 어떤 식을 계산한 값을 문맥 내부에서 임시로 사용할 수 있도록 해주는 몇 가지 함수가 존재한다.
- 영역 함수가 하는 일은 **인자로 제공한 람다를 간단하게 실행해주는 것**이다.
- 모든 영역 함수는 인라인 함수이므로 런타임 부가 비용이 들지 않는다.

## run

- 확장 람다를 받는 확장 함수이며 람다의 결과를 돌려준다.
- 객체 상태를 설정한 다음, 해당 객체를 대상으로 어떤 결과를 만들어내는 람다를 호출한다.

```kotlin
fun main() {
    Person().run {
        name = "Joohyun"
        sex = "M"
        isMale()
    }
}

class Person {
    var name: String = ""
    var sex: String = ""

    fun isMale(): Boolean {
        return sex == "M"
    }
}
```

## 문맥 없는 run

- 문맥 식이 없고 람다의 값을 반환하기만 하는 `run()` 오버로딩한 함수를 제공한다.

```kotlin
fun main() {
    run {
        val name = readLine() ?: return
        val sex = readLine() ?: return
        Person(name, sex)
    }
}
```

## with

- `run()`과 매우 유사하며 차이점은 `with()`는 확장 함수가 아니므로 문맥 식을 with의 첫 번째 인자로 전달해야 한다.
- 인라인 함수이기 때문에 람다 내부에서 바깥쪽 함수의 제어를 반환시키기 위해 return을 사용할 수 있다.

```kotlin
fun main() {
    with(Person("Joohyun", "M")) {
        isMale()
    }
}

class Person(var name: String, var sex: String) {
    fun isMale(): Boolean {
        return sex == "M"
    }
}
```

## let

- 외부 영역에 새로운 변수를 도입하는 것을 피하고 싶을 때 주로 사용한다.
- 내부에서 `it` 파라미터로 접근할 수 있고 `->`로 원하는 이름을 부여할 수도 있다.
- `?.let { 본문 }`고 같은 방식으로 널이 아닐 때만 실행되게 하여 널이 아님을 보장할 수 있다.

```kotlin
fun main() {
    val age = 26
    Person("Joohyun", "M").let {
        "${it.name}, ${it.sex}, $age"
    }
    Person("Joohyun", "M").let { p ->
        "${p.name}, ${p.sex}, $age"
    }
}
```

## apply

- 확장 람다를 받는 확장 함수이며 자신의 수신 객체를 반환한다.
- `run()`과 달리 반환 값을 만들어내지 않고 객체의 상태를 설정하는 경우에 사용한다.

```kotlin
fun main() {
    val age = 26
    Person().apply { 
        name = "Joohyun"
        sex = "M"
    }
}
```

## also

- `apply()`와 유사하고 인자가 하나 있는 람다를 파라미터로 받는다.

```kotlin
fun main() {
    val age = 26
    Person().also {
        it.name = "Joohyun"
        it.sex = "M"
    }
}
```

- [코틀린 완벽 가이드](https://books.google.co.kr/books/about/%EC%BD%94%ED%8B%80%EB%A6%B0_%EC%99%84%EB%B2%BD_%EA%B0%80%EC%9D%B4%EB%93%9C.html?id=851kEAAAQBAJ&source=kp_book_description&redir_esc=y)