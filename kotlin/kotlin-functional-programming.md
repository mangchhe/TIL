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