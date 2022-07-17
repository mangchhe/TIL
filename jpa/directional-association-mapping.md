# 단/양방향 매핑 & 연관관계 주인

## 단방향

- 하나의 게시글에 여러 개의 댓글을 달 수 있다.

![diagram](https://user-images.githubusercontent.com/50051656/179397903-e86bea2d-480a-49ba-b604-f278ff2bcfbe.png)

- 외래키를 가지는 곳에 `@ManyToOne` 필드 추가
- `@JoinColumn`으로 생성될 때 컬럼 이름을 지정할 수 있고 생략이 가능하다.

```java
@Entity
public class Comment {

	@Id @GeneratedValue
	@Column(name = "comment_id")
	private Long id;

	private String content;

	@ManyToOne
	@JoinColumn(name = "post_id")
	private Post post;
}
```

```java
@Entity
public class Post {

	@Id @GeneratedValue
	@Column(name = "post_id")
	private Long id;

	private String title;
	private String content;
}
```

## 양방향

![diagram](https://user-images.githubusercontent.com/50051656/179397990-710f03fe-eed0-4a43-88d2-a2884aa6e6b2.png)

- `@OneToMany`로 외래키를 가지지 않는 곳에 추가

```java
@Entity
public class Post {

	...

	@OneToMany(mappedBy = "post")
	private List<Comment> comments = new ArrayList<>();
}
```

## 연관관계 주인

- 양방향으로 매핑할 경우 Post에 있는 comments, Comment에 있는 Post 참조가 양쪽에서 일어난다.
- 실제 변경이 일어날 때 둘 중 어디를 변경해주어야 하는지 아니면 둘 다 관리해서 변경해야 하는지 헷갈리는 상황이 발생한다.
- 이때 연관관계의 주인을 설정하여 주인의 변경만이 쿼리를 발생하게 하여 문제를 해결한다.
- 보통 `Many` 쪽이 연관관계의 주인이 되도록 설정하고 `One` 쪽에서 `mappedBy` 속성을 이용하여 주인을 설정한다.
    - 이유는 One에서 관리하게 되면 Post에서 Comment 쿼리를 발생시키는 것이기 때문에 헷갈릴 수 있다.

```java
@Entity
public class Post {

	...

	@OneToMany(mappedBy = "post")
	private List<Comment> comments = new ArrayList<>();
}
```

### 주의사항

- Post에 comments는 연관관계의 주인이 아니기 때문에(읽기전용) insert 쿼리는 발생하지만, Comment의 Post는 null 값이 들어가게 된다.

```java
Comment comment = new Comment();

Post post = new Post();
post.getComments().add(comment);
```

- 연관관계의 주인인 Comment에서 Post를 넣어줘야 제대로 된 값이 들어가게 된다.

```java
Post post = new Post();

Comment comment = new Comment();
comment.setPost(post);
```

- 하지만, 객체지향적으로 생각했을 때 양쪽에다가 모두 값 세팅을 해주는 것이 맞다. (순수 객체 상태임을 고려)
- 그리고 이렇게 해주지 않는다면 이후에 select 할 때 flush가 발생하지 않았다면 저장했던 값이 나오지 않는 현상을 겪게 된다.

```java
Post post = new Post();

Comment comment = new Comment();
comment.setPost(post);

post.getComments().add(comment);
```

- 위와 같이 사용할 때 관리해야 될 포인트가 `setPost()`, `getComments().add()`로 두 개가 되므로 편의를 위해 코드를 조금 개선한다.
- 연관관계의 주인에서 값을 설정할 때 주인이 아닌 값도 함께 설정해주는 방식을 이용한다.

```java
public class Comment {

    ...

    public void setPost(Post post) {
        this.post = post;
        post.getComments().add(this);
    }
}

public class Post {

    ...

    public void addComment(Comment comment) {
        comment.setPost(this);
        comments.add(comment);
    }
}
```

- 그러면 굳이 두 개의 메서드를 호출할 필요가 없어진다.
- 양쪽 모두 있을 필요는 없고 한쪽만 있으면 되고 나는 웬만하면 `OneToMany` 쪽에 구현하는 것을 선호한다. (이유는 읽는 그대로 이해하기 좋음)

```java
Post post = new Post();

Comment comment = new Comment();
post.addComment(comment);
```

## Reference

- [https://www.inflearn.com/course/ORM-JPA-Basic/dashboard](https://www.inflearn.com/course/ORM-JPA-Basic/dashboard)