# Transaction Isolation Level

DB에서 동시에 여러 개의 트랜잭션을 처리할 때 서로가 얼마만큼 서로에서 영향을 미칠 수 있는지에 대한 정도를 말한다.

## 격리 종류

아래로 갈수록 격리 수준이 높아져 동시성이 올라가지만, 그에 따른 성능 이슈가 발생할 수 있다.

- Read Uncommitted
- Read Committed
- Repeatable Read
- Serializable

### 격리 수준에 따른 문제점

- Dirty Read : 어떤 트랜잭션에서 작업하던 데이터를 아직 커밋하지 않아도 다른 트랜잭션에서 확인할 수 있는 것을 말한다.
- Non-Repeatable Read : 같은 트랜잭션 내에서 동일한 조회 쿼리를 날렸는데 동일한 결과가 나오지 않는 것을 말한다. (일관성 x)
- Phantom Read : 동일 조회 쿼리를 날렸는데 이전에는 존재하지 않았던 레코드(row)가 추가되어 나오는 것을 말한다.

각 수준에 따라 문제점이 다르게 나타난다.

|-|Dirty Read|Non-Repeatable read|Phantom Read|
|:-:|:-:|:-:|:-:|
|Read Uncommitted|O|O|O|
|Read Committed|-|O|O|
|Repeatable Read|-|-|O|
|Serializable|-|-|-|

### Read Uncommitted

가장 낮은 고립 수준을 가지며 Dirty Read, Non-Repeatable Read, Phantom Read가 발생한다.

![image](https://user-images.githubusercontent.com/50051656/169473446-adc2f86d-aa89-4d94-b600-2e5b1cbe36c6.png)

### Read Committed

다른 트랜잭션에서 커밋이 이루어져 작업 내용을 읽을 수 있고 Non-Repeatable Read, Phantom Read가 발생한다.

![image](https://user-images.githubusercontent.com/50051656/169473606-c37b7c92-f488-4090-a707-7b289c6bfeae.png)

### Repeatable Read

같은 트랜잭션에 같은 조회를 할 경우 같은 결과를 보장하고 Phantom Read가 발생한다.
Mysql에서는 트랜잭션마다 ID를 부여하고 트랜잭션 ID보다 작은 트랜잭션 번호에서 변경한 것만 읽게 된다. 변경되기 전 레코드는 Undo 공간에 백업해두고 실제 레코드값을 변경한다.

![image](https://user-images.githubusercontent.com/50051656/169473649-7cb1fc12-3f92-4082-9abb-cb525539e667.png)

### Serializable

가장 높은 고립 수준을 가지며 발생할 수 있는 모든 문제점이 발생하지 않지만, 동시성 처리 떄문에 속도가 느리다.

![image](https://user-images.githubusercontent.com/50051656/169473680-664f61bd-3538-4f93-8a26-662452b398b5.png)

## Mysql 격리 수준 조회 및 설정

mysql default 격리 수준은 Repeatable Read이다. 그 외 postgres, oracle은 Read Committed이다.

### 조회

```sql
SELECT @@GLOBAL.transaction_isolation;
SELECT @@SESSION.transaction_isolation;
```

### 설정

```sql
SET GLOBAL TRANSACTION ISOLATION LEVEL REPEATABLE READ;
SET SESSION TRANSACTION ISOLATION LEVEL SERIALIZABLE;
```