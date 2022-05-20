# Exclusive Lock, Shared Lock

멀티 트랜잭션 환경에서 데이터베이스의 일관성과 무결성을 유지하려면 트랜잭션의 순차적인 진행을 보장할 수 있는 장치가 필요하다.

예를 들어 상품의 수량이 한 개가 남았는데 두 명이 동시에 구매하였을 때 순차적으로 처리해 한 명은 주문이 되고 한 명은 주문이 되어서는 안 되게 하기 위해서 사용되는 것이 lock이다.

## Exclusive Lock (베타 잠금)

- 어떤 트랜잭션이 데이터(row)를 사용하고 있을 때 트랜잭션이 완료되기 전까지 다른 트랜잭션은 읽거나 쓰지 못하게 하기 위해 사용한다.
- exclusive lock이 걸리면 해당 트랜잭션에는 다른 트랜잭션이 exclusive, shared lock을 걸 수 없다.

```sql
SELECT * FROM table ... FOR UPDATE;
```

## Shared Lock (공유 잠금)

- 어떤 트랜잭션이 데이터(row)를 읽고 있을 때 다른 트랜잭션이 해당 데이터를 쓰지 못하게 하기 위해 사용한다.
- 단, 서로 다른 트랜잭션이 데이터를 읽는 것은 가능하다.
- shared lock이 걸리면 다른 트랜잭션은 exclusive를 걸 수 없고 shared lock은 가능하다.

```sql
# 5.7
SELECT * FROM table ... LOCK IN SHARE MODE;
# 8.x
SELECT * FROM table ... FOR SHARE;
```

## Reference

- [Mysql Reference](https://dev.mysql.com/doc/refman/8.0/en/innodb-locking.html#innodb-shared-exclusive-locks)
- [https://sabarada.tistory.com/121](https://sabarada.tistory.com/121)