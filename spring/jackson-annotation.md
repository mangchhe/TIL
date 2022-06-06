# Jackson Annotation 정리

- Baledung 실습 -> [링크](https://www.baeldung.com/jackson-annotations)
- 실습 저장소 : [https://github.com/TIL-Repo/jackson-examples](https://github.com/TIL-Repo/jackson-examples)

## 목차

- [Serialization Annotations](#Serialization-Annotations)
    - [@JsonAnyGetter](#JsonAnyGetter)
    - [@JsonGetter](#JsonGetter)
    - [@JsonPropertyOrder](#JsonPropertyOrder)
    - [@JsonRawValue](#JsonRawValue)
    - [@JsonValue](#JsonValue)
    - [@JsonRootName](JsonRootName)
- [Deserialization Annotations](#Deserialization-Annotations)
	- [@JsonCreator](#JsonCreator)
	- [@JacksonInject](#JacksonInject)
	- [@JsonAnySetter](#JsonAnySetter)
	- [@JsonSetter](#JsonSetter)
	- [@JsonDeserialize](#JsonDeserialize)
	- [@JsonAlias](#JsonAlias)
- [Jackson Property Inclusion Annotations](#Jackson-Property-Inclusion-Annotations)
	- [@JsonIgnoreProperties](#JsonIgnoreProperties)
	- [@JsonIgnore](#JsonIgnore)
	- [@JsonIgnoreType](#JsonIgnoreType)
	- [@JsonInclude](#JsonInclude)
	- [@JsonAutoDetect](#JsonAutoDetect)
- [Jackson Polymorphic Type Handling Annotations](#Jackson-Polymorphic-Type-Handling-Annotations)
- [Jackson General Annotations](#Jackson-General-Annotations)
	- [@JsonProperty](#JsonProperty)
	- [@JsonFormat](#JsonFormat)
	- [@JsonUnwrapped](#JsonUnwrapped)
	- [@JsonView](#JsonView)
	- [@JsonManagedReference, @JsonBackReference](#JsonManagedReference,-JsonBackReference)
	- [@JsonIdentityInfo](#JsonIdentityInfo)
	- [@JsonFilter](#JsonFilter)
	- [@JacksonAnnotationsInside](#JacksonAnnotationsInside)
	- [MixIn Method](#MixIn-Method)
	- [Disable Method](#Disable-Method)

## Serialization Annotations

### JsonAnyGetter

- Map 필드 사용을 유연하게 해준다. 아래 내용 참고

```json
before
{"name":"홍길동","properties":{"key":"value"}}
after
{"name":"홍길동","key":"value"}
```

```java
public class ExtendableBean {

	public String name;
	private Map<String, String> properties;

	@JsonAnyGetter
	public Map<String, String> getProperties() {
		return properties;
	}

	public void setName(String name) {
		this.name = name;
	}

	public void setProperties(Map<String, String> properties) {
		this.properties = properties;
	}
}

@Test
public void jsonAnyGetter() throws Exception {
    //given
    final ExtendableBean extendableBean = new ExtendableBean();
    extendableBean.setName("홍길동");
    extendableBean.setProperties(Map.of("key", "value"));
    //when
    final String result = objectMapper.writeValueAsString(extendableBean);
    //then
    assertThat(result).isEqualTo("{\"name\":\"홍길동\",\"key\":\"value\"}");
}
```

### JsonGetter

- @JsonProperty 대안
- 해당 값의 키값을 정의할 수 있다. getter 이름이랑 프로퍼티명이 다를 때 유용하다.

```json
before
{"id":1,"theName":"My bean"}
after
{"id":1,"name":"My bean"}
```

```java
public class MyBean {

	public int id;
	private String name;

	@JsonGetter("name")
	public String getTheName() {
		return name;
	}

	public MyBean(int id, String name) {
		this.id = id;
		this.name = name;
	}
}

@Test
public void jsonGetter() throws Exception {
    //given
    MyBean bean = new MyBean(1, "My bean");
    //when
    String result = objectMapper.writeValueAsString(bean);
    //then
    assertThat(result).isEqualTo("{\"id\":1,\"name\":\"My bean\"}");
}
```

### JsonPropertyOrder

- 직렬화 시 프로퍼티의 순서를 결정한다.

```json
before
{"id":1,"name":"My bean2"}
after
{"name":"My bean2","id":1}
```

```java
@JsonPropertyOrder({ "name", "id" })
public class MyBean2 {

	public int id;
	public String name;

	public MyBean2(int id, String name) {
		this.id = id;
		this.name = name;
	}
}

@Test
public void jsonPropertyOrder() throws Exception {
    //given
    MyBean2 bean2 = new MyBean2(1, "My bean2");
    //when
    final String result = objectMapper.writeValueAsString(bean2);
    //then
    assertThat(result).isEqualTo("{\"name\":\"My bean2\",\"id\":1}");
}
```

### JsonRawValue

- 문자열 값 그대로 Json 구조로 직렬화할 수 있게 해준다.

```json
before
{"name":"My bean","json":"{\"attr\":false}"}
after
{"name":"My bean","json":{"attr":false}}
```

```java
public class RawBean {

	public String name;

	@JsonRawValue
	public String json;

	public RawBean(String name, String json) {
		this.name = name;
		this.json = json;
	}
}

@Test
public void jsonRawValue() throws Exception {
    //given
    RawBean bean = new RawBean("My bean", "{\"attr\":false}");
    //when
    String result = new ObjectMapper().writeValueAsString(bean);
    //then
    assertThat(result).isEqualTo("{\"name\":\"My bean\",\"json\":{\"attr\":false}}");
}
```

### JsonValue

- 전체 인스턴스를 직렬화하는 데 사용할 단일 메서드를 나타낸다.

```json
before
"TYPE1"
after
"Type A"
```

```java
public enum TypeEnumWithValue {

	TYPE1(1, "Type A"), TYPE2(2, "Type 2");

	private Integer id;
	private String name;

	TypeEnumWithValue(Integer id, String name) {
		this.id = id;
		this.name = name;
	}

	@JsonValue
	public String getName() {
		return name;
	}
}

@Test
public void jsonValue() throws Exception {
    //when
    String result = new ObjectMapper().writeValueAsString(TypeEnumWithValue.TYPE1);
    //then
    assertThat(result).isEqualTo("\"Type A\"");
}
```

### JsonRootName

- 직렬화할 때 루트 이름을 정해 래핑한다.

```json
before
{"id":1,"name":"John"}
after
{"User":{"id":1,"name":"John"}}
```

```java
@JsonRootName(value = "user")
public class UserWithRoot {

	public int id;
	public String name;

	public UserWithRoot(int id, String name) {
		this.id = id;
		this.name = name;
	}
}

@Test
public void jsonRootName() throws Exception {
    //given
    UserWithRoot user = new UserWithRoot(1, "John");
    objectMapper.enable(SerializationFeature.WRAP_ROOT_VALUE);
    //when
    String result = objectMapper.writeValueAsString(user);
    //then
    assertThat(result).isEqualTo("{\"user\":{\"id\":1,\"name\":\"John\"}}");
}
```

### JsonSerialize

- 커스텀 한 직렬 변환기를 설정하는 것을 말한다.

```json
before
{"name":"party","eventDate":1653583800000}
after
{"name":"party","eventDate":"27-05-2022 01:50:00"}
```

```java
public class EventWithSerializer {

	public String name;

	@JsonSerialize(using = CustomDateSerializer.class)
	public Date eventDate;

	public EventWithSerializer(String name, Date eventDate) {
		this.name = name;
		this.eventDate = eventDate;
	}
}
public class CustomDateSerializer extends StdSerializer<Date> {

	private static SimpleDateFormat formatter = new SimpleDateFormat("dd-MM-yyyy hh:mm:ss");

	public CustomDateSerializer() {
		this(null);
	}

	public CustomDateSerializer(Class<Date> t) {
		super(t);
	}

	@Override
	public void serialize(Date value, JsonGenerator gen, SerializerProvider arg2) throws IOException, JsonProcessingException {
		gen.writeString(formatter.format(value));
	}
}

@Test
public void jsonSerialize() throws Exception {
    //given
    SimpleDateFormat df = new SimpleDateFormat("dd-MM-yyyy hh:mm:ss");
    EventWithSerializer event = new EventWithSerializer("party", df.parse("27-05-2022 01:50:00"));
    //when
    String result = new ObjectMapper().writeValueAsString(event);
    //then
    assertThat(result).isEqualTo("{\"name\":\"party\",\"eventDate\":\"27-05-2022 01:50:00\"}");
}
```

## Deserialization Annotations

### JsonCreator

- 생성자나 팩토리를 대상으로 역직렬화하도록 사용할 수 있다.
- 대상 엔티티와 정확히 일치하지 않는 일부 JSON을 역직렬화할 때 유용하다.
- 만약 존재하지 않는 프로퍼티를 역직렬화하려고 할 때 UnrecognizedPropertyException이 발생한다.

```java
public class BeanWithCreator {

	public int id;
	public String name;

	@JsonCreator
	public BeanWithCreator(
		@JsonProperty("id") int id,
		@JsonProperty("theName") String name) {
		this.id = id;
		this.name = name;
	}
}

@Test
public void jsonCreator() throws Exception {
	//given
	String json = "{\"id\":1,\"theName\":\"My bean\"}";
	//when
	BeanWithCreator bean = new ObjectMapper()
		.readerFor(BeanWithCreator.class)
		.readValue(json);
	//then
	assertThat(bean.name).isEqualTo("My bean");
}
```

### JacksonInject

- 프로퍼티는 JSON에 존재하지 않는 값을 인젝션을 이용해 값을 주입받을 수 있다.
- 같은 타입 두 개를 주입하려고 할 경우 InvalidDefinitionException이 발생한다.

```java
public class BeanWithInject {

	@JacksonInject
	public int id;

	@JacksonInject
	public long id2;

	public String name;
}

@Test
public void jacksonInject() throws Exception {
	//given
	String json = "{\"name\":\"My bean\"}";
	//when
	InjectableValues inject = new InjectableValues.Std()
		.addValue(int.class, 1)
		.addValue(long.class, 3);
	BeanWithInject bean = new ObjectMapper().reader(inject)
		.forType(BeanWithInject.class)
		.readValue(json);
	//then
	assertEquals("My bean", bean.name);
	assertEquals(1, bean.id);
	assertEquals(3, bean.id2);
}
```

### JsonAnySetter

- @JsonAnyGetter와 반대라고 생각하면 된다.
- Map 사용을 유연하게 제공한다. (역직렬화 시에 JSON 값을 Map에 담아준다.)

```java
public class ExtendableBean {

	public String name;
	private Map<String, String> properties = new HashMap<>();

	@JsonAnySetter
	public void add(String key, String value) {
		properties.put(key, value);
	}

	public Map<String, String> getProperties() {
		return properties;
	}
}

@Test
public void jacksonAnySetter() throws Exception {
	//given
	String json
		= "{\"name\":\"My bean\",\"attr2\":\"val2\",\"attr1\":\"val1\"}";
	//when
	ExtendableBean bean = new ObjectMapper()
		.readerFor(ExtendableBean.class)
		.readValue(json);
	//then
	assertEquals("My bean", bean.name);
	assertEquals("val1", bean.getProperties().get("attr1"));
	assertEquals("val2", bean.getProperties().get("attr2"));
}
```

### JsonSetter

- @JsonProperty 대안
- Setter에 적용할 수 있고 타겟 엔티티 클래스의 프로퍼티가 실제 JSON 데이터와 다를 때 유용하다.

```java
public class MyBean {

	public int id;
	private String name;

	@JsonSetter("name")
	public void setTheName(String name) {
		this.name = name;
	}

	public String getTheName() {
		return name;
	}
}

@Test
public void jsonSetter() throws Exception {
	//given
	String json = "{\"id\":1,\"name\":\"My bean\"}";
	//when
	MyBean bean = new ObjectMapper()
		.readerFor(MyBean.class)
		.readValue(json);
	//then
	assertEquals("My bean", bean.getTheName());
}
```

### JsonDeserialize

- 커스텀 한 역직렬 변환기를 설정하는 것을 말한다.

```java
public class EventWithSerializer {

	public String name;

	@JsonDeserialize(using = CustomDateDeserializer.class)
	public Date eventDate;
}
public class CustomDateDeserializer extends StdDeserializer<Date> {

	private static SimpleDateFormat formatter = new SimpleDateFormat("dd-MM-yyyy hh:mm:ss");

	public CustomDateDeserializer() {
		this(null);
	}

	public CustomDateDeserializer(Class<?> vc) {
		super(vc);
	}

	@Override
	public Date deserialize(JsonParser jsonparser, DeserializationContext context) throws IOException {
		String date = jsonparser.getText();
		try {
			return formatter.parse(date);
		} catch (ParseException e) {
			throw new RuntimeException(e);
		}
	}
}

@Test
public void jsonDeserialize() throws Exception {
	//given
	String json = "{\"name\":\"party\",\"eventDate\":\"28-05-2022 08:50:00\"}";
	//when
	SimpleDateFormat df = new SimpleDateFormat("dd-MM-yyyy hh:mm:ss");
	EventWithSerializer event = new ObjectMapper()
		.readerFor(EventWithSerializer.class)
		.readValue(json);
	//then
	assertEquals("28-05-2022 08:50:00", df.format(event.eventDate));
}
```

### JsonAlias

- 하나 또는 두 개 이상의 이름을 역직렬화하는 동안 프로퍼티로 설정하는 데에 사용된다.

```java
public class AliasBean {

	@JsonAlias({ "fName", "f_name" })
	private String firstName;
	private String lastName;

	public String getFirstName() {
		return firstName;
	}

	public String getLastName() {
		return lastName;
	}
}

@Test
public void jsonAlias() throws Exception {
	//given
	String json = "{\"fName\": \"John\", \"lastName\": \"Green\"}";
	//when
	AliasBean aliasBean = new ObjectMapper().readerFor(AliasBean.class).readValue(json);
	//then
	assertEquals("John", aliasBean.getFirstName());

	//given
	String json2 = "{\"f_name\": \"John\", \"lastName\": \"Green\"}";
	//when
	AliasBean aliasBean2 = new ObjectMapper().readerFor(AliasBean.class).readValue(json2);
	//then
	assertEquals("John", aliasBean2.getFirstName());
}
```

## Jackson Property Inclusion Annotations

### JsonIgnoreProperties

- 클래스 레벨 어노테이션으로 변환 시에 무시할 프로퍼티를 설정한다.

```java
@JsonIgnoreProperties({"id"})
public class BeanWithIgnoreProperties {

	public int id;
	public String name;

	public BeanWithIgnoreProperties(int id, String name) {
		this.id = id;
		this.name = name;
	}
}
@Test
public void jsonIgnoreProperties() throws Exception {
	//given
	final BeanWithIgnoreProperties bean = new BeanWithIgnoreProperties(1, "My bean");
	//when
	final String result = new ObjectMapper().writeValueAsString(bean);
	//then
	assertTrue(result.contains("My bean"));
	assertFalse(result.contains("id"));
}
```

### JsonIgnore

- 필드 레벨 어노테이션으로 변환 시에 무시할 프로퍼티에 사용한다.

```java
public class BeanWithIgnore {

	@JsonIgnore
	public int id;
	public String name;

	public BeanWithIgnore(int id, String name) {
		this.id = id;
		this.name = name;
	}
}
@Test
public void jsonIgnore() throws Exception {
	//given
	final BeanWithIgnore bean = new BeanWithIgnore(1, "My Bean");
	//when
	final String result = new ObjectMapper().writeValueAsString(bean);
	//then
	assertTrue(result.contains("My Bean"));
	assertFalse(result.contains("id"));
}
```

### JsonIgnoreType

- 어노테이션이 달린 모든 프로터리를 무시한다.

```java
public class User {

	public int id;
	public Name name;

	@JsonIgnoreType
	public static class Name {
		public String firstName;
		public String lastName;

		public Name(String firstName, String lastName) {
			this.firstName = firstName;
			this.lastName = lastName;
		}
	}

	public User(int id, Name name) {
		this.id = id;
		this.name = name;
	}
}
@Test
public void jsonIgnoreType() throws Exception {
	//given
	final User.Name name = new User.Name("John", "Doe");
	final User user = new User(1, name);
	//when
	final String result = new ObjectMapper().writeValueAsString(user);
	//then
	assertTrue(result.contains("1"));
	assertFalse(result.contains("name"));
	assertFalse(result.contains("John"));
}
```

### JsonInclude

- 비어 있거나, null 이거나 기본값인 프로퍼티를 무시 대상으로 설정할 수 있다.

```java
@JsonInclude(JsonInclude.Include.NON_NULL)
public class MyBean {

	public int id;
	public String name;

	public MyBean(int id, String name) {
		this.id = id;
		this.name = name;
	}
}
@Test
public void jsonInclude() throws Exception {
	//given
	final MyBean bean = new MyBean(1, null);
	//when
	final String result = new ObjectMapper().writeValueAsString(bean);
	//then
	assertTrue(result.contains("1"));
	assertFalse(result.contains("name"));
}
```

### JsonAutoDetect

- 프로퍼티가 무시될지 말지에 대한 범위를 설정할 수 있다.
- getter, creator, field에 설정할 수 있고 ANY, NON_PRIVATE, PUBLIC_ONLY 등 존재한다.

```java
@JsonAutoDetect(fieldVisibility = JsonAutoDetect.Visibility.ANY)
public class PrivateBean {

	private int id;
	private String name;

	public PrivateBean(int id, String name) {
		this.id = id;
		this.name = name;
	}
}
@Test
public void jsonAutoDetect() throws Exception {
	//given
	final PrivateBean bean = new PrivateBean(1, "My bean");
	//when
	final String result = new ObjectMapper().writeValueAsString(bean);
	//then
	assertTrue(result.contains("1"));
	assertTrue(result.contains("My bean"));
}
```

## Jackson Polymorphic Type Handling Annotations

- @JsonTypeInfo : 인터페이스나 추상 클래스를 이용하여 다형성을 구현할 경우, 직렬화할 때 타입에 대한 상세 정보를 담는다.
- @JsonSubTypes : 서브 타입을 나타낸다.
- @JsonTypeName : 어노테이션이 사용된 클래스의 논리적 이름을 지정한다.

```java
public class Zoo {

	public Animal animal;

	public Zoo() {
	}

	public Zoo(Animal animal) {
		this.animal = animal;
	}

	public Animal getAnimal() {
		return animal;
	}
}

@JsonTypeInfo(
	use = JsonTypeInfo.Id.NAME,
	include = JsonTypeInfo.As.PROPERTY,
	property = "type"
)
@JsonSubTypes({
	@JsonSubTypes.Type(value = Dog.class, name = "dog"),
	@JsonSubTypes.Type(value = Cat.class, name = "cat")
})
public interface Animal {
}

@JsonTypeName("cat")
public class Cat implements Animal {

	boolean likesCream;
	public int lives;

	public Cat() {}

	public Cat(boolean likesCream, int lives) {
		this.likesCream = likesCream;
		this.lives = lives;
	}
}

@JsonTypeName("dog")
public class Dog implements Animal {

	public double barkVolume;

	public Dog() {}

	public Dog(double barkVolume) {
		this.barkVolume = barkVolume;
	}
}
```

```java
@Test
public void serializationDog() throws Exception {
	//given
	final Dog dog = new Dog();
	final Zoo zoo = new Zoo(dog);
	//when
	final String result = objectMapper.writeValueAsString(zoo);
	//then
	assertTrue(result.contains("type"));
	assertTrue(result.contains("dog"));
}

@Test
public void serializationCat() throws Exception {
	//given
	final Cat cat = new Cat();
	final Zoo zoo = new Zoo(cat);
	//when
	final String result = objectMapper.writeValueAsString(zoo);
	//then
	assertTrue(result.contains("type"));
	assertTrue(result.contains("cat"));	}

@Test
public void deserializationCat() throws Exception {
	//given
	String json = "{\"animal\":{\"type\":\"cat\"}}";
	//when
	final Zoo result = objectMapper.readerFor(Zoo.class)
		.readValue(json);
	//then
	assertTrue(result.getAnimal().getClass().equals(Cat.class));
}
```

## Jackson General Annotations

### JsonProperty

- 프로퍼티의 이름을 나타내는 데 사용된다.

```java
public class MyBean {

	public int id;
	private String name;

	public MyBean() {
	}

	public MyBean(int id, String name) {
		this.id = id;
		this.name = name;
	}

	@JsonProperty("name")
	public void setTheName(String name) {
		this.name = name;
	}

	@JsonProperty("name")
	public String getTheName() {
		return name;
	}
}

@Test
public void jsonProperty() throws Exception {
	//given
	final MyBean bean = new MyBean(1, "My bean");
	//when, then
	final String result = objectMapper.writeValueAsString(bean);

	assertTrue(result.contains("name"));
	assertTrue(result.contains("1"));

	final MyBean resultBean = objectMapper.readerFor(MyBean.class)
		.readValue(result);

	assertTrue(resultBean.getTheName().equals("My bean"));
}
```

### JsonFormat

- Date/Time 값을 직렬화할 때 포맷 형태를 명시할 수 있다.

```java
public class EventWithFormat {

	public String name;

	@JsonFormat(
		shape = JsonFormat.Shape.STRING,
		pattern = "dd-MM-yyyy"
	)
	public Date evenDate;

	public EventWithFormat(String name, Date evenDate) {
		this.name = name;
		this.evenDate = evenDate;
	}
}

@Test
public void JsonFormat() throws Exception {
	//given
	SimpleDateFormat df = new SimpleDateFormat("dd-MM-yyyy hh:mm:ss");
	df.setTimeZone(TimeZone.getTimeZone("UTC"));
	Date date = df.parse("01-06-2022 11:00:00");
	EventWithFormat event = new EventWithFormat("party", date);
	//when
	final String result = objectMapper.writeValueAsString(event);
	//then
	assertTrue(result.contains("01-06-2022"));
}
```

### JsonUnwrapped

- 직렬화/역직렬화할 때 값을 평평하게 한다. 아래 예제 참고

```json
// before
{"id":1,"firstName":"John","lastName":"Doe"}
// after
{"id":1,"name":{"firstName":"John","lastName":"Doe"}}
```

```java
public class UnwrappedUser {

	public int id;

	@JsonUnwrapped
	public Name name;

	public static class Name {
		public String firstName;
		public String lastName;

		public Name(String firstName, String lastName) {
			this.firstName = firstName;
			this.lastName = lastName;
		}
	}

	public UnwrappedUser(int id, Name name) {
		this.id = id;
		this.name = name;
	}
}

@Test
public void JsonUnwrapped() throws Exception {
	//given
	final UnwrappedUser.Name name = new UnwrappedUser.Name("John", "Doe");
	final UnwrappedUser unwrappedUser = new UnwrappedUser(1, name);
	//when
	final String result = objectMapper.writeValueAsString(unwrappedUser);
	//then
	assertTrue(result.equals("{\"id\":1,\"firstName\":\"John\",\"lastName\":\"Doe\"}"));
}
```

### JsonView

- 직렬화/역직렬화할 때 해당 프로퍼티를 포함할 클래스를 지정할 수 있다.

```java
public class Views {

	public static class Public {}
	public static class Internal {}
	public static class ExtendPublic extends Public {}
}

public class Item {

	@JsonView(Views.Public.class)
	public int id;

	@JsonView({Views.Public.class, Views.Internal.class})
	public String itemName;

	@JsonView({Views.ExtendPublic.class, Views.Internal.class})
	public String ownerName;

	public Item(int id, String itemName, String ownerName) {
		this.id = id;
		this.itemName = itemName;
		this.ownerName = ownerName;
	}
}

@Test
public void JsonView() throws Exception {
	//given
	final Item item = new Item(2, "book", "John");
	//when
	final String result = objectMapper.writerWithView(Views.Public.class)
		.writeValueAsString(item);
	final String result2 = objectMapper.writerWithView(Views.Internal.class)
		.writeValueAsString(item);
	final String result3 = objectMapper.writerWithView(Views.ExtendPublic.class)
		.writeValueAsString(item);
	//then
	assertTrue(result.equals("{\"id\":2,\"itemName\":\"book\"}"));
	assertTrue(result2.equals("{\"itemName\":\"book\",\"ownerName\":\"John\"}"));
	assertTrue(result3.equals("{\"id\":2,\"itemName\":\"book\",\"ownerName\":\"John\"}"));
}
```

### JsonManagedReference, JsonBackReference

- 순환 참조를 막기 위해서 사용되며 JPA 양방향 매핑 시 그 객체를 역/직렬화할 때 사용될 수 있다.
	- JsonMappingException: infinite recursion이 발생하는 것을 방지
- 어노테이션을 부모/자식 관계로 각각 설정해준다.
- JsonManagedReference 직렬화 시 포함, JsonBackReference 직렬화 시 제외

```json
{"id":2,"itemName":"book","owner":{"id":1,"name":"John"}}
```

```java
public class ItemWithRef {

	public int id;
	public String itemName;

	@JsonManagedReference
	public UserWithRef owner;

	public ItemWithRef(int id, String itemName, UserWithRef owner) {
		this.id = id;
		this.itemName = itemName;
		this.owner = owner;
	}
}
public class UserWithRef {

	public int id;
	public String name;

	@JsonBackReference
	public List<ItemWithRef> userItems;

	public UserWithRef(int id, String name) {
		this.id = id;
		this.name = name;
		userItems = new ArrayList<>();
	}

	public void addItem(ItemWithRef userItem) {
		this.userItems.add(userItem);
	}
}

@Test
public void JsonManagedReference_JsonBackReference() throws Exception {
	// given
	final UserWithRef user = new UserWithRef(1, "John");
	final ItemWithRef item = new ItemWithRef(2, "book", user);
	user.addItem(item);
	// when
	final String result = objectMapper.writeValueAsString(item);
	// then
	assertTrue(result.contains("book"));
	assertTrue(result.contains("2"));
	assertFalse(result.contains("userItems"));
}
```

### JsonIdentityInfo

- 직렬화에 포함할 프로퍼티 값을 지정한다.
- 순환 참조를 방지할 때 사용된다.

```json
{"id":2,"itemName":"book","owner":{"id":1,"name":"John","userItems":[2]}}
```

```java
@JsonIdentityInfo(
	generator = ObjectIdGenerators.PropertyGenerator.class,
	property = "id"
)
public class ItemWithIdentity {

	public int id;
	public String itemName;
	public UserWithIdentity owner;

	public ItemWithIdentity(int id, String itemName,
		UserWithIdentity owner) {
		this.id = id;
		this.itemName = itemName;
		this.owner = owner;
	}
}
@JsonIdentityInfo(
	generator = ObjectIdGenerators.PropertyGenerator.class,
	property = "id"
)
public class UserWithIdentity {

	public int id;
	public String name;
	public List<ItemWithIdentity> userItems;

	public UserWithIdentity(int id, String name) {
		this.id = id;
		this.name = name;
		userItems = new ArrayList<>();
	}

	public void addItem(ItemWithIdentity itemWithIdentity) {
		userItems.add(itemWithIdentity);
	}
}

@Test
public void JsonIdentityInfo() throws Exception {
	// given
	final UserWithIdentity user = new UserWithIdentity(1, "John");
	final ItemWithIdentity item = new ItemWithIdentity(2, "book", user);
	user.addItem(item);
	// when
	final String result = objectMapper.writeValueAsString(item);
	// then
	assertTrue(result.contains("book"));
	assertTrue(result.contains("John"));
	assertTrue(result.contains("userItems"));
}
```

### JsonFilter

- 직렬화할 때 적용할 필터를 명시한다.

```json
{"name":"My bean"}
```

```java
@JsonFilter("myFilter")
public class BeanWithFilter {

	public int id;
	public String name;

	public BeanWithFilter(int id, String name) {
		this.id = id;
		this.name = name;
	}
}

@Test
public void JsonFilter() throws Exception {
	// given
	final BeanWithFilter bean = new BeanWithFilter(1, "My bean");

	final SimpleFilterProvider filters = new SimpleFilterProvider().addFilter("myFilter",
		SimpleBeanPropertyFilter.filterOutAllExcept("name"));
	// when
	final String result = objectMapper.writer(filters)
		.writeValueAsString(bean);
	// then
	assertTrue(result.contains("My bean"));
	assertFalse(result.contains("id"));
}
```

### JacksonAnnotationsInside

- Jackson 커스텀해서 여러 설정을 담은 커스텀 어노테이션을 만들 때 사용한다.

```json
{"name":"My bean","id":1}
```

```java
@Retention(RetentionPolicy.RUNTIME)
@JacksonAnnotationsInside
@JsonInclude(JsonInclude.Include.NON_NULL)
@JsonPropertyOrder({"name", "id", "dataCreated"})
public @interface CustomAnnotation {}

@Test
public void JacksonAnnotationsInside() throws Exception {
	// given
	final BeanWithCustomAnnotation bean = new BeanWithCustomAnnotation(1, "My bean", null);
	// when
	final String result = objectMapper.writeValueAsString(bean);
	// then
	assertTrue(result.equals("{\"name\":\"My bean\",\"id\":1}"));
	assertFalse(result.contains("dateCreated"));
}
```

### MixIn Method

- ObjectMapper를 구성하는 클래스에 MixIn을 통해서 특정 설정값들을 설정할 수 있다.

```json
{"id":1,"itemName":"book"}
```

```java
public class Item2 {

	public int id;
	public String itemName;
	public User owner;

	public Item2(int id, String itemName, User owner) {
		this.id = id;
		this.itemName = itemName;
		this.owner = owner;
	}
}
@JsonIgnoreType
public class MyMixInForIgnoreType {}

@Test
public void MinInMethod() throws Exception {
	// given
	final Item2 item = new Item2(1, "book", null);
	// when, then
	final String result = objectMapper.writeValueAsString(item);
	assertTrue(result.contains("owner"));

	final ObjectMapper objectMapper = new ObjectMapper();
	objectMapper.addMixIn(User.class, MyMixInForIgnoreType.class);

	final String result2 = objectMapper.writeValueAsString(item);
	assertFalse(result2.contains("owner"));
}
```

### Disable Method

- 클래스에 설정된 모든 jackson 설정값들을 무시한다.

```json
{"id":1,"name":null}
```

```java
@JsonInclude(JsonInclude.Include.NON_NULL)
@JsonPropertyOrder({"name", "id"})
public class MyBean2 {

	public int id;
	public String name;

	public MyBean2(int id, String name) {
		this.id = id;
		this.name = name;
	}
}
@Test
public void Disable() throws Exception {
	// given
	final ObjectMapper objectMapper = new ObjectMapper();
	final MyBean2 bean = new MyBean2(1, null);
	// when
	objectMapper.disable(MapperFeature.USE_ANNOTATIONS);
	final String result = objectMapper.writeValueAsString(bean);
	// then
	assertTrue(result.equals("{\"id\":1,\"name\":null}"));
}
```