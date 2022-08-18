# Transaction Propagation

## 트랜잭션 전파 종류

|전파 옵션|설명|
|:---|:---|
|REQUIRED|전파 옵션 DEFAULT 설정이고 부모 트랜잭션이 존재할 경우 합류하고 그렇지 않으면 새로운 트랜잭션을 만들어낸다. 부모와 같은 트랜잭션으로 부모/자식 둘 중 한 곳에서 롤백이 일어나면 둘 다 롤백 된다.|
|REQUIRES_NEW|부모 트랜잭션이 존재하는 것을 떠나서 무조건 새로운 트랜잭션을 만든다.|
|MANDATORY|부모 트랜잭션으로 합류하고 존재하지 않다면 예외를 발생시킨다.|
|SUPPORTS|부모 트랜잭션이 있으면 합류하고 그렇지 않으면 트랜잭션이 없는 상태로 진행된다.|
|NOT_SUPPORTED|트랜잭션을 사용하지 않고 만약 트랜잭션이 존재한다면 보류한다.|
|NESTED|부모 트랜잭션이 존재하면 트랜잭션에 중첩하고 존재하지 않다면 새로운 트랜잭션을 생성한다. 부모 트랜잭션에 예외가 발생하면 자식 트랜잭션까지 롤백하지만, 자식 트랜잭션에 예외가 발생하면 부모 트랜잭션은 롤백 되지 않는다.|
|NEVER|트랜잭션을 사용하지 않고 만약 트랜잭션이 존재한다면 예외가 발생한다.|

## REQUIRED

### 성공

```java
@Service
@RequiredArgsConstructor
public class ParentService {

	private final ChildService childService;
	private final ParentRepository parentRepository;

	@Transactional
	public void logic() {
		Parent parent = new Parent();
		parentRepository.save(parent);
		childService.logic();
	}
}
```

```java
@Service
@RequiredArgsConstructor
public class ChildService {

	private final ChildRepository childRepository;

	@Transactional
	public void logic() {
		Child child = new Child();
		childRepository.save(child);
	}
}
```

- 부모, 자식 모두 같은 트랜잭션 내에서 insert 문이 발생하고 커밋 되는 것을 확인할 수 있다.

![required](https://user-images.githubusercontent.com/50051656/185166622-91a9aa6c-1bfb-4637-9a1c-d30efda8c6d1.png)

### 실패

```java
@Service
@RequiredArgsConstructor
public class ParentService {

	private final ChildService childService;
	private final ParentRepository parentRepository;

	@Transactional
	public void logic() {
		Parent parent = new Parent();
		parentRepository.save(parent);
		try {
			childService.logic();
		} catch (Exception e) {}
	}
}
```

```java
@Service
@RequiredArgsConstructor
public class ChildService {

	private final ChildRepository childRepository;

	@Transactional
	public void logic() {
		Child child = new Child();
		childRepository.save(child);
        throw new RuntimeException();
	}
}
```

- 자식 서비스 로직에서 예외를 터트리게 되면 부모와 같은 트랜잭션이기 때문에 둘 다 반영되지 않고 롤백 되는 것을 확인할 수 있다.

![required_exception](https://user-images.githubusercontent.com/50051656/185166643-cdfd09c6-ad63-4cdb-9a5a-762533226a67.png)

## REQUIRES_NEW

### 성공

```java
@Service
@RequiredArgsConstructor
public class ParentService {

	private final ChildService childService;
	private final ParentRepository parentRepository;

	@Transactional
	public void logic() {
		Parent parent = new Parent();
		parentRepository.save(parent);
		childService.logic();
	}
}
```

```java
@Service
@RequiredArgsConstructor
public class ChildService {

	private final ChildRepository childRepository;

	@Transactional(propagation = Propagation.REQUIRES_NEW)
	public void logic() {
		Child child = new Child();
		childRepository.save(child);
	}
}
```

- 자식 서비스에서 새로운 트랜잭션이 시작됐기 때문에 따로 insert문이 커밋되는 것을 확인할 수 있다.

![requires_new](https://user-images.githubusercontent.com/50051656/185166685-a6316cab-209b-4443-ac27-315f934ea690.png)

### 실패

```java
@Service
@RequiredArgsConstructor
public class ParentService {

	private final ChildService childService;
	private final ParentRepository parentRepository;

	@Transactional
	public void logic() {
		Parent parent = new Parent();
		parentRepository.save(parent);
		try {
			childService.logic();
		} catch (Exception e) {}
	}
}
```

```java
@Service
@RequiredArgsConstructor
public class ChildService {

	private final ChildRepository childRepository;

	@Transactional(propagation = Propagation.REQUIRES_NEW)
	public void logic() {
		Child child = new Child();
		childRepository.save(child);
        throw new RuntimeException();
	}
}
```

- 자식 서비스에서 예외를 발생시켜도 부모 서비스와 다른 트랜잭션이기 때문에 자식만 롤백 되고 부모는 정상적으로 커밋되는 것을 확인할 수 있다.

![requires_new_exception](https://user-images.githubusercontent.com/50051656/185166711-c46238cb-49ad-49a1-a9f1-aec3f10c59b0.png)

## MANDATORY

```java
public class ParentService {

	private final ChildService childService;
	private final ParentRepository parentRepository;

	public void logic() {
		Parent parent = new Parent();
		parentRepository.save(parent);
        childService.logic();
	}
}
```

```java
public class ChildService {

	private final ChildRepository childRepository;

	@Transactional(propagation = Propagation.MANDATORY)
	public void logic() {
		Child child = new Child();
		childRepository.save(child);
	}
}
```

- 부모 서비스에 트랜잭션이 존재하지 않으면 예외를 발생시키며 자식 서비스 로직이 수행되지 않는다.

```java
org.springframework.transaction.IllegalTransactionStateException: No existing transaction found for transaction marked with propagation 'mandatory'
```

## SUPPORTS

### 부모에 트랜잭션 존재

```java
public class ParentService {

	private final ChildService childService;
	private final ParentRepository parentRepository;

	@Transactional
	public void logic() {
		Parent parent = new Parent();
		parentRepository.save(parent);
		childService.logic();
	}

}
```

```java
public class ChildService {

	private final ChildRepository childRepository;

	@Transactional(propagation = Propagation.SUPPORTS)
	public void logic() {
		Child child = new Child();
		childRepository.save(child);
	}
}
```

- 기존에 트랜잭션이 있기 때문에 합류된다.

![supports](https://user-images.githubusercontent.com/50051656/185403147-f453edb4-7adb-4de2-beba-500eb06535e9.png)

### 부모에 트랜잭션 존재 X

```java
public class ParentService {

	private final ChildService childService;
	private final ParentRepository parentRepository;

	public void logic() {
		Parent parent = new Parent();
		parentRepository.save(parent);
		childService.logic();
	}

}
```

```java
public class ChildService {

	private final ChildRepository childRepository;

	@Transactional(propagation = Propagation.SUPPORTS)
	public void logic() {
		Child child = new Child();
		childRepository.save(child);
	}
}

- 기존에 트랜잭션이 없기 때문에 자식 서비스는 트랜잭션 없이 진행된다.

![supports_no_transaction](https://user-images.githubusercontent.com/50051656/185403153-0f802420-e18f-4960-ad51-140b4379a1f8.png)

## NEVER

```java
public class ParentService {

	private final ChildService childService;
	private final ParentRepository parentRepository;

	@Transactional
	public void logic() {
		Parent parent = new Parent();
		parentRepository.save(parent);
		childService.logic();
	}

}
```

```java
public class ChildService {

	private final ChildRepository childRepository;

	@Transactional(propagation = Propagation.NEVER)
	public void logic() {
		Child child = new Child();
		childRepository.save(child);
	}
}
```

- 트랜잭션이 존재하면 안 되는데 부모 트랜잭션이 존재하기 때문에 예외가 발생한다.

```java
org.springframework.transaction.IllegalTransactionStateException: Existing transaction found for transaction marked with propagation 'never'
```

![never](https://user-images.githubusercontent.com/50051656/185407153-232cbb61-5985-42b4-874b-47d2e0afe0b1.png)

## References

- [https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/transaction/annotation/Propagation.html](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/transaction/annotation/Propagation.html)