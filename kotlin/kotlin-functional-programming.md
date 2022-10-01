# 함수형 프로그래밍

- 함수형 언어는 함수를 일급 시민 값으로 취급한다. 즉, 함수를 다른 일반적인 타입의 값과 똑같이 취급한다.
- 일급 시민은 변수에 값을 대입하거나 변수에서 값을 읽을 수 있고 함수에 값을 전달하거나 함수가 값을 반환할 수 있다는 뜻이다.

## 고차 함수

- 일반적으로 배열의 원소 합계를 구하기 위해서는 아래와 같이 함수를 작성한다.

```kotlin
fun sum(numbers: IntArray): Int {
    var result = numbers.firstOrNull() ?: throw IllegalArgumentException("Empty Array")

    for (i in 1..numbers.lastIndex) result += numbers[i]

    return result
}

fun main() {
    println(sum(intArrayOf(1, 2, 3, 4, 5)))
}
```

- 위 함수를 추상화해서 다양한 집계 함수를 사용하도록 만들어보자.
- 함수 타입의 op 파라미터를 넣어서 좀 더 효율적인 코드를 작성할 수 있다.

```kotlin
fun aggregate(numbers: IntArray, op: (Int, Int) -> Int): Int {
    var result = numbers.firstOrNull() ?: throw IllegalArgumentException("Empty Array")

    for (i in 1..numbers.lastIndex) result = op(result, numbers[i])

    return result
}

fun sum(numbers: IntArray) = aggregate(numbers) { result, op -> result + op }

fun max(numbers: IntArray) = aggregate(numbers) { result, op -> if (op > result) op else result }

fun main() {
    println(sum(intArrayOf(1, 2, 3, 4, 5)))
    println(max(intArrayOf(1, 2, 3, 4, 5)))
}
```

## 함수 타입

- 자바는 하나의 추상 메서드를 가진 인터페이스를 함수 타입처럼 취급하기 때문에 람다식이나 SAM(Single Abstract Method) 인터페이스를 인스턴스화 할 수 있다.
- 코틀린에서는 함숫값은 항상 (P1, ..., Pn) -> R 형태의 함수 타입에 속하기 때문에 임의의 SAM 인터페이스로 변환하는 것이 코틀린은 불가능하다.

```kotlin
fun main() {
    val consume: Consumer<String> = {s -> println(s)} // Type mismatch
    consume.accept("Hello World!")
}
```

- interface 앞에 `fun`을 붙이게 되면 코틀린 인터페이스를 SAM 인터페이스로 취급할 수 있게 한다.

```kotlin
fun main() {
    val stringConsume = StringConsumer { s -> println(s) }
    stringConsume.accept("Hello World!")
}

fun interface StringConsumer {
    fun accept(s: String)
}
```

- 파라미터 타입을 둘러싼 괄호는 필수로 작성해야 한다.

```kotlin
val inc: (Int) -> Int = {n -> n + 1}
val dec: Int -> Int = {n -> n - 1} // Error
```

- 함수 타입도 널이 될 수 있고 함수 타입 전체를 괄호 치고 `?`를 붙이면 된다.

```kotlin
fun main() {
    aggregate {x, y ->  x + y}
    aggregate(null)
}

fun aggregate(action: ((x: Int, y: Int) -> Int)?) {
    val result = action?.invoke(1, 2)
    println(result)
}
```

## 람다

- 람다에 인자가 없으면 `->` 기호를 생략할 수 있다.

```kotlin
fun main() {
    test { 2 + 3 }
}

fun test(action: () -> Unit): Long {
    action()
    return 1
}
```

- 인자가 하나밖에 없는 람다식의 경우에 `it`를 이용해 유일한 파라미터 값을 가리킬 수 있다.

```kotlin
fun main() {
    test { (it + 1).toLong() }
}

fun test(action: (Int) -> Long): Long {
    return action(3)
}
```

- 람다의 파라미터 목록에서 사용하지 않는 값이 있다면 `_` 기호를 지정해 사용하지 않을 수 있다.

