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

- 하나 또는 두 개 이상의 이름을 역직렬화하는 동안 프로터리로 설정하는 데에 사용된다.

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

