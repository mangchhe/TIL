# Cascade, OrphanRemoval

## Cascade

- 부모 Entity에 변화가 있을 때 연관관계에 있는 하위 Entity의 영속이나 삭제 등 상태를 전파하는 것을 말한다.
- 옵션 종류
    - ALL
    - PERSIST : 하위 Entity까지 영속화
    - MERGE : 하위 Entity까지 MERGE(=detach 된 엔티티를 영속화)
    - REMOVE : 하위 Entity까지 제거
    - REFRESH : 하위 Entity까지 데이터베이스로부터 값 다시 읽기
    - DETACH : 하위 Entity까지 영속 제거

### PERSIST

- 부모를 영속화할 때 연관된 자식들도 함께 영속화하려고 할 때 사용한다.
- `@OneToMany(cascade = CascadeType.PERSIST)`

```java
@Entity
public class Post {

	@Id @GeneratedValue
	@Column(name = "post_id")
	private Long id;

	...

	@OneToMany(mappedBy = "post", cascade = CascadeType.PERSIST)
	private List<Comment> comments = new ArrayList<>();

	public void addComment(Comment comment) {
		comment.setPost(this);
		comments.add(comment);
	}
}
```

```java
@Entity
public class Comment {

	@Id @GeneratedValue
	@Column(name = "comment_id")
	private Long id;

	...

	@ManyToOne(fetch = FetchType.LAZY)
	@JoinColumn(name = "post_id")
	private Post post;
}
```

#### BEFORE

- cascade 설정을 따로 해주지 않으면 각 Entity 별로 영속화를 진행해야 한다.

```java
Post post = new Post();
postRepository.save(post);

Comment comment = new Comment();
comment.setPost(post);
commentRepository.save(comment);

Comment comment2 = new Comment();
comment2.setPost(post);
commentRepository.save(comment2);
```

#### AFTER

- cascade 설정 후 부모와 자식 Entity를 한 번에 영속화할 수 있다.

```java
Post post = new Post();

Comment comment = new Comment();
Comment comment2 = new Comment();

post.addComment(comment);
post.addComment(comment2);

postRepository.save(post);
```

### REMOVE

- 부모 Entity를 삭제하면 연관된 자식 Entity까지 모두 삭제하게 된다.
- `@OneToMany(cascade = CascadeType.REMOVE)`

```java
@Entity
public class Post {

    ...
	
	@OneToMany(mappedBy = "post", cascade = CascadeType.REMOVE)
	private List<Comment> comments = new ArrayList<>();
}
```

#### BEFORE

```java
commentReposistory.delete(comment1);
commentReposistory.delete(comment2);
postRepository.delete(post);
```

#### AFTER

```java
postRepository.delete(post);
```

## OrphanRemoval

- 부모 Entity와 연관관계가 끊어진 자식 Entity를 자동으로 삭제하는 것을 말한다.
- `@OneToMany(cascade = CascadeType.REMOVE)`와 동작이 같다.
- `@OneToMany(orphanRemoval = true)`

```java
@Entity
public class Post {

    ...

	@OneToMany(mappedBy = "post", orphanRemoval = true)
	private List<Comment> comments = new ArrayList<>();
}

```

## Cascade + OrphanRemoval

- 부모 Entity에서 자식 Entity의 생명주기까지 모두 관리할 수 있다.

```java
@Entity
public class Post {

    ...

    @OneToMany(mappedBy = "post", cascade = CascadeType.PERSIST, orphanRemoval = true)
    private List<Comment> comments = new ArrayList<>();
}
```

- 부모 Entity에서 자식 Entity의 연관관계를 끊게 되면 삭제된다.

```java
List<Comment> comments = post.getComments();
comments.remove(0);
```

## References

- [http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788960777330](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788960777330)
- [https://www.inflearn.com/course/ORM-JPA-Basic/dashboard](https://www.inflearn.com/course/ORM-JPA-Basic/dashboard)