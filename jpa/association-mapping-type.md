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

## 1:1

- `@OneToOne`은 지연 로딩으로 설정해도 결국 항상 즉시 로딩이 일어난다.
- 이유는 해당 외래키가 Null값인지 아닌지 판단하기 위해서 상대 테이블을 뒤져야 하기 때문이다.

### 1:1 단방향

<img width="488" alt="diagram" src="https://user-images.githubusercontent.com/50051656/179661688-09081207-a951-47f9-9d9c-539796dbcb71.png">

```java
@Entity
public class Post {

	...
}
```

```java
@Entity
public class Category {

    ...

    @OneToOne
    @JoinColumn(name = "post_id")
    private Post post;
}
```

### 1:1 양방향

<img width="485" alt="diagram" src="https://user-images.githubusercontent.com/50051656/179661702-ab4de6a3-f973-4f88-a8cd-942f125c82ab.png">

- N:1 양방향 매핑과 유사하며 연관관계의 주인을 설정해준다.

```java
@Entity
public class Post {

    @OneToOne(mappedBy = "post")
    private Category category;
}
```

## N:N

- RDB에서는 다대다를 테이블 두 개로 표현할 수 없기 때문에 중간에 테이블이 하나 생성이 된다.

### `@ManyToMany`

- `@ManyToMany`를 사용해서 만들게 되면 중간 테이블에 추가 컬럼을 넣을 수 없고 매핑 키만 넣을 수 있는 문제가 발생한다.
- 그리고 쿼리를 예측하기 어렵다.

![diagram](https://user-images.githubusercontent.com/50051656/179761955-8e9f5a7e-416b-479b-a8b1-b050460a1db8.png)

```java
@Entity
public class Student {

    ...

    @MannyToMany
    @JoinTable(name = "student_lecture")
    private List<Lecture> lectures = new ArrayList<>();
}
```

```java
@Entity
public class Lecture {

    ...

    @ManyToMany(mappedBy = "lectures")
    private List<Student> students = new ArrayList<>();
}
```

### `@OneToMany`, `@ManyToOne`

- `@ManyToMany`를 사용하지 않고 중간 테이블을 직접 만들어서 사용하면 위와 같은 문제를 해결할 수 있다.

![diagram](https://user-images.githubusercontent.com/50051656/179765038-ef12d534-5cd5-4524-b197-70145bcd8016.png)

```java
@Entity
public class StudentLecture {

    @Id @GeneratedValue
    private Long id;

    @ManyToOne
    @JoinColumn(name = "student_id")
    private Student student;

    @ManyToOne
    @JoinColumn(name = "lecture_id")
    private Lecture lecture;
}
```

```java
@Entity
public class Student {

    ...

    @OneToMany(mappedBy = "student")
    private List<StudentLecture> studentLectures = new ArrayList<>();
}
```

```java
@Entity
public class Lecture {

    ...

    @OneToMany(mappedBy = "lecture")
    private List<StudentLecture> studentLectures = new ArrayList<>();
}
```

## Reference

- [http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788960777330](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788960777330)
- [https://www.inflearn.com/course/ORM-JPA-Basic/dashboard](https://www.inflearn.com/course/ORM-JPA-Basic/dashboard)