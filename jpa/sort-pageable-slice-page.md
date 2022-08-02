# Sort, Pageable, Slice, Page

## Sort

<img width="335" alt="sort" src="https://user-images.githubusercontent.com/50051656/182392180-c8c8338e-5e24-450e-adbe-76e3fca1906c.png">

- 쿼리 생성할 때 정렬하고 싶을 때 파라미터로 사용된다.
- 기본적으로 오름차순이고 `.descending`, `.ascending`으로 설정할 수 있다.

```java
personRepository.findAll(Sort.by("username"));
personRepository.findAll(Sort.by("username").descending());
personRepository.findAll(Sort.by("username").ascending());
personRepository.findAll(Sort.by("username").and(Sort.by("age")));
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
    person0_.username asc
    person0_.username desc
    person0_.username asc
    person0_.username asc, person0_.age asc
```

## Pageable

- 쿼리 생성할 때 페이징하고 싶을 때 파라미터로 사용된다.
- JpaRepository가 상속한 클래스 중 `PagingAndSortingRepository`가 있는데 기본적으로 아래와 같이 구현도 되어 있다.

```java
public interface PagingAndSortingRepository<T, ID> extends CrudRepository<T, ID> {

	/**
	 * Returns a {@link Page} of entities meeting the paging restriction provided in the {@link Pageable} object.
	 *
	 * @param pageable the pageable to request a paged result, can be {@link Pageable#unpaged()}, must not be
	 *          {@literal null}.
	 * @return a page of entities
	 */
	Page<T> findAll(Pageable pageable);
}
```

- Pageable을 파라미터로 넘겨줄 때 인터페이스이기 때문에 객체로 만들어줄 수 없다.
- 그래서 아래 보면 Pageable을 구현한 PageRequest가 있는데 이것을 이용해 파라미터로 전달한다.

<img width="314" alt="pageRequest" src="https://user-images.githubusercontent.com/50051656/182397241-655d8294-27c8-4614-a8b7-214c6ed0017a.png">

```java
public static PageRequest of(int page, int size) {
    return of(page, size, Sort.unsorted());
}

public static PageRequest of(int page, int size, Sort sort) {
    return new PageRequest(page, size, sort);
}

personRepository.findAll(PageRequest.of(1, 5))
```

## Slice

<img width="331" alt="slice" src="https://user-images.githubusercontent.com/50051656/182392556-6405a280-bb91-4d49-8bbe-cfae65496cbb.png">

- 페이징 관련한 내용과 데이터를 가져올 수 있다.
- 만약, parameter로 Pageable이 없을 경우 `Paging query needs to have a Pageable parameter` 메시지가 뜨며 `IllegalArgumentException`을 발생시킨다.

```java
Slice<Person> findByUsername(Pageable pageable, String username);
Slice<Person> findByUsername(String username, Pageable pageable);
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
    person0_.username=? limit ?, ?
```

## Page

<img width="335" alt="page" src="https://user-images.githubusercontent.com/50051656/182392769-28774aac-797d-45b9-826c-468b44ce06ce.png">

- Slice와 차이점은 Page는 검색하려는 대상의 자료가 총 몇 개인지를 조회하는 쿼리가 추가된다.
- 상황에 맞게 Slice와 Page를 택 일하여 사용한다.
- 마찬가지로 parameter로 Pageable이 없을 경우 `Paging query needs to have a Pageable parameter` 메시지가 뜨며 `IllegalArgumentException`을 발생시킨다.

```java
List<Person> findByUsername(String username, Sort sort);
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
    person0_.username=? limit ?, ?
---
select
    count(person0_.id) as col_0_0_ 
from
    person person0_ 
where
    person0_.username=?
```

## Sort + Page

- 페이징도 하고 싶고 정렬도 하고 싶을 때는 어떻게 해야 할까?
- 만약 아래처럼 둘 다 파라미터로 전달하게 된다면 `Method must not have Pageable *and* Sort parameters`로 둘 다 가질 수 없다며 `IllegalStateException`을 발생시킨다.

```java
List<Person> findAll(Sort sort, Pageable pageable);
```

- 그러면 어떻게 해야 할까?
- Pageable 파라미터를 전달할 때 Sort도 같이 설정하여 전달하면 된다.

```java
public static PageRequest of(int page, int size, Sort sort) {
    return new PageRequest(page, size, sort);
}

personRepository.findAll(PageRequest.of(1, 5, Sort.by("username")));
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
    person0_.username asc limit ?, ?
```

## References

- [https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#repositories.special-parameters](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#repositories.special-parameters)