```kotlin
fun main() {
    test {_, y -> y + 1}
}

fun test(action: (Int, Int) -> Int): Int {
    return action(2, 4)
}
```

- 코틀린의 람다는 자바와 다르게 외부 변수의 값을 변경할 수 있다.

```kotlin
fun main() {
    var result = 0
    forEach(intArrayOf(1, 2, 3, 4, 5)) {
        result += it
    }
    println(result)
}

fun forEach(nums: IntArray, action: (Int) -> Unit) {
    for (n in nums) {
        action(n)
    }
}
```

## 익명 함수

- 함수 이름이 필요 없고 `fun` 키워드 다음 바로 파라미터 목록이 나온다.
- 람마와 마찬가지로 문맥에서 파라미터 추론이 가능하다.
- 익명 함수는 식이므로 인자로 함수에 넘기거나 변수에 대입하는 등 일반 값처럼 사용할 수 있다.

```kotlin
// 람다
fun sum(numbers: IntArray) = aggregate(numbers) { result, op -> result + op }

// 익명 함수
fun sum(numbers: IntArray) = aggregate(numbers, fun(result, op) = result + op)
```

## 호출 가능 참조

- 이미 함수 정의가 있고 함수 정의를 함숫값처럼 전달하기 위해서는 람다식으로 감싸서 전달할 수 있다.
- 코틀린에서는 호출 가능 참조(callable reference)를 사용하면 좀 더 간단하게 표현할 수 있다.
- 함수 이름 앞에 `::`를 붙여서 표현한다.

```kotlin
fun main() {
    sum(intArrayOf(1, 2, 3, 4, 5)) {it -> odd(it) }
    sum(intArrayOf(1, 2, 3, 4, 5)) { odd(it) }
    sum(intArrayOf(1, 2, 3, 4, 5), ::odd) // 호출 참조
}

fun odd(x: Int): Boolean = x % 2 == 1

fun sum(numbers: IntArray, action: (Int) -> Boolean): Int {
    var result = 0
    for (number in numbers) {
        if (!action(number))
            continue
        result += number
    }
    return result
}
```

- 클래스 이름 앞에 `::`를 붙이게 될 경우에 클래스의 생성자에 대한 호출 가능 참조를 얻을 수 있다.

```kotlin
fun main() {
    val createProduct = ::Product
    val product = createProduct("상품이름", 10, 1000)
}

class Product(name: String, quantity: Int, price: Int)
```

- 클래스 인스턴스의 문맥 안에서 멤버 함수를 호출고 싶을 때 바인딩 된 호출 가능 참조를 사용한다.


```kotlin
fun main() {
    val isToothpaste = Product("치약", 10, 1000)::equalName
    println(isToothpaste("치약"))
    println(isToothpaste("칫솔"))
}

class Product(val name: String, val quantity: Int, val price: Int) {
    fun equalName(name: String) = this.name.equals(name, ignoreCase = true)
}
```

- 호출 가능 참조는 오버로딩된 함수를 구분할 수 없기 때문에 함수 타입을 지정해주어야 한다.

```kotlin
fun main() {
    val condition = ::isLessThan // Overload resolution ambiguity. All these functions match.
    val condition2: (Double) -> Boolean = ::isLessThan
}

fun isLessThan(price: Int): Boolean = Int.MAX_VALUE > price
fun isLessThan(price: Double): Boolean = Double.MAX_VALUE > price
```

```kotlin
fun main() {
    val condition = ::isLessThan(5) // This syntax is reserved for future use; to call a reference, enclose it in parentheses: (foo::bar)(args)
    val condition2 = (::isLessThan)(5.0)
}

fun isLessThan(price: Double): Boolean = Double.MAX_VALUE > price
```

- 프로퍼티에 대한 호출 가능 참조를 만들 수 있고 참조 자체는 실제 함숫값이 아니라 프로퍼티 정보를 담고 있는 리플렉션 객체이다.

```kotlin
fun main() {
    val nameGetter = ((::Product)("상품이름", 5, 1000))::name.getter
    println(nameGetter())
}

class Product(val name: String, quantity: Int, price: Int)
```

## 인라인 함수와 프로퍼티

- 고차 함수와 함숫값을 사용하면 함수가 객체로 표현되기 때문에 성능 부분에서 부가 비용이 발생할 수 있다.
- 코틀린은 함숫값을 사용할 때 발생하는 런타임 비용을 줄일 수 있는 방법을 제공한다.
- 함숫값을 사용하는 고차 함수를 호출하는 부분을 해당 함수의 본문으로 대체하는 **인라인 기법**을 이용하는 것이다.

```kotlin
inline fun 함수명(파라미터) {본문}
```

- 코틀린은 다른 언어와 달리 컴파일러가 상황에 따라 무시해도 되는 최적화를 위한 힌트가 아니기에 인라인이 불가능한 경우에는 컴파일 오류로 간주한다.
- 인라인 함수가 아닌 함수에 전달할 수 없고 널이 될 수 있는 타입도 전달받을 수 없다.
- `noinline` 키워드를 붙여서 특정 람다를 인라인하지 말라고 알려주어야 한다.

```kotlin
inline fun forEach(nums: IntArray, noinline action: ((Int) -> Unit)? {
    if (action == null) 
        return
    for (n in nums) {
        action(n)
    }
}
```

- 인라인 함수에 비공개 멤버를 전달하게 되면 함수 본문이 호출 지점을 대신하기 때문에 캡슐화가 깨지게 되기 때문에 지원하지 않는다.

```kotlin
class Product(private var name: String, quantity: Int, price: Int) {
    inline fun getName(): String {
        return "$name" // Public-API inline function cannot access non-public-API 'private final var name
    }
}
```

- 프로퍼티 접근자에도 인라인을 지원한다.

```kotlin
class Product(var name: String, quantity: Int, price: Int) {
    var category: String
        inline get() = category
        set(value) { category = value }
}
```

## 비지역적 제어 흐름

- return 문 등과 같이 일반적인 제어 흐름을 깨는 명령을 사용할 때 문제가 발생한다.
- return 문은 디폴트로 자신을 둘러싸고 있는 fun, get, set으로 정의된 가장 안쪽 함수로부터 제어 흐름을 반환시킨다.
- **람다를 사용하게 될 경우 의도치 않은 제어 흐름을 유도할 수 있기 때문에 익명 함수를 사용하여 이 문제를 해결할 수 있다.**
- 또는 레이블을 사용하여 람다도 이 문제를 해결할 수 있다.

# 확장

- 기존 클래스에 기능을 확장하고 싶을 때 클래스를 변경할 수 없거나 변경하는 비용이 커서 변경하지 못할 수 있다.
- 코틀린에서는 클래스 밖에서 함수나 프로퍼티를 선언하여 사용할 수 있게 확장시킬 수 있게 지원하기 때문에 OCP를 지키며 기존 클래스를 확장할 수 있다.

## 확장 함수

```kotlin
fun main() {
    val product = Product("상품이름", 5, 10000)
    product.decreaseQuantity(1)
    println(product.quantity)
}

class Product(private var name: String, var quantity: Int, var price: Int) {}

fun Product.decreaseQuantity(quantity: Int) {
    this.quantity -= quantity
}
```

- private 멤버 변수에는 접근이 불가능하다.
- 하지만, 클래스 내부에 확장 함수가 존재할 경우에는 접근이 가능하다.
- 클래스 내에 있는 함수 이름과 확장 함수 이름이 같다면 멤버 함수를 우선적으로 선택하여 동작한다.
- 아래 코드처럼 널이 될 수 있는 타입으로 선언할 수 있고 널 값을 처리하는 책임을 확장 함수에 줄 수 있다.

```kotlin
fun Product?.decreaseQuantity(quantity: Int) {
    if (this == null) return
    this.quantity -= quantity
}
```