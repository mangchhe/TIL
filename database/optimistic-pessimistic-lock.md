# Optimistic lock, Pessimistic lock

## 낙관적 락 (Optimistic Lock)

- 데이터 갱신 시에 충돌이 일어나지 않을 것이라고 낙관적으로 보는 것
- DB에서 걸지 않고 Version을 사용해 애플리케이션에서 거는 락
- 충돌이 일어나면 방지하는 정도 (exception)
- 커밋 시에만 충돌이 발생했는지 알 수 있다. 그렇기 때문에 트랜잭션에서 일어난 모든 내용이 커밋 시점에 충돌을 알아채고 롤백이 되기 때문에 성능 이슈가 생길 수 있다.
- 충돌이 많이 발생하게 될 경우 적합하지 않다.

![optimistic](https://user-images.githubusercontent.com/50051656/169637069-0b301bff-3363-44df-92c7-423d1c855e02.png)

### JPA에서 사용하는 방법

```java
@Entity
public class 클래스명 {
    @Id
    private Long id;

    @Version
    private Integer version;
}
```

## 비관적 락 (Pessimistic lock)

- 데이터 갱신 시에 충돌이 일어나겠다고 생각하고 미리 잠금을 거는 것
- 애플리케이션에서 거는 락이 아니라 DB에서 제공하는 락을 사용 (ex. SELECT * ... FOR UPDATE)
- 데이터 수정 시에 바로 충돌을 알 수 있다. 그렇기 때문에 낙관적 락처럼 커밋 시점에 알아채는 것에 비해 충돌에 대한 손실이 적다.
- 충돌이 많이 발생하는 경우에 적합하고 발생하지 않는 곳에 건다면 Lock으로 인해 그만큼 오버헤드만 발생하게 된다.

![pessimistic](https://user-images.githubusercontent.com/50051656/169637067-97548193-87bc-4843-8475-748bb7b9182f.png)

### JPA에서 사용하는 방법

```java
public interface UserRepository extends JpaRepository<User, Long> {

    @Lock(LockModeType.PESSIMISTIC_WRITE)
    @Query("select * from user where id = :id ")
    User findByIdForUpdate(long id);
}
```

## Reference

[10분 테코톡](https://www.youtube.com/watch?v=w6sFR3ZM64c)