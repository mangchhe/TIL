# Get vs Find

- 네이밍이 다른 만큼 이 둘의 동작에는 차이점이 존재한다.

## Get

- 실제 엔티티를 바로 가져오는 것이 아니라 **프록시 객체를 반환**하여 사용할 때 DB를 통해 값을 가져온다. (LAZY)
- 찾는 값이 존재하지 않을 경우 `EntityNotFoundException`을 발생시킨다.

```java
Person person = new Person();
personRepository.save(person);

// getOne, getById : Deprecated 됨
Person person = personRepository.getReferenceById(person.getId() + 1);
```

## Find

- 프록시 객체가 아닌 **실제 엔티티를 반환**하여 바로 DB 조회를 통해 값을 가져온다. (EAGER)
- 단일 값을 조회할 때는 `Optional`로 전달되며 값이 없으면 비어있다.
- 여러 값을 조회할 때는 해당 리스트가 비어있는 상태에 전달된다.

```java
Person person = new Person();
personRepository.save(person);
Long id = person.getId()

Optional<Person> person = personRepository.findById(id + 1);
List<Person> person= personRepository.findAllById(List.of(id + 1, id + 2));
```

## 쿼리 생성

- 다음과 같이 식별키가 아닌 다른 컬럼에 조건을 걸어서 조회하는 쿼리를 만들었을 경우는 다르다.
- 프록시 객체가 반환되지 않고 DB에서 조회한 후 실제 엔티티가 반환된다.

```java
public interface PersonRepository extends JpaRepository<Person, Long> {
	Person getByUsername(String username);
	List<Person> getAllByUsername(String username);
}
```

## References

- [https://tuhrig.de/find-vs-get/](https://tuhrig.de/find-vs-get/)