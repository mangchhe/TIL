# @MappedSuperclass, @AttributeOverrides

- 상속 관계 매핑 X, 엔티티 X
- 부모 클래스에서 제공하는 컬럼에 대한 정보들만 제공받아서 사용한다.
- 즉, 중복되는 필드나 메서드 구현을 줄여 객체 지향적으로 구현한다.

## 구현

```java
@MappedSuperclass
public class BaseEntity {

	private String creatorId;
	private LocalDateTime createdDate;
	private String modifierId;
	private LocalDateTime modifiedDate;
}
```

```java
@Entity
public class A extends BaseEntity {

    @Id @GeneratedValue
    @Column(name = "A_ID")
    private Long id;

    private String AColumn;
}
```

### DDL 결과

```mysql
create table a (
    a_id bigint not null,
    created_date datetime(6),
    creator_id varchar(255),
    modified_date datetime(6),
    modifier_id varchar(255),
    acolumn varchar(255),
    primary key (a_id)
)
```

## @AttributeOverrides

- `@AttributeOverride`를 이용해 상속 받은 매핑 정보를 재정의할 수 있다.

```java
@Entity
@AttributeOverrides({
	@AttributeOverride(name = "creatorId", column = @Column(name = "createdBy")),
	@AttributeOverride(name = "createdDate", column = @Column(name = "createdAt")),
	@AttributeOverride(name = "modifierId", column = @Column(name = "modifiedBy")),
	@AttributeOverride(name = "modifiedDate", column = @Column(name = "modifiedAt"))
})
public class A extends BaseEntity {

	@Id @GeneratedValue
	@Column(name = "A_ID")
	private Long id;

	private String AColumn;
}
```

### DDL 결과

```mysql
create table a (
    a_id bigint not null,
    created_at datetime(6),
    created_by varchar(255),
    modified_at datetime(6),
    modified_by varchar(255),
    acolumn varchar(255),
    primary key (a_id)
)
```

- [http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788960777330](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788960777330)
- [https://www.inflearn.com/course/ORM-JPA-Basic/dashboard](https://www.inflearn.com/course/ORM-JPA-Basic/dashboard)