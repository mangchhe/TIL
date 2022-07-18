# 연관관계 - N:1, 1:N, 1:1, N:N

## N:1

### N:1 단방향

![diagram](https://user-images.githubusercontent.com/50051656/179524280-eb240fd2-fff4-47c6-973c-0d4204e48a06.png)

```java
@Entity
public class Comment {

    ...

    @ManyToOne
    @JoinColumn(name = "post_id")
    private Post post;
}
```

```java
@Entity
public class Post {

	...
}
```

### N:1 양방향

![diagram](https://user-images.githubusercontent.com/50051656/179524298-1c1084fc-3092-421a-b65c-4831c425314f.png)

```java
@Entity
public class Post {

    ...

    // 추가
    @OneToMany(mappedBy = "post")
    private List<Comment> comments = new ArrayList<>();
}
```

## 1:N

- `One` 쪽에 연관관계의 주인, 즉 외래키가 `Many`가 아니라 `One` 쪽에 존재하여 이상한 구조
- JoinColumn을 사용해주어야 하고 사용하지 않으면 중간 테이블 `post_comment`이 하나 생기게 됨

### 1:N 단방향

![diagram](https://user-images.githubusercontent.com/50051656/179524314-57836162-8c88-40ba-b9e3-200332a9da35.png)

```java
@Entity
public class Comment {

    ...
}
```

```java
@Entity
public class Post {

    @OneToMany
    @JoinColumn(name = "post_id")
    private List<Comment> comments = new ArrayList<>();
}
```
### N:1 양방향

![diagram](https://user-images.githubusercontent.com/50051656/179524329-615c3d84-f344-492f-be15-bd497711316e.png)

- JPA 지원하는 Spec은 아니고 읽기전용으로 만들어 양방향처럼 사용할 수 있다.

```java
@Entity
public class Comment {

    ...

    // 추가
    @ManyToOne(insertable = false, updatable = false)
    private Post post;
}
```

## Reference

- [http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788960777330](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788960777330)
- [https://www.inflearn.com/course/ORM-JPA-Basic/dashboard](https://www.inflearn.com/course/ORM-JPA-Basic/dashboard)