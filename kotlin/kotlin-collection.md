# 컬렉션

- 컬렉션 타입은 배열, 이터러블, 시퀀스, 맵으로 네 가지로 분류할 수 있다.
- 켈력션의 타입은 제네릭 타입이다.

## 이터러블

- `Iterable<T>`
- 이터러블 타입은 원소를 순회할 수 있는 `iterator()` 메서드를 제공하고 코틀린 for loop에서는 이 메서드를 통해 모든 이터러블 객체를 활용할 수 있다.
- `hasNext()`, `next()` 메서드가 존재하고 `remove()`는 MutableIterator로 이동했다.
- 불변 컬렉션 타입에는 공변성이라는 특징이 있다.

> 공변성(covariance)
>
> T가 U의 하위 타입인 경우 Iterable<T>도 Iterable<U>의 하위 타입이다.

## 시퀀스

- 이터러블과 동일하게 `iterator()` 메서드를 제공하지만, 지연 계산을 한다는 점에서 다르다.
- 객체 초기화 시 원소를 초기화하지 않고 요청에 따라 원소를 계산한다. 즉, 상태를 가지지 않는다.

## Comparable, Comparator

- `x.compareTo(y)`, x가 y보다 더 크면 양수 작으면 음수 같으면 0

```kotlin
class Point(
        val x: Int,
        val y: Int
): Comparable<Point> {
    override fun compareTo(other: Point): Int {
        return x.compareTo(other.x)
    }
}

fun main() {
    Comparator<Point> { o1, o2 -> o1.x.compareTo(o2.x)}
    
    compareBy<Point>{ it.x }
    compareByDescending<Point> { it.x }
}
```

## 컬렉션 생성

```kotlin
fun main() {
    emptyList<Point>() // 빈 불변 list
    emptySet<Point>() // 빈 불변 set
    emptyMap<String, String>() // 빈 불변 map

    listOf<Point>() // 불변 list
    setOf<Point>() // 불변 set
    mapOf<String, String>() // 불변 map

    listOfNotNull(listOf<Point>()) // 널을 제외한 불변 list

    mutableListOf<Point>() // 가변 list
    mutableSetOf<Point>() // 가변 set
    mutableMapOf<String, String>() // 가변 map

    arrayListOf<Point>() // arrayList

    hashSetOf<Point>() // HashSet
    linkedSetOf<Point>() // LinkedHashSet
    sortedSetOf<Point>() // TreeSet

    hashMapOf<String, String>() // HashMap
    linkedMapOf<String, String>() // LinkedHashMap
    sortedMapOf<String, String>() // TreeMap
}
```

### Sequence 생성

```kotlin
fun main() {
    sequenceOf<Point>()
    listOf<Point>().asSequence()
    generateSequence(2) { if (it < 32) it * it else null }
    sequence {
        yield(0)
        yieldAll(listOf(1, 2, 3, 4, 5))
    }
}
```

### 컬렉션 간 이동

```kotlin
fun main() {
    emptyList<Point>().toSet()
    emptySet<Point>().toList()
    emptySet<Point>().toMutableSet()
    emptyMap<String, String>().toList()
    emptyMap<String, String>().toSortedMap()
}
```

## 기본 컬렉션 연산

### list

```kotlin
fun main() {
    for ((key, value) in mapOf(1 to "One", 2 to "Two", 3 to "Three")) {
        println("$key : $value")
    }

    sequenceOf(1, 2, 3).forEach { it * it }
    listOf(1, 2, 3).forEachIndexed { idx, value ->  println("$idx : $value") }

    val list = listOf(1, 2, 3)
    list.size
    list.isEmpty()
    list.contains(4)
    2 in list
    list.containsAll(listOf(1, 2, 3, 4))

    val list = arrayListOf(1, 2, 3)
    list.add(4)
    list.remove(4)
    list.addAll(listOf(4, 5))
    list.removeAll(listOf(1, 2))
    list.retainAll(listOf(3, 4))
    list.clear()
    
    list += 3
    list += listOf(3, 4)
    list -= listOf(1, 3)

    val list = arrayListOf(1, 2, 3, 3, 4, 5, 6)
    list.get(3) // 3
    list[3] // 3
    list.indexOf(3) // 2
    list.lastIndexOf(3) // 3

    list.set(0, 0)
    list[0] = 0
    list.removeAt(6) // [0, 2, 3, 3, 4, 5]
    list.add(4, 6) // [0, 2, 3, 3, 6, 4, 5]
    list.subList(1, 5) // [2, 3, 3, 6]
}
```

### map

```kotlin
fun main() {
    val numMap = mutableMapOf(1 to "One", 2 to "Two", 3 to "Three")

    for ((key, value) in numMap) {
        println("$key : $value")
    }

    numMap.isEmpty()
    numMap.size
    numMap.get(1)
    numMap[1]
    numMap.get(0) // null
    numMap.getOrDefault(4, "Four")
    numMap.getOrElse(4) {"Four"}
    numMap.containsKey(1)
    numMap.containsValue("One")
    numMap.keys
    numMap.values
    numMap.entries

    numMap.put(4, "Four")
    numMap[5] = "Five"
    numMap.remove(5)
    numMap.putAll(mapOf(6 to "Six", 7 to "Seven"))
    numMap += 8 to "Eight"
    numMap -= 1 // key 값을 기준으로 제거
}
```

## 컬렉션 원소 접근

```kotlin
fun main() {
    val list = listOf(1, 2, 3, 4, 5)
    list.first() // 1
    list.last() // 5
    emptyList<Int>().first() // NoSuchElementException
    emptyList<Int>().firstOrNull() // null
    list.first { it > 3 } // 4

    listOf(2).single() // 2
    list.single() // IllegalArgumentException
    list.singleOrNull() // null

    list.elementAt(4) // 5
    list.elementAt(6) // ArrayIndexOutOfBoundsException
    list.elementAtOrNull(6) // null
    list.elementAtOrElse(6) {"Hello World!"}
}
```

## 컬렉션 조건 검사

```kotlin

fun main() {
    val list = listOf(1, 2, 3, 4, 5)
    val map = mapOf(1 to "one", 2 to "two", 3 to "three")

    list.all { it > 4 } // false
    list.all { it > 0 } // true
    list.none {it > 5} // true
    list.none {it > 0} // false

    map.all { it.key > 2 } // false
    map.all { it.key > 0 } // true
    map.any { it.key > 2} // true
    map.any { it.key > 3} // false
}
```