# Index

- 데이터베이스에서 인덱스는 테이블의 검색 속도를 향상시키기 위한 자료구조이다.
- 책의 앞에 나오는 목차처럼 해당 칼럼이 어디에 있는지 저장하여, 해당 부분만 검색할 수 있게 하여 검색 속도를 향상시킨다.

### 장점

- select 절의 성능이 향상된다.

### 단점

- insert, update delete 절의 성능이 하락한다.
- 추가적인 데이터베이스 공간이 필요하다.

### 인덱스 선택 방법

- 카디널리티가 높은 칼럼 (= 칼럼의 중복 값이 별로 없는 칼럼)
- 중복 값이 별로 없을수록 Full Scan에 가까워지기 때문에 일반적으로 조회할 경우 매우 느리다. (<-> Range Scan)

## Clustered Index

- 인덱스와 데이터가 밀접하게 군집 되어 있는 형태
- 만약 데이터를 중간에 삽입하거나 삭제하게 될 경우 모두 밀어내고 당기는 과정에 리소스를 많이 소모한다. (데이터가 순서대로 정렬되어 담겨 있다.)
- 테이블당 한 개를 생성할 수 있다.
- PK 설정 시에 자동으로 그 칼럼은 클러스터드 인덱스로 설정이 된다.

### 왜 Auto_Increment로 설정할까?

```java
@Id
@NotBlank
@Email
private String email;
```

- email을 PK로 가져갔을 경우 발생할 수 있는 성능 이슈
    - 값이 새로 들어오거나 나가면 모두 밀거나 당겨야 하는 이슈가 생긴다.
    - 해당 상황의 경우 고유한 값을 가져야 한다면 UNIQUE KEY를 주는 것이 좋다.

## Non-Clusterd Index

- 인덱스와 데이터가 밀접하게 붙어있는 것이 아닌 중간에 해시테이블과 같이 주소를 담고 있고 찾아갈 수 있는 구조의 형태
- 테이블당 여러 개를 생성할 수 있다.
- insert 시에 추가 작업이 필요하고 중간에 저장 공간을 만들기 위한 추가 저장 공간이 필요하다.

## 인덱스의 구조와 원리

- B트리는 이진 트리에서 발전되어 모든 리프 노드들이 같은 레벨을 가질 수 있도록 밸런스를 맞추는 트리이다.
- 정렬된 순서를 보장하고 멀티레벨 인덱싱을 통해 빠른 검색이 가능하다.

> multi-level index
>
> 저장해야 할 정보가 많을 때 인덱스의 인덱스를 만들어 멀티 레벨로 저장하는 것을 말한다. 즉, 일종의 트리 구조이다.
> 검색 속도는 빨라지지만, 삽입과 삭제와 같은 연산은 느려진다.

<img width="1431" alt="btree" src="https://user-images.githubusercontent.com/50051656/172034124-9f5a846f-203d-4539-9dd7-78dcb3ee0e8f.png">

- innoDB Storage engine은 disk에 데이터를 저장하는 기본 단위를 페이지(block)라고 부른다.
- 이것은 디스크를 읽기, 쓰기 작업의 최소 단위로 인덱스도 페이지 단위로 관리된다.

<img width="1401" alt="btree2" src="https://user-images.githubusercontent.com/50051656/172034127-54058725-3fb7-4549-aaeb-bf67726106e1.png">

## 실습

- user 테이블 하나 생성

```sql
create table user
(
    id       bigint auto_increment
        primary key,
    birth    varchar(255) null,
    password varchar(255) null,
    username varchar(255) null
);
```

- 더미 데이터 20만 유저를 넣고 조회했을 때 0.5초가 걸리는 것을 확인할 수 있다.
- 얼마 안 걸린다고 생각할 수 있겠지만, 로직에서 많은 로직이 있는데 조회 하나만으로 0.5초가 걸린다는 것은 부담이 있을 수밖에 없다.

![select](https://user-images.githubusercontent.com/50051656/172039009-ea9e0408-3f3d-4b85-8675-d13a5faca973.png)

- 인덱스를 생성하고 제대로 생성이 되었는지 확인해보자.

```sql
CREATE UNIQUE INDEX `idx_name` on user(username);
SHOW INDEX FROM user;
```

![createindex](https://user-images.githubusercontent.com/50051656/172039004-5af24e5d-b89b-46d9-ab7a-6be6c4e060a2.png)

- 인덱스를 생성하고 똑같은 쿼리문을 날려보면 0.02초로 시간이 단축된 것을 확인할 수 있다.

![selectindex](https://user-images.githubusercontent.com/50051656/172039007-5c36f213-bd5b-4f28-bb60-63be8d8a1464.png)

## Reference

- [10분 테코톡](https://www.youtube.com/watch?v=9ZXIoh9PtwY)