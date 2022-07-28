# 쿼리 생성 네이밍에 사용되는 키워드

## 목차

- [사전 준비](#사전-준비)
- [Distinct](#Distinct)
- [And](#And)
- [Or](#Or)
- [Is, Equals](#Is,-Equals)
- [Between](#Between)
- [LessThan, LessThanEqual, GreaterThan, GreaterThanEqual](#LessThan,-LessThanEqual,-GreaterThan,-GreaterThanEqual)
- [Before, After](#Before,-After)
- [Null, NotNull](#Null,-NotNull)
- [Like, NotLike](#Like,-NotLike)
- [StartingWith, EndingWith](#StartingWith,-EndingWith)
- [Containing](#Containing)
- [OrderBy](#OrderBy)
- [Not, In, NotIn](#Not,-In,-NotIn)
- [True,-False](#True,-False)
- [IgnoreCase](#IgnoreCase)

## 사전 준비

```java
@Entity
@NoArgsConstructor
@AllArgsConstructor
@Getter @Setter
public class Person {

	@Id @GeneratedValue
	private Long id;

	private String username;

	private Integer age;
	private Integer weight;
	private Integer height;

	private Boolean usedYn;
}
```

```java
@SpringBootTest
@Transactional
class PersonTest {

	@Autowired
	private PersonRepository personRepository;

	@BeforeEach
	void setup() {
		Person person = new Person(null, "username", 27, 70, 180, true);
		Person person2 = new Person(null, "username", 27, 70, 180, true);
		personRepository.save(person);
		personRepository.save(person2);
    }
}
```

## Distinct

```java
List<Person> findDistinctByAge(int age);

@Test
void distinct() throws Exception {
    List<Person> persons = personRepository.findDistinctByAge(27);
    assertThat(persons).hasSize(2);
}
```

```sql
select
    distinct person0_.id as id1_0_,
    person0_.age as age2_0_,
    person0_.height as height3_0_,
    person0_.used_yn as used_yn4_0_,
    person0_.username as username5_0_,
    person0_.weight as weight6_0_ 
from
    person person0_ 
where
    person0_.age=?
```

## And

```java
List<Person> findByAgeAndWeight(int age, int weight);

@Test
void and() throws Exception {
    List<Person> persons = personRepository.findByAgeAndWeight(27, 70);
    assertThat(persons).hasSize(2);

    persons = personRepository.findByAgeAndWeight(27, 69);
    assertThat(persons).isEmpty();
}
```

```sql
select
    person0_.id as id1_0_,
    person0_.age as age2_0_,
    person0_.height as height3_0_,
    person0_.used_yn as used_yn4_0_,
    person0_.username as username5_0_,
    person0_.weight as weight6_0_ 
from
    person person0_ 
where
    person0_.age=? 
    and person0_.weight=?
```

## Or

```java
List<Person> findByAgeOrWeight(int age, int weight);

@Test
void or() throws Exception {
    List<Person> persons = personRepository.findByAgeOrWeight(27, 70);
    assertThat(persons).hasSize(2);

    persons = personRepository.findByAgeOrWeight(27, 69);
    assertThat(persons).hasSize(2);

    persons = personRepository.findByAgeOrWeight(26, 69);
    assertThat(persons).isEmpty();
}
```

```sql
select
    person0_.id as id1_0_,
    person0_.age as age2_0_,
    person0_.height as height3_0_,
    person0_.used_yn as used_yn4_0_,
    person0_.username as username5_0_,
    person0_.weight as weight6_0_ 
from
    person person0_ 
where
    person0_.age=? 
    or person0_.weight=?
```

## Is, Equals

```java
List<Person> findByUsername(String username);
List<Person> findByUsernameIs(String username);
List<Person> findByUsernameEquals(String username);

@Test
void is_equals() throws Exception {
    List<Person> persons = personRepository.findByUsername("username");
    assertThat(persons).hasSize(2);
    persons = personRepository.findByUsernameIs("username");
    assertThat(persons).hasSize(2);
    persons =personRepository.findByUsernameEquals("username");
    assertThat(persons).hasSize(2);
}
```

```sql
select
    person0_.id as id1_0_,
    person0_.age as age2_0_,
    person0_.height as height3_0_,
    person0_.used_yn as used_yn4_0_,
    person0_.username as username5_0_,
    person0_.weight as weight6_0_ 
from
    person person0_ 
where
    person0_.username=?
```

## Between

```java
List<Person> findByAgeBetween(int lowAge, int highAge);

@Test
void between() throws Exception {
    List<Person> persons = personRepository.findByAgeBetween(10, 27);
    assertThat(persons).hasSize(2);

    persons = personRepository.findByAgeBetween(30, 40);
    assertThat(persons).isEmpty();
}
```

```sql
select
    person0_.id as id1_0_,
    person0_.age as age2_0_,
    person0_.height as height3_0_,
    person0_.used_yn as used_yn4_0_,
    person0_.username as username5_0_,
    person0_.weight as weight6_0_ 
from
    person person0_ 
where
    person0_.age between ? and ?
```

## LessThan, LessThanEqual, GreaterThan, GreaterThanEqual

```java
List<Person> findByAgeLessThan(int age);
List<Person> findByAgeLessThanEqual(int age);
List<Person> findByAgeGreaterThan(int age);
List<Person> findByAgeGreaterThanEqual(int age);

@Test
void lessThan_greaterThan() throws Exception {
    List<Person> persons = personRepository.findByAgeLessThan(27);
    assertThat(persons).isEmpty();

    persons = personRepository.findByAgeLessThanEqual(27);
    assertThat(persons).hasSize(2);

    persons = personRepository.findByAgeGreaterThan(27);
    assertThat(persons).isEmpty();

    persons = personRepository.findByAgeGreaterThanEqual(27);
    assertThat(persons).hasSize(2);
}
```

```sql
select
    person0_.id as id1_0_,
    person0_.age as age2_0_,
    person0_.height as height3_0_,
    person0_.used_yn as used_yn4_0_,
    person0_.username as username5_0_,
    person0_.weight as weight6_0_ 
from
    person person0_ 
where
    person0_.age<?
    person0_.age<=?
    person0_.age>?
    person0_.age>=?

```

## Before, After

```java
List<Person> findByAgeAfter(int age);
List<Person> findByAgeBefore(int age);

@Test
void before_after() throws Exception {
    List<Person> persons = personRepository.findByAgeBefore(27);
    assertThat(persons).isEmpty();
    persons = personRepository.findByAgeBefore(28);
    assertThat(persons).hasSize(2);

    persons = personRepository.findByAgeAfter(27);
    assertThat(persons).isEmpty();
    persons = personRepository.findByAgeAfter(26);
    assertThat(persons).hasSize(2);
}
```

```sql
select
    person0_.id as id1_0_,
    person0_.age as age2_0_,
    person0_.height as height3_0_,
    person0_.used_yn as used_yn4_0_,
    person0_.username as username5_0_,
    person0_.weight as weight6_0_ 
from
    person person0_ 
where
    person0_.age<?
    person0_.age>?
```

## Null, NotNull

```java
List<Person> findByAgeNull();
List<Person> findByAgeIsNull();
List<Person> findByAgeNotNull();
List<Person> findByAgeIsNotNull();

@Test
void null_notNull() throws Exception {
    List<Person> persons = personRepository.findByAgeNull();
    assertThat(persons).isEmpty();
    persons = personRepository.findByAgeIsNull();
    assertThat(persons).isEmpty();

    persons = personRepository.findByAgeNotNull();
    assertThat(persons).hasSize(2);
    persons = personRepository.findByAgeIsNotNull();
    assertThat(persons).hasSize(2);
}
```

```sql
select
    person0_.id as id1_0_,
    person0_.age as age2_0_,
    person0_.height as height3_0_,
    person0_.used_yn as used_yn4_0_,
    person0_.username as username5_0_,
    person0_.weight as weight6_0_ 
from
    person person0_ 
where
    person0_.age is null
    person0_.age is not null
```

## Like, NotLike

```java
List<Person> findByUsernameLike(String username);
List<Person> findByUsernameNotLike(String username);

@Test
void like_notLike() throws Exception {
    List<Person> persons = personRepository.findByUsernameLike("user");
    assertThat(persons).isEmpty();

    persons = personRepository.findByUsernameNotLike("user");
    assertThat(persons).hasSize(2);

    persons = personRepository.findByUsernameLike("user%");
    assertThat(persons).hasSize(2);
}
```

```sql
select
    person0_.id as id1_0_,
    person0_.age as age2_0_,
    person0_.height as height3_0_,
    person0_.used_yn as used_yn4_0_,
    person0_.username as username5_0_,
    person0_.weight as weight6_0_ 
from
    person person0_ 
where
    person0_.username not like ? escape ?
    person0_.username like ? escape ?
```

## StartingWith, EndingWith

```java
List<Person> findByUsernameStartingWith(String username);
List<Person> findByUsernameStartsWith(String username);
List<Person> findByUsernameEndingWith(String username);
List<Person> findByUsernameEndsWith(String username);

@Test
void startingWith_endingWith() throws Exception {
    List<Person> persons = personRepository.findByUsernameStartingWith("user");
    assertThat(persons).hasSize(2);
    persons = personRepository.findByUsernameStartsWith("user");
    assertThat(persons).hasSize(2);

    persons = personRepository.findByUsernameEndingWith("name");
    assertThat(persons).hasSize(2);
    persons = personRepository.findByUsernameEndsWith("name");
    assertThat(persons).hasSize(2);
}
```

```sql
select
    person0_.id as id1_0_,
    person0_.age as age2_0_,
    person0_.height as height3_0_,
    person0_.used_yn as used_yn4_0_,
    person0_.username as username5_0_,
    person0_.weight as weight6_0_ 
from
    person person0_ 
where
    person0_.username like '%?' escape ?
    person0_.username like '?%' escape ?
```

## Containing

```java
List<Person> findByUsernameContains(String username);
List<Person> findByUsernameContaining(String username);
List<Person> findByUsernameNotContains(String username);
List<Person> findByUsernameNotContaining(String username);

@Test
void containing() throws Exception {
    List<Person> persons = personRepository.findByUsernameContains("erna");
    assertThat(persons).hasSize(2);
    persons = personRepository.findByUsernameContaining("erna");
    assertThat(persons).hasSize(2);
    
    persons = personRepository.findByUsernameNotContains("erna");
    assertThat(persons).isEmpty();
    persons = personRepository.findByUsernameNotContaining("erna");
    assertThat(persons).isEmpty();
}
```

```sql
select
    person0_.id as id1_0_,
    person0_.age as age2_0_,
    person0_.height as height3_0_,
    person0_.used_yn as used_yn4_0_,
    person0_.username as username5_0_,
    person0_.weight as weight6_0_ 
from
    person person0_ 
where
    person0_.username not like "%?%" escape ?
    person0_.username like "%?%" escape ?
```

## OrderBy

```java
List<Person> findAllByOrderByAge();
List<Person> findAllByOrderByAgeAsc();
List<Person> findAllByOrderByAgeDesc();

@Test
void orderBy() throws Exception {
    Person person = new Person(null, "username", 15, 70, 180, true);
    Person person2 = new Person(null, "username", 20, 70, 180, true);
    personRepository.save(person);
    personRepository.save(person2);

    List<Person> persons = personRepository.findAllByOrderByAge();
    assertThat(persons).isSortedAccordingTo(Comparator.comparingInt(Person::getAge));

    persons = personRepository.findAllByOrderByAgeAsc();
    assertThat(persons).isSortedAccordingTo(Comparator.comparingInt(Person::getAge));

    persons = personRepository.findAllByOrderByAgeDesc();
    assertThat(persons).isSortedAccordingTo((o1, o2) -> o2.getAge() - o1.getAge());
}
```

```sql
select
    person0_.id as id1_0_,
    person0_.age as age2_0_,
    person0_.height as height3_0_,
    person0_.used_yn as used_yn4_0_,
    person0_.username as username5_0_,
    person0_.weight as weight6_0_ 
from
    person person0_ 
order by
    person0_.age asc
    person0_.age desc
```

## Not, In, NotIn

```java
List<Person> findByUsernameNot(String username);
List<Person> findByUsernameIn(List<String> username);
List<Person> findByUsernameNotIn(List<String> username);

@Test
void not_in_notIn() throws Exception {
    List<Person> persons = personRepository.findByUsernameNot("name");
    assertThat(persons).hasSize(2);

    persons = personRepository.findByUsernameIn(List.of("username"));
    assertThat(persons).hasSize(2);

    persons = personRepository.findByUsernameNotIn(List.of("username"));
    assertThat(persons).isEmpty();
}
```

```sql
select
    person0_.id as id1_0_,
    person0_.age as age2_0_,
    person0_.height as height3_0_,
    person0_.used_yn as used_yn4_0_,
    person0_.username as username5_0_,
    person0_.weight as weight6_0_ 
from
    person person0_ 
where
    person0_.username in ( ? )
    person0_.username not in ( ? )
```

## True, False

```java
List<Person> findByUsedYnTrue();
List<Person> findByUsedYnFalse();

@Test
void true_false() throws Exception {
    List<Person> persons = personRepository.findByUsedYnTrue();
    assertThat(persons).hasSize(2);

    personRepository.findByUsedYnFalse();
    assertThat(persons).hasSize(2);
}
```

```sql
select
    person0_.id as id1_0_,
    person0_.age as age2_0_,
    person0_.height as height3_0_,
    person0_.used_yn as used_yn4_0_,
    person0_.username as username5_0_,
    person0_.weight as weight6_0_ 
from
    person person0_ 
where
    person0_.used_yn=1
    person0_.used_yn=0
```

## IgnoreCase

```java
List<Person> findByUsernameIgnoreCase(String username);

@Test
void ignoreCase() throws Exception {
    Person person = new Person(null, "UserName", 15, 70, 180, true);
    personRepository.save(person);

    List<Person> persons = personRepository.findByUsername("username");
    assertThat(persons).hasSize(2);

    persons = personRepository.findByUsernameIgnoreCase("username");
    assertThat(persons).hasSize(3);
}
```

```sql
select
    person0_.id as id1_0_,
    person0_.age as age2_0_,
    person0_.height as height3_0_,
    person0_.used_yn as used_yn4_0_,
    person0_.username as username5_0_,
    person0_.weight as weight6_0_ 
from
    person person0_ 
where
    upper(person0_.username)=upper(?)
```

## References

- [https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.query-methods](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.query-methods)