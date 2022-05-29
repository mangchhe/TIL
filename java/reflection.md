# Reflectoin 정리

- Baledung 실습 -> [링크](https://www.baeldung.com/java-reflection)
- 실습 저장소 : [https://github.com/TIL-Repo/java-study/blob/master/src/test/java/reflection/ReflectionTest.java](https://github.com/TIL-Repo/java-study/blob/master/src/test/java/reflection/ReflectionTest.java)
- 실습 내용 요약 : [바로가기](#요약)

## 개념

- 구체적인 클래스 타입을 알지 못해도 그 클래스의 메소드, 타입, 멤버 변수에 접근할 수 있게 해주는 자바 API
- 런타임 시에 실행되고 있는 클래스를 가져와서 실행해야 하는 경우
- 코드 작성 시에는 어떤 타입 클래스를 사용할지 모르지만, 런타임 시점에 실행되고 있는 클래스를 가져와 실행해야 할 경우

## 실습

### 사용하게 될 클래스

<img width="413" alt="diagram" src="https://user-images.githubusercontent.com/50051656/170852943-ed0b76db-af24-4b75-b2c2-e3bb3bf2e9d6.png">

```java
public interface Eating {

	String eats();
}

public interface Locomotion {

	String getLocomotion();
}
```

```java
public abstract class Animal implements Eating {

	public static String CATEGORY = "domestic";
	private String name;

	protected abstract String getSound();

	public Animal(String name) {
		this.name = name;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}
}
```

```java
public class Goat extends Animal implements Locomotion {

	@Override
	protected String getSound() {
		return "bleat";
	}

	@Override
	public String eats() {
		return "grass";
	}

	@Override
	public String getLocomotion() {
		return "walks";
	}

	public Goat(String name) {
		super(name);
	}
}
```

```java
public class Bird extends Animal {

	private boolean walks;

	public Bird() {
		super("bird");
	}

	public Bird(String name) {
		super(name);
	}

	public Bird(String name, boolean walks) {
		super(name);
		setWalks(walks);
	}

	public void setWalks(boolean walks) {
		this.walks = walks;
	}

	public boolean walks() { return walks; }

	@Override
	public String eats() {
		return null;
	}

	@Override
	protected String getSound() {
		return null;
	}
}
```

### 자바 클래스 검사

#### 클래스 & 이름

- 클래스 얻기
    - Object.getClass()
    - Class.forName('패키지를 포함한 경로'), 못 찾을 시 ClassNotFoundException
- 이름 얻기
    - getSimpleName() : 클래스 이름
    - getName(), getCanonicalName() : 패키지 경로를 포함한 이름
    - getName(), getCanonicalName 차이점 : [알아보기](https://stackoverflow.com/questions/15202997/what-is-the-difference-between-canonical-name-simple-name-and-class-name-in-jav)

```java
@Test
public void getClassNames() throws Exception {
    //given
    Object goat = new Goat("goat");
    //when
    Class<?> clazz = goat.getClass();
    //then
    assertEquals("Goat", clazz.getSimpleName());
    assertEquals("reflection._2_inspecting_java_classes.getting_ready.Goat", clazz.getName());
    assertEquals("reflection._2_inspecting_java_classes.getting_ready.Goat", clazz.getCanonicalName());

    //when
    Class<?> clazz2 = Class.forName("reflection._2_inspecting_java_classes.getting_ready.Goat");
    //then
    assertEquals("Goat", clazz2.getSimpleName());
    assertEquals("reflection._2_inspecting_java_classes.getting_ready.Goat", clazz2.getName());
    assertEquals("reflection._2_inspecting_java_classes.getting_ready.Goat", clazz2.getCanonicalName());
}
```

#### Modifier

- getModifiers()
    - integer 타입을 반환하며 각각 식별코드가 존재한다.
- Modifier 클래스 : 식별 코드를 통해 유형을 알아낼 수 있는 검증 메소드들이 존재한다.
    - isPublic, isFinal, isPrivate, isStatic, isSynchronized 등 존재

```java
@Test
public void getClassModifiers() throws Exception {
    //given
    Class<?> goatClass = Class.forName("reflection._2_inspecting_java_classes.getting_ready.Goat");
    Class<?> animalClass = Class.forName("reflection._2_inspecting_java_classes.getting_ready.Animal");
    //when
    int goatMods = goatClass.getModifiers();
    int animalMods = animalClass.getModifiers();
    //then
    assertTrue(Modifier.isPublic(goatMods));
    assertFalse(Modifier.isInterface(goatMods));
    assertTrue(Modifier.isPublic(animalMods));
    assertTrue(Modifier.isAbstract(animalMods));
}
```

#### Package

- getPackage()

```java
@Test
public void getPackageInformation() throws Exception {
    //given
    Goat goat = new Goat("goat");
    Class<?> goatClass = goat.getClass();
    //when
    Package pkg = goatClass.getPackage();
    //then
    assertEquals("reflection._2_inspecting_java_classes.getting_ready", pkg.getName());
}
```

#### Super Class

- getSuperClass()

```java
@Test
public void getSuperClass() throws Exception {
    //given
    final Goat goat = new Goat("goat");
    String str = "any string";
    //when
    final Class<?> goatSuperClass = goat.getClass().getSuperclass();
    //then
    assertEquals("Animal", goatSuperClass.getSimpleName());
    assertEquals("Object", str.getClass().getSuperclass().getSimpleName());
}
```

#### Implemented Interface

- getInterfaces()

```java
@Test
public void getImplementedInterfaces() throws Exception {
    //given
    Class<?> goatClass = Class.forName("reflection._2_inspecting_java_classes.getting_ready.Goat");
    Class<?> animalClass = Class.forName("reflection._2_inspecting_java_classes.getting_ready.Animal");
    //when
    final Class<?>[] goatInterfaces = goatClass.getInterfaces();
    final Class<?>[] animalInterfaces = animalClass.getInterfaces();
    //then
    assertEquals(1, goatInterfaces.length);
    assertEquals(1, animalInterfaces.length);
    assertEquals("Locomotion", goatInterfaces[0].getSimpleName());
    assertEquals("Eating", animalInterfaces[0].getSimpleName());
}
```

#### 생성자, 메서드 및 필드

- getConstructors() : 생성자
- getDeclaredFields() : 필드
- getDeclaredMethods() : 메소드

```java
@Test
public void getConstructorsMethodsAndFields() throws Exception {

    final Class<?> goatClass = Class.forName("reflection._2_inspecting_java_classes.getting_ready.Goat");
    final Class<?> animalClass = Class.forName("reflection._2_inspecting_java_classes.getting_ready.Animal");

    /* Constructor */
    //when
    final Constructor<?>[] constructors = goatClass.getConstructors();
    //then
    assertEquals(1, constructors.length);
    assertEquals("reflection._2_inspecting_java_classes.getting_ready.Goat", constructors[0].getName());

    /* Field */
    //when
    final Field[] fields = animalClass.getDeclaredFields();
    final List<String> actualFields = getFieldNames(fields);
    //then
    assertEquals(2, actualFields.size());
    assertTrue(actualFields.containsAll(Arrays.asList("name", "CATEGORY")));

    /* Method */
    //when
    final Method[] methods = animalClass.getDeclaredMethods();
    final List<String> actualMethods = getMethodNames(methods);
    //then
    assertEquals(3, actualMethods.size());
    assertTrue(actualMethods.containsAll(Arrays.asList("getName", "setName", "getSound")));
}
```

### 생성자 검사

- getConstructor(Class<?>... parameterTypes) : 생성자 얻기
    - 존재하지 않는 생성자 찾을 시 NoSuchMethodException 발생

```java
@Test
public void getConstructor() throws Exception {
    //given
    final Class<?> birdClass = Class.forName("reflection._3_inspecting_constructors.Bird");
    //when
    final Constructor<?> cons1 = birdClass.getConstructor();
    final Constructor<?> cons2 = birdClass.getConstructor(String.class);
    final Constructor<?> cons3 = birdClass.getConstructor(String.class, boolean.class);
    // final Constructor<?> cons4 = birdClass.getConstructor(String.class, Boolean.class); // NoSuchMethodException
}
```

- Constructor<?>.newInstance() : 인스턴스 생성

```java
@Test
public void createNewInstance() throws Exception {
    //given
    final Class<?> birdClass = Class.forName("reflection._3_inspecting_constructors.Bird");
    final Constructor<?> cons1 = birdClass.getConstructor();
    final Constructor<?> cons2 = birdClass.getConstructor(String.class);
    final Constructor<?> cons3 = birdClass.getConstructor(String.class, boolean.class);

    //when
    final Bird bird = (Bird)cons1.newInstance();
    final Bird bird2 = (Bird)cons2.newInstance("Weaver bird");
    final Bird bird3 = (Bird)cons3.newInstance("dove", true);

    //then
    assertEquals("bird", bird.getName());
    assertEquals("Weaver bird", bird2.getName());
    assertEquals("dove", bird3.getName());

    assertFalse(bird.walks());
    assertTrue(bird3.walks());
}
```

### 필드 검사

- getField('필드명')
- getFields() : 해당 클래스의 액세스 가능한 모든 public 필드를 반환, 클래스와 모든 슈퍼클래스의 모든 공개 필드를 반환

```java
@Test
public void getPublicFields() throws Exception {
    //given
    final Class<?> birdClass = Class.forName("reflection._3_inspecting_constructors.Bird");
    //when
    final Field[] fields = birdClass.getFields();
    //then
    assertEquals(1, fields.length);
    assertEquals("CATEGORY", fields[0].getName());
}
@Test
public void getPublicFieldByName() throws Exception {
    //given
    final Class<?> birdClass = Class.forName("reflection._3_inspecting_constructors.Bird");
    //when
    final Field field = birdClass.getField("CATEGORY");
    //then
    assertEquals("CATEGORY", field.getName());
}
```

- getDeclaredField('필드명')
    - 존재하지 않는 필드를 찾을 시 NoSuchFieldException 발생
- getDeclaredFields() : 해당 클래스의 private 필드도 반환

```java
@Test
public void getDeclaredField() {
    //given
    Class<?> birdClass = Class.forName("reflection._3_inspecting_constructors.Bird");
    //when
    Field field = birdClass.getDeclaredField("walks");
    //then
    assertEquals("walks", field.getName());
}
@Test
public void getDeclaredFields() throws Exception {
    //given
    final Class<?> birdClass = Class.forName("reflection._3_inspecting_constructors.Bird");
    //when
    final Field[] fields = birdClass.getDeclaredFields();
    //then
    assertEquals(1, fields.length);
    assertEquals("walks", fields[0].getName());
}
```

- getType()

```java
@Test
public void getType() {
    //given
    Field field = Class.forName("com.baeldung.reflection.Bird").getDeclaredField("walks");
    //when
    Class<?> fieldClass = field.getType();
    //then
    assertEquals("boolean", fieldClass.getSimpleName());
}
```

- setAccessible(boolean) : 필드나 메서드의 access modifier에 의한 제어를 변경, 일반적으로 private 인스턴스 변수나 메서드는 외부에서 접근할 수 없어 예외를 발생시킨다.
- getBoolean(Object o) : 해당 타입의 값을 가져온다.
- Field.set(Object o, boolean) : 해당 필드의 값을 설정한다.

```java
@Test
public void setsAndGetValue() throws Exception {
    //given
    final Class<?> birdClass = Class.forName("reflection._3_inspecting_constructors.Bird");
    final Bird bird = (Bird)birdClass.getConstructor().newInstance();
    final Field field = birdClass.getDeclaredField("walks");
    //when, then
    field.setAccessible(true);

    assertFalse(field.getBoolean(bird));
    assertFalse(bird.walks());

    field.set(bird, true);

    assertTrue(field.getBoolean(bird));
    assertTrue(bird.walks());
}
```

- Field.get(Object o) : 필드를 소유하고 있는 객체를 인자로 넘겨야 하며, public static일 경우 null을 전달한다.

```java
@Test
public void getsAndSetsWithNull() throws Exception {
    //given
    final Class<?> birdClass = Class.forName("reflection._3_inspecting_constructors.Bird");
    final Field field = birdClass.getField("CATEGORY");
    //when
    field.setAccessible(true);
    //then
    assertEquals("domestic", field.get(null));
}
```

### 메소드 검사

- getMethods() : 모든 클래스 및 슈퍼클래스의 공용 메소드 배열을 리턴한다.

```java
@Test
public void getsAllPublicMethods() throws Exception {
    //given
    final Class<?> birdClass = Class.forName("reflection._3_inspecting_constructors.Bird");
    //when
    final Method[] methods = birdClass.getMethods();
    final List<String> methodNames = getMethodNames(methods);
    //then
    assertTrue(methodNames.containsAll(Arrays.asList(
        "equals", "notifyAll", "hashCode", "walks", "eats", "toString"
    )));
}
```

- getDeclaredMethods() : 해당 클래스의 public 메소드의 배열을 리턴한다.

```java
@Test
public void getOnlyDeclaredMethods() throws Exception {
    //given
    final Class<?> birdClass = Class.forName("reflection._3_inspecting_constructors.Bird");
    List<String> expectedMethodNames = Arrays.asList("setWalks", "walks", "getSound", "eats");
    //when
    final List<String> actualMethodNames = getMethodNames(birdClass.getDeclaredMethods());
    //then
    assertEquals(expectedMethodNames.size(), actualMethodNames.size());
    assertTrue(expectedMethodNames.containsAll(actualMethodNames));
    assertTrue(actualMethodNames.containsAll(expectedMethodNames));
}
```

- getDeclaredMethod('메소드명', ... parameters), 인자가 없다면 메소드명만 아니라면 인자 개수만큼 작성

```java
@Test
public void getsMethod() throws Exception {
    //given
    final Bird bird = new Bird();
    final Method walksMethod = bird.getClass().getDeclaredMethod("walks");
    final Method setWalksMethod = bird.getClass().getDeclaredMethod("setWalks", boolean.class);
    //when, then
    assertTrue(walksMethod.canAccess(bird));
    assertTrue(setWalksMethod.canAccess(bird));
}
```

- invoke(Object o, Object... args) : 해당 메소드 호출

```java
@Test
public void invokes() throws Exception {
    //given
    final Class<?> birdClass = Class.forName("reflection._3_inspecting_constructors.Bird");
    final Bird bird = (Bird)birdClass.getConstructor().newInstance();
    final Method setWalksMethod = birdClass.getDeclaredMethod("setWalks", boolean.class);
    final Method walksMethod = birdClass.getDeclaredMethod("walks");
    final boolean walks = (boolean)walksMethod.invoke(bird);
    //when, then
    assertFalse(walks);
    assertFalse(bird.walks());

    setWalksMethod.invoke(bird, true);

    final boolean walks2 = (boolean)walksMethod.invoke(bird);
    assertTrue(walks2);
    assertTrue(bird.walks());
}
```

## 요약

### 클래스

- Object.getClass()
- Class.forName('패키지를 포함한 경로')

### 클래스 이름

- getSimpleName()
- getName()
- getCanonicalName()

### Modifier

- getModifiers()
- Modifier.isXXX() : 검증

### Package

- getPackage()

### Super Class

- getSuperClass()

### Implemented Interface

- getInterfaces()

### Constructor

- getConstructor(Class<?>... parameterTypes)
- getConstructors()
- Constructor<?>.newInstance()

### Field

- getField('필드명')
- getFields() : 해당 클래스의 액세스 가능한 모든 public 필드를 반환, 클래스와 모든 슈퍼클래스의 모든 공개 필드를 반환
- getDeclaredFields() : 해당 클래스의 private 필드도 반환
- getType()
- setAccessible(boolean) : 필드나 메서드의 access modifier에 의한 제어를 변경, 일반적으로 private 인스턴스 변수나 메서드는 외부에서 접근할 수 없어 예외를 발생시킨다.
- getBoolean(Object o) : 해당 타입의 값을 가져온다.
- Field.set(Object o, boolean) : 해당 필드의 값을 설정한다.
- Field.get(Object o) : 필드를 소유하고 있는 객체를 인자로 넘겨야 하며, public static일 경우 null을 전달한다.

### Method

- getMethods() : 모든 클래스 및 슈퍼클래스의 공용 메소드 배열을 리턴한다.
- getDeclaredMethods() : 해당 클래스의 public 메소드의 배열을 리턴한다.
- getDeclaredMethod('메소드명', ... parameters), 인자가 없다면 메소드명만 아니라면 인자 개수만큼 작성
- invoke(Object o, Object... args) : 해당 메소드 호출