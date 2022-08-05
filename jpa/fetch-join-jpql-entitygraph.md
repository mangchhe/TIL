# Fetch Join - JPQL, EntityGraph

- 페치 조인은 기존 SQL에 속하는 조인은 아니고 JPQL의 성능 튜닝을 위해 제공되는 조인이다.
- 페치 조인을 사용하면 관련 엔티티(테이블)까지 모두 조회하는 것을 말하며 N+1 문제 해결로 자주 사용된다.

## 구현

```java
@Entity
public class Post {

	@Id @GeneratedValue
	@Column(name = "post_id")
	private Long id;

	@OneToMany(mappedBy = "post")
	private List<Comment> comments = new ArrayList<>();
}
```

```java
@Entity
public class Comment {

	@Id @GeneratedValue
	@Column(name = "comment_id")
	private Long id;

	@ManyToOne(fetch = FetchType.LAZY)
	@JoinColumn(name = "post_id")
	private Post post;
}
```

### JPQL

```java
public interface CommentRepository extends JpaRepository<Comment, Long> {

	@Query("select c from Comment c join fetch c.post where c.id = :comment")
	Optional<Comment> findById(@Param("comment") Long id);
}
```

```sql
select
    comment0_.comment_id as comment_1_0_0_,
    post1_.post_id as post_id1_2_1_,
    comment0_.content as content2_0_0_,
    comment0_.post_id as post_id3_0_0_,
    post1_.content as content2_2_1_,
    post1_.title as title3_2_1_ 
from
    comment comment0_ 
inner join
    post post1_ 
        on comment0_.post_id=post1_.post_id 
where
    comment0_.comment_id=?
```

### EntityGraph

- attributePaths : 페치 조인할 속성을 설정한다.
- EntityGraph.EntityGraphType
    - FETCH (Default) : 속성으로 지정된 것은 EAGER, 그 외에는 LAZY로 처리된다.
    - LOAD : 속성으로 지정된 것은 EAGER, 그 외에는 엔티티에서 지정되었거나 그렇지 않다면 기본 FetchType으로 처리된다.

```java
public interface CommentRepository extends JpaRepository<Comment, Long> {

	@EntityGraph(attributePaths = {"post"}, type = EntityGraph.EntityGraphType.LOAD)
	Optional<Comment> findById(Long id);
}
```

- JPQL로 했을 때와 차이점은 left outer join이 된다는 점을 명심해야한다. 잘못하다간 원치 않은 값이 나올 수 있음

```sql
select
    comment0_.comment_id as comment_1_0_0_,
    comment0_.content as content2_0_0_,
    comment0_.post_id as post_id3_0_0_,
    post1_.post_id as post_id1_2_1_,
    post1_.content as content2_2_1_,
    post1_.title as title3_2_1_ 
from
    comment comment0_ 
left outer join
    post post1_ 
        on comment0_.post_id=post1_.post_id 
where
    comment0_.comment_id=?
```