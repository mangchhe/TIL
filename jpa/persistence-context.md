# 영속성 컨텍스트 (Persistence Context)

- 엔티티를 영구 저장하고 관리하는 환경
- `EntityManager.persist(entity);`
- 엔티티 매니저를 통해서 영속성 컨텍스트에 접근

## 엔티티 매니저

- JPA에서 Entity를 저장, 수정, 삭제와 같은 CRUD 작업을 처리한다.
- 기본적으로 엔티티 팩토리가 하나 생성이 되고 거기서 엔티티 매니저가 여러 개 생성되어 관리된다.
- 이유는 엔티티 팩토리 생성 비용은 비싸고 엔티티 매니저는 그렇지 않기 떄문이다.
- 엔티티 팩토리는 Thread safe 하지만 엔티티 매니저는 그렇지 않기 때문에 다른 스레드가 동시에 접근한다면 동시성 문제가 발생할 수 있다.

## 엔티티 생명 주기

- 비영속(new/transient) : 영속성 컨텍스트와 전혀 관계없는 객체의 상태

```java
// 현재 영속성 컨텍스트에서 관리되고 있지 않는 객체
Member member = new Member();
member.setName("홍길동");
member.setHobby("취미");
```

- 영속(managed) : 영속성 컨텍스트에서 관리되는 상태

```java
Member member = new Member();
member.setName("홍길동");
member.setHobby("취미");

EntityManager em = emf.createEntityManager(); // emf : EntityManagerFactory
em.getTransaction().begin() // 트랜잭션 시작
// 영속성 컨텍스트에서 관리
// DB에는 저장되어있지 않음, DB에 저장하려면 commit이 이루어져야함.
em.persist(member);
```

- 준영속(detached) : 영속성 컨텍스트에 관리되다가 분리된 상태

```java
em.detach(member);
```

- 삭제(removed) : 영속성 컨텍스트와 DB에서 완전히 삭제된 상태

```java
em.remove(member);
```

### 전체 소스

```java
public class JpaTest {

	public static void main(String[] args) {
        // 엔티티 팩토리 생성
		EntityManagerFactory emf = Persistence.createEntityManagerFactory("Hello World!");
        // 엔티티 매니저 생성
		EntityManager em = emf.createEntityManager();
        // 트랜잭션 생성
		EntityTransaction tx = em.getTransaction();

        // 트랜잭션 시작
        tx.begin();

        // 비영속
        Member member = new Member();
        member.setName("홍길동");
        member.setHobby("취미");

        // 영속
        em.persist(member);
        // 준영속
        // em.detach(member);
        // 삭제
        // em.remove(member);

        // DB 저장
        tx.commit();
	}
}
```

## 영속성 컨텍스트 이점

### 1차 캐시

- `@id`를 키로 가지고 엔티티를 값으로 1차 캐시라는 저장 공간에 저장된다.
- 조회할 때 DB까지 거칠 필요 없이 1차 캐시에서 바로 조회가 가능하다.
- 만약 1차 캐시에 값이 존재하지 않는다면 DB에서 조회한 후 1차 캐시에 저장하고 값을 반환한다.
- 트랜잭션이 시작되고 끝날 때까지만 공유하기 때문에 큰 성능 이점을 얻기에는 어렵다.

### 동일성 보장

- 같은 트랜잭션 내에서 같은 엔티티일 경우 같은 객체임을 보장한다.

### 쓰기 지연

- 영속화하면 1차 캐시 저장소와 쓰기 지연 SQL 저장소에도 값이 저장된다.
- 커밋(=flush)할 때 쓰기 지연 SQL 저장소에 쌓여있는 쿼리문이 함께 발생한다.
- 매번 DB 커넥션에 접근해야 하는 시간을 줄일 수 있다.

### 변경 감지 (Dirty checking)

- 영속 상태의 엔티티는 변경이 일어날 경우 따로 함수 호출 없이 DB에 update 쿼리가 발생하게 된다.
- 1차 캐시에는 엔티티가 영속화될 때의 최초 상태를 스냅샷으로 저장하고 있고 commit될 때 비교해서 변경된 값에 대한 update 쿼리를 발생시킨다.

## Reference

- [http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788960777330](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788960777330)
- [https://mangchhe.github.io/jpa/2021/02/06/PersistenceContext/](https://mangchhe.github.io/jpa/2021/02/06/PersistenceContext/)
- [https://www.inflearn.com/course/ORM-JPA-Basic/dashboard](https://www.inflearn.com/course/ORM-JPA-Basic/dashboard)