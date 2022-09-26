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
- 주생성자 파라미터는 프로퍼티 초기화나 init 블록 밖에 사용할 수 없다.
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