# 상속 관계 매핑

- 상속 관계를 구현하여 공통으로 사용되는 컬럼(=필드)와 메서드 사용의 중복을 줄일 수 있다.
- RDB에는 상속 관계가 존재하지 않지만, 슈퍼타입 - 서브 타입 모델링 기법으로 객체 상속과 유사하게 구현이 가능하다.

## 상속 관계 종류

- 슈퍼 타입과 서브 타입을 구현하기 위한 방법으로 세 가지가 있다.

1. 단일 테이블 전략(SIGLE_TABLE) : 테이블 하나에 서브 테이블의 컬럼을 모두 넣어 구현하는 방법
1. 조인 전략(JOINED) : 슈퍼타입의 주키를 받아 각 서브 테이블에서 구현하는 방법
1. 테이블별 구현 클래스 전략(TABLE_PER_CLASS) : 서브 테이블을 전부 공통 컬럼들을 넣어 구현하는 방법

- 단일 테이블 전략은 테이블 하나에 너무 많은 컬럼이 종속되기 때문에 지양해야 한다.
- 테이블별 구현 클래스 전략은 무식한 방법으로 중복 컬럼들이 많이 생기게 되고 객체 지향의 장점을 살리지 못하는 모델링이므로 지양해야 한다.
- 조인 전략이 제일 최적의 방법이므로 해당 전략으로 모델링 하는 것이 가장 좋다.

## 실습 클래스

```java
@Entity
public class ABC {

	@Id @GeneratedValue
	@Column(name = "ABC_ID")
	private Long id;

    private String ABCColumn;
}
```

```java
@Entity
public class A extends ABC {

	@Id @GeneratedValue
	@Column(name = "A_ID")
	private Long id;

	private String AColumn;
}
```

```java
@Entity
public class B extends ABC {

	@Id @GeneratedValue
	@Column(name = "B_ID")
	private Long id;

	private String BColumn;
}
```

```java
@Entity
public class C extends ABC {

	@Id @GeneratedValue
	@Column(name = "C_ID")
	private Long id;

	private String CColumn;
}
```

## 단일 테이블 전략 구현

- 조상 클래스에 `@Inheritance(strategy = InheritanceType.SINGLE_TABLE)` 추가
- default 값이 단일 테이블 전략이기 때문에 어노테이션을 추가하지 않아도 된다.
- 하지만, 프로젝트는 혼자 하는 것이 아니기 때문에 다른 누군가 봤을 때 알 수 있도록 적어주는 것을 권한다.

```java
@Entity
@Inheritance(strategy = InheritanceType.SINGLE_TABLE)
public class ABC {

	...
}
```

### DDL 결과

```mysql
create table abc (
    dtype varchar(31) not null,
    abc_id bigint not null,
    abccolumn varchar(255),
    acolumn varchar(255),
    bcolumn varchar(255),
    ccolumn varchar(255),
    primary key (abc_id)
)
```

## 조인 전략 구현

- 조상 클래스에 `@Inheritance(strategy = InheritanceType.JOINED)` 추가

```java
@Entity
@Inheritance(strategy = InheritanceType.JOINED)
public class ABC {

	...
}
```

### DDL 결과

```mysql
create table abc (
    abc_id bigint not null,
    abccolumn varchar(255),
    primary key (abc_id)
)

create table a (
    acolumn varchar(255),
    abc_id bigint not null,
    primary key (abc_id)
)

create table b (
    bcolumn varchar(255),
    abc_id bigint not null,
    primary key (abc_id)
)

create table c (
    ccolumn varchar(255),
    abc_id bigint not null,
    primary key (abc_id)
)

alter table a
    add constraint FKigfmqp3plwly69mui1mxyoamx 
    foreign key (abc_id) 
    references abc (abc_id)

alter table b
    add constraint FKryoogr6idmmd0kaixevph3t0k 
    foreign key (abc_id)
    references abc (abc_id)

alter table c
    add constraint FKcckw6evndnkby4w10b2trnb3u 
    foreign key (abc_id) 
    references abc (abc_id)
```

## 테이블별 구현 클래스 전략

- 조상 클래스에 `@Inheritance(strategy = InheritanceType.TABLE_PER_CLASS)` 추가

```java
@Entity
@Inheritance(strategy = InheritanceType.TABLE_PER_CLASS)
public class ABC {

    ...
}
```

### DDL 결과

```mysql
create table abc (
    abc_id bigint not null,
    abccolumn varchar(255),
    primary key (abc_id)
)

create table a (
    abc_id bigint not null,
    abccolumn varchar(255),
    acolumn varchar(255),
    primary key (abc_id)
)

create table b (
    abc_id bigint not null,
    abccolumn varchar(255),
    bcolumn varchar(255),
    primary key (abc_id)
)

create table c (
    abc_id bigint not null,
    abccolumn varchar(255),
    ccolumn varchar(255),
    primary key (abc_id)
)
```