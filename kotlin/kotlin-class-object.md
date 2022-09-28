# 코틀린 클래스와 객체

## 기본 구조

```kotlin
class Person {
    var firstName: String = ""
    var familyName: String = ""
    var age: Int = 0
    
    fun fullName() = "$firstName $familyName"
}
```

## 생성자

### 주생성자

- 클래스 헤더의 파라미터 목록을 주생성자 선언이라고 부른다.
- 주생성자 파라미터는 프로퍼티 초기화나 init 블록밖에 사용할 수 없다.
- 이에 대한 해법은 생성자 파라미터의 값을 저장할 멤버 프로퍼티를 정의하는 것이고 이것을 간단하게 생성자 파라미터 앞에 var, val를 붙여 해결할 수 있다.

```kotlin
class Person(firstName: String, familyName: String) {
}
```

- 주생성자는 함수와 달리 본문이 하나가 아니고 프로퍼티 초기화와 초기화 블록이 등장하는 순서대로 구성된다.
- `init {}` : 초기화 블록. 클래스 초기화 시 필요한 로직을 수행할 수 있다.
- 초기화 블록은 여러 개 선언할 수 있고 순서대로 실행된다.

```kotlin
class Person(firstName: String, familyName: String) {
    var firstName: String = ""
    var familyName: String = ""
    var age: Int = 0

    init {
        println("created new instance")
    }
}
```

- 컴파일러는 모든 프로퍼티가 초기화되어 있는지 확인하고 그렇지 않으면 `Property must be initialized or be abstract` 오류가 발생한다.

### 부생성자

- 여러 생성자를 통하여 클래스 인스턴스를 서로 다른 방법으로 초기화하고 싶을 때 사용한다.
- `constructor` 키워드를 사용하여 부생성자를 정의한다.
- 부생성자는 Unit 타입 값을 반환하는 함수이므로 init 블록과 달리 내부에서 return 할 수 있다.
- 클래스에 주생성자를 선언하지 않은 경우, 모든 부생성자는 자신의 본문을 실행하기 전에 프로퍼티 초기화와 init 블록을 실행한다.
- 부생성자 파라미터 목록은 var, val 키워드를 사용할 수 없다.

```kotlin
class Person {
    var firstName: String = ""
    var familyName: String = ""

    init {
        println("created new instance")
    }
    
    constructor(firstName: String, familyName: String) {
        this.firstName = familyName;
        this.familyName = firstName;
    }
    
    constructor(fullName: String) {
        val names = fullName.split(" ")
        
        if (names.size != 2) {
            throw IllegalArgumentException("Invalid Name $fullName")
        }
        
        firstName = names[0]
        familyName = names[1]
    }
}
```

- 부생성자가 **생성자 위임 호출**을 사용해 다른 부생성자를 호출할 수 있다.
- 생성자 파라미터 목록 뒤에 콜론(:)을 넣고 this를 사용하면 생성자 위임 호출이 된다.
- 클래스에 주생성자가 있다면 모든 부생성자는 주생성장에게 위임하거나 다른 부생성자에게 위임해야 한다.

```kotlin
class Person {
    var firstName: String = ""
    var familyName: String = ""

    init {
        println("created new instance")
    }

    constructor(firstName: String, familyName: String): this("JooHyun Ha")

    constructor(fullName: String) {
        val names = fullName.split(" ")

        if (names.size != 2) {
            throw IllegalArgumentException("Invalid Name $fullName")
        }

        firstName = names[0]
        familyName = names[1]
    }

    fun fullName() = "$firstName $familyName"
}
```

## 멤버 가시성

- 함수와 프로퍼티, 주생성자, 부생성자에 대해 가시성 변경자를 지원한다.
- 종류
    - public : default
    - internal : 멤버를 멤버가 속한 클래스가 포함된 컴파일 모듈 내부에서만 볼 수 있다.
    - protected
    - private

```kotlin
// 주생성자에 가시성을 지정하려면 constructor 키워드를 명시해주어야 한다.
class Person private constructor(firstName: String, familyName: String) {
}
```

## Nested Class

- 내부 클래스 멤버 변수가 private이라도 내부 클래스에서는 값을 가져다가 쓸 수 있다.

```kotlin
class Person(private val insideClass: InsideClass){

    class InsideClass(val firstName: String, val lastName: String) {
        fun show(person: Person) = println("${person.insideClass.firstName}, ${person.insideClass.lastName}")
    }

    fun show() = println("${insideClass.firstName}, ${insideClass.lastName}")
}
```

- 하지만, 내부 클래스 멤버 변수가 private할 때 외부 클래스는 그 멤버 변수를 가져다가 사용할 수 없다.

```kotlin
class Person(private val insideClass: InsideClass){

    class InsideClass(private val firstName: String, private val lastName: String) {
        fun show(person: Person) = println("${person.insideClass.firstName}, ${person.insideClass.lastName}")
    }

    fun show() = println("${insideClass.firstName}, ${insideClass.lastName}") // Error
}
```

## 지역 클래스

- 함수 본문에서 클래스를 정의할 수 있고 지역 클래스는 자신을 둘러싼 코드 블록 안에서만 사용될 수 있다.

```kotlin
fun main() {
    class Point(x: Int, y: Int) {
        
    }
    
    val point = Point(1, 2)
}

fun main2() {
    val point = Point(1, 2) // Error
}
```

- 지역 클래스는 nested class를 지원하지 않고 inner 클래스만 지원한다.
- nested class는 특성상 자신의 외부 클래스 상태에 접근할 수 없는데 지역 클래스의 경우 자신을 둘러싼 블록 내의 모든 멤버를 포함할 수 있다.

```kotlin
fun main(args: Array<String>) {
    class Point(x: Int, y: Int) {
         inner class Point2() {
            val first = args.get(0);
        }
    }

    val point = Point(1, 2)
}
```

## 널 가능성

- 컴파일러가 정적 타입 정보만으로 오류를 잡아내지 못하기 때문에 런타임 시점에서 오류를 찾을 수 있다.
- 코틀린 타입에서는 널 값이 될 수 있는 참조 타입과 널 값이 될 수 없는 참조 타입을 확실히 구분해준다.
- 자바에서는 모든 참조 타입을 널이 될 수 있는 타입으로 간주한다.

### 널이 될 수 있는 타입

- 타입 뒤에 `?`를 붙이게 되면 널을 허용한다.

```kotlin
String?
Int? // 원시 타입도 널이 될 수 있는 타입이 존재하고 항상 박싱한 값만 표현한다.
Nothing? // 널이 될 수 있는 타입 중 가장 하위 타입
Any? // 코틀린 타입 시스템 중 가장 큰 상위 타입
```

- 널을 허용하는 타입은 원래 타입(?가 붙지 않은 타입)의 상위 타입이다.
- 즉, 널을 허용하는 타입에 허용하지 않는 타입을 넣을 수 있고 그 반대는 불가능하다.

```kotlin
fun main() {
    val text: String? = "Hello"
    val text2: String = text // Error
}
```

### 널 아님 단언 연산자

- `!!`
- 널이 될 수 있는 타입의 연산자이지만, 현재는 널이 아님을 주장할 수 있는 것을 말한다.

```kotlin
readLine()!!.toInt()
```

### 안전한 호출 연산자

- `?.`
- 널이 될 수 없는 타입에 대해서 널이면 함수를 호출하지 않고 널을 반환하고 널이 아니면 정상 동작한다.

```kotlin
readLine()?.toInt()
```

### 엘비스 연산자

- `?:`
- 널을 대신할 디폴트 값을 지정할 수 있다.

```kotlin
fun main() {
    val nullable: String? = null
    println("Hello" + (nullable ?: " World!"))
}
```

## 단순한 변수 이상의 프로퍼티

### 최상위 프로퍼티

- 전역 변수나 상수로 사용할 수 있고 public, internal, private 가시성을 가질 수 있다.

```kotlin
val suffix: String = " World!"

fun main() {
    val nullable: String? = null
    println("Hello" + (nullable ?: suffix))
}
```

### 늦은 초기화

- 클래스를 인스턴스화할 때 프로퍼티를 초기화해야 한다.
- 초기화되지 않은 상태라는 것을 알리기 위해 null 값을 디폴트로 설정하게 되면 null 가능성을 남기게 된다.
- `lateinit` 키워드를 이용해 널 가능성을 남기지 않아도 된다.
- 만약 해당 키워드가 붙은 프로퍼티를 읽으려고 할 때 초기화되어있지 않다면 `UninitializedPropertyccessException`을 발생시킨다. 
- `!!`와 비슷한 특성을 가진다.
- lateinit가 되기 위한 두 가지 조건
    - 가변 프로퍼티(var)로 지정
    - Int, Boolean 같은 원시 타입이 아니어야 한다. 내부에서 초기화되지 않은 상태를 표현하기 위해 null을 사용하는 값으로 표현하기 때문이다.

```kotlin
// before
class Content {
    var text: String? = null
    
    fun loadFile(file: File) {
        text = file.readText()
    }
}

// after
class Content {
    lateinit var text: String

    fun loadFile(file: File) {
        text = file.readText()
    }
}
```