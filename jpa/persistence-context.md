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

## Reference

- [https://mangchhe.github.io/jpa/2021/02/06/PersistenceContext/](https://mangchhe.github.io/jpa/2021/02/06/PersistenceContext/)
- [https://www.inflearn.com/course/ORM-JPA-Basic/dashboard](https://www.inflearn.com/course/ORM-JPA-Basic/dashboard)