# JPA에서 JSON Column 사용하기

## One

### Column

```java
public class LanguageValue {

    private String value;
    private String locale;

    public LanguageValue() {
    }

    public LanguageValue(String value, String locale) {
        this.value = value;
        this.locale = locale;
    }

    ... getter, setter
}
```

### Entity & Repository

```java
@Entity
public class Human {

    @Id @GeneratedValue
    private long id;

    @Convert(converter = LanguageConverter.class)
    private LanguageValue name;

    public Human() {
    }

    public Human(LanguageValue name) {
        this.name = name;
    }
}

public interface HumanRepository extends JpaRepository<Human, Long> {
}
```

### Converter

```java
public class LanguageConverter implements AttributeConverter<LanguageValue, String> {

    private static final ObjectMapper objectMapper = new ObjectMapper();

    @Override
    public String convertToDatabaseColumn(LanguageValue attribute) {
        try {
            return objectMapper.writeValueAsString(attribute);
        } catch (JsonProcessingException e) {
            throw new RuntimeException(e);
        }
    }

    @Override
    public LanguageValue convertToEntityAttribute(String dbData) {
        try {
            return objectMapper.readValue(dbData, LanguageValue.class);
        } catch (JsonProcessingException e) {
            throw new RuntimeException(e);
        }
    }
}
```

### Result

<img width="417" alt="one" src="https://user-images.githubusercontent.com/50051656/186157331-10818d75-7336-47bd-93c5-b39a4fb45773.png">

## List

### Entity

```java
@Entity
public class Human {

    @Id @GeneratedValue
    private long id;

    @Convert(converter = LanguageConverter.class)
    private List<LanguageValue> name;

    public Human() {
    }

    public Human(List<LanguageValue> name) {
        this.name = name;
    }
}
```

### Converter

```java
public class LanguageConverter implements AttributeConverter<List<LanguageValue>, String> {

    private static final ObjectMapper objectMapper = new ObjectMapper();

    @Override
    public String convertToDatabaseColumn(List<LanguageValue> attribute) {
        try {
            return objectMapper.writeValueAsString(attribute);
        } catch (JsonProcessingException e) {
            throw new RuntimeException(e);
        }
    }

    @Override
    public List<LanguageValue> convertToEntityAttribute(String dbData) {
        try {
            return objectMapper.readValue(dbData, List.class);
        } catch (JsonProcessingException e) {
            throw new RuntimeException(e);
        }
    }
}
```

### Result

<img width="647" alt="list" src="https://user-images.githubusercontent.com/50051656/186157338-43366e01-1812-419a-9002-c02d2f8b6f22.png">

## Map

### Entity

```java
@Entity
public class Human {

    @Id
    @GeneratedValue
    private long id;

    @Convert(converter = LanguageConverter.class)
    private Map<String, String> name;

    public Human() {
    }

    public Human(Map<String, String> name) {
        this.name = name;
    }
}
```

### Converter

```java
public class LanguageConverter implements AttributeConverter<Map<String, String>, String> {

    private static final ObjectMapper objectMapper = new ObjectMapper();

    @Override
    public String convertToDatabaseColumn(Map<String, String> attribute) {
        try {
            return objectMapper.writeValueAsString(attribute);
        } catch (JsonProcessingException e) {
            throw new RuntimeException(e);
        }
    }

    @Override
    public Map<String, String> convertToEntityAttribute(String dbData) {
        try {
            return objectMapper.readValue(dbData, Map.class);
        } catch (JsonProcessingException e) {
            throw new RuntimeException(e);
        }
    }
}
```

### Result

![map](https://user-images.githubusercontent.com/50051656/186157376-f022da7a-fed2-4892-8a45-6eae464e2aeb.png)