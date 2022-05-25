# Nested Loop Join, Sort Merge Join, Hash Join

Join 작업을 할 때 옵티마이저는 어떤 방식으로 조인할지 실행 계획을 세우게 된다. 이때 RDB는 내부적으로 어떻게 조인하는지 세 가지 방식이 있다.

## Nested Loop Join

![image](https://user-images.githubusercontent.com/50051656/170306554-8e9a9f70-22f4-4126-8d9a-b574fe204a17.png)

- **두 개의 테이블의 행을 각각 모두 확인하여 조인하는 방법이다.** (ex. 중첩 for문과 같은 방식)
- Driving(먼저 실행되는 테이블), Driven(나중에 실행되는 테이블)으로 나뉜다.
- Driving 테이블부터 조건에 만족하는 모든 행을 찾고 그 후 Driven 테이블에 행만큼 반복해서 조인하여 값을 찾는다.
- 행의 개수가 더 적은 테이블을 Driving 테이블로 두어야 효율이 좋다. 이유는 일일이 루트 블록을 거치는 일이 적기 때문이다.
- 데이터를 찾을 때 랜덤으로 접근하기 때문에 처리 범위가 좁은 것이 좋다.
- **행이 적은 쪽은 Driving 테이블, Driven 테이블은 인덱스를 가져야 풀 스캔을 하지 않음**

## Sort Merge Join

![image](https://user-images.githubusercontent.com/50051656/170306613-97acff45-6b96-40bd-9055-861ad2eb4599.png)

- **두 개의 테이블을 조인 컬럼으로 정렬하여 조인하는 방법이다.**
- 랜덤 접근이 부담되는 넓은 범위 데이터를 처리할 때 이용한다.
- 정렬 데이터가 많아 임시 메모리를 써야 할 경우 성능이 떨어질 수 있다.
- 조인 컬럼의 인덱스가 존재하지 않아도 사용할 수 있다.

## Hash Join

![image](https://user-images.githubusercontent.com/50051656/170306622-d2984e56-746a-4b1c-a590-5f26a84a6704.png)

- **조인 컬럼을 기준으로 해시함수를 수행하여 서로 동일한 해시값을 갖는 것들 사이에서 실제 값이 같은지 비교하면서 조인 수행하는 방법이다.**
- 조인 컬럼의 인덱스가 존재하지 않아도 사용할 수 있다.
- 해시함수를 수행하기 위해 해시 테이블을 만들어야 한다.
- 해시 조인을 할 때는 행의 개수가 더 적은 테이블을 Driving 테이블로 두는 것이 좋다.

# Reference

- [https://devlog.changhee.me/posts/Join%EA%B8%B0%EB%B2%95_%EC%A0%95%EB%A6%AC/](https://devlog.changhee.me/posts/Join%EA%B8%B0%EB%B2%95_%EC%A0%95%EB%A6%AC/)