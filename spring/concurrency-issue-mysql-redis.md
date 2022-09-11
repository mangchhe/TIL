# 동시성 이슈 해결 방법 - Mysql, Redis

- Application
    - Synchronized
- Mysql
    - Pessimistic Lock
    - Optimistic Lock
    - Named Lock
- Redis
    - Lettuce
    - Redisson

## 준비

- `@RequiredArgsConstructor`, `@Getter` 등 일부 애노테이션 생략

```java
@Entity
public class Product {

	@Id
	private Long id;

	private Integer quantity;

	public void decreaseQuantity(int quantity) {
		this.quantity -= quantity;
	}
}

public interface ProductRepository extends JpaRepository<Product, Long> {
}

@Service
public class ProductService {

	private final ProductRepository productRepository;

	@Transactional
	public void order(long productId, int quantity) {
		Product product = productRepository.findById(productId).orElseThrow();
		product.decreaseQuantity(quantity);
	}
}
```

```java
@SpringBootTest
class ProductTest {

        ...

	@BeforeEach
	void before() {
		productRepository.saveAndFlush(new Product(1L, 100));
	}

	@Test
	void concurrency_test() throws Exception {
		int threadCount = 100;
		ExecutorService executorService = Executors.newFixedThreadPool(10);
		CountDownLatch countDownLatch = new CountDownLatch(threadCount);

		for (int i = 0; i < threadCount; i++) {
			executorService.submit(() -> {
				try {
					productService.order(1L, 1);
				} catch (Exception e) {
					System.out.println(e.getMessage());
				}
				finally {
					countDownLatch.countDown();
				}
			});
		}

		countDownLatch.await();

		Assertions.assertThat(productRepository.findById(1L).get().getQuantity()).isEqualTo(0);
	}
}
```

## Synchronized

- `@Transactional`을 제거하고 `saveAndFlush`를 추가해서 바로 DB에 반영되도록 수정한다.
- `@Transactional`을 쓰게되면 메서드가 끝나고 DB가 반영하기까지 시간이 있기 때문에 제대로 sync가 안 된다.
- 애플리케이션 단위 즉, 프로세스 단위로 동시성을 보장하기 때문에 분산 서비스에서 여러 개의 같은 서비스가 여러 개가 켜져 있을 때 동시성을 보장할 수 없다.

```java
// @Transactional 제거
public synchronized void order(long productId, int quantity) {
        Product product = productRepository.findById(productId).orElseThrow();
        product.decreaseQuantity(quantity);
        productRepository.saveAndFlush(product); // 추가
}
```

## Pessimistic Lock

- exclusive lock을 걸어 해당 로우를 읽는 동안은 다른 트랜잭션이 접근하지 못하도록 한다.
- PESSIMISTIC_WRITE : for update
- PESSIMISTIC_READ : for share
- [exclusive vs shared](https://github.com/mangchhe/TIL/blob/main/database/exclusive-shared-lock.md)

```java
public interface ProductRepository extends JpaRepository<Product, Long> {

        @Lock(value = LockModeType.PESSIMISTIC_WRITE)
        Product getProductById(Long id);
}

@Transactional
public void order(long productId, int quantity) {
        Product product = productRepository.getProductById(productId);
        product.decreaseQuantity(quantity);
}
```

### Query

```java
select product0_.id as id1_0_, product0_.quantity as quantity2_0_ from product product0_ where product0_.id=? for update
```

## Optimistic

```java
public class Product {
        // 추가
        @Version
        private Long version;
}

public interface ProductRepository extends JpaRepository<Product, Long> {

        @Lock(value = LockModeType.OPTIMISTIC)
        Optional<Product> findProductById(Long id);
}

@Service
public class OptimisticLockService {

	private final ProductService productService;

	public void order(long productId, int quantity) throws InterruptedException {
		while (true) {
			try {
				productService.order(1L, 1);
				break;
			} catch (Exception e) {
				Thread.sleep(100);
			}
		}
	}
}

@Transactional
public void order(long productId, int quantity) {
        Product product = productRepository.findProductById(productId).orElseThrow();
        product.decreaseQuantity(quantity);
}
```

- update 하려고 할 때 자신의 version과 DB version이 일치하지 않으면 에러를 발생시킨다.

```
Batch update returned unexpected row count from update [0]; actual row count: 0; expected: 1; statement executed: update product set quantity=?, version=? where id=? and version=?; nested exception is org.hibernate.StaleStateException:
```

```java
@Transactional
public void order(long productId, int quantity) throws InterruptedException {
    while (true) {
        try {
            Product product = productRepository.findProductById(productId).orElseThrow();
            product.decreaseQuantity(quantity);
        } catch (StaleStateException e) {
            Thread.sleep(10);
        }
    }
}
```

## Named Lock

- [Mysql Locking Functions References](https://dev.mysql.com/doc/refman/5.7/en/locking-functions.html)

```java
public interface ProductRepository extends JpaRepository<Product, Long> {

	@Query(value = "select get_lock(:key, :delay)", nativeQuery = true)
	void getLock(String key, int delay);

	@Query(value = "select release_lock(:key)", nativeQuery = true)
	void releaseLock(String key);
}
```

```java
@Service
public class NamedLockService {

	private final ProductService productService;
	private final ProductRepository productRepository;

	private final String lockKey = "order";

	public void order(long productId, int quantity) {
		try {
			productRepository.getLock(lockKey, 3000);
			productService.order(1L, 1);
		} finally {
			productRepository.releaseLock(lockKey);
		}
	}
}
```

## Lettuce

```java
@Component
public class RedisRepository {

	private final RedisTemplate<String, String> redisTemplate;

	public boolean getLock(String lockKey, String lockValue) {
		return redisTemplate.opsForValue()
			.setIfAbsent(lockKey, lockValue, Duration.ofSeconds(3));
	}

	public void releaseLock(String lockKey) {
		redisTemplate.delete(lockKey);
	}
}
```

```java
@Service
public class LettuceLockService {

	private final ProductService productService;
	private final RedisRepository redisRepository;

	private final String lockKey = "order";

	public void order(long productId, int quantity) throws InterruptedException {
		while (true) {
			if (redisRepository.getLock(lockKey, "lock")) {
				productService.order(1L, 1);
				redisRepository.releaseLock(lockKey);
				break;
			}
			Thread.sleep(100);
		}
	}
}
```

## Redisson

- [Redisson 설명](https://github.com/TIL-Repo/redisson-study)

```java
@Component
public class RedissonRepository {

	private final RedissonClient redissonClient;

	public void getLock(String lockName, long waitTime, long releaseTime) {
		try {
			redissonClient.getLock(lockName).tryLock(waitTime, releaseTime, TimeUnit.SECONDS);
		} catch (InterruptedException e) {
			throw new RuntimeException(e);
		}
	}

	public void releaseLock(String lockName) {
		RLock lock = redissonClient.getLock(lockName);
		lock.unlock();
	}
}
```

```java
@Service
public class RedissonLockService {

	private final ProductService productService;
	private final RedissonRepository redissonRepository;

	private final String lockKey = "order";

	public void order(long productId, int quantity) {
		redissonRepository.getLock(lockKey, 5, 5);
		productService.order(1L, 1);
		redissonRepository.releaseLock(lockKey);
	}
}
```

## References

- [https://github.com/TIL-Repo/redisson-study](https://github.com/TIL-Repo/redisson-study)
- [https://dev.mysql.com/doc/refman/5.7/en/locking-functions.html](https://dev.mysql.com/doc/refman/5.7/en/locking-functions.html)
- [https://github.com/mangchhe/TIL/blob/main/database/exclusive-shared-lock.md](https://github.com/mangchhe/TIL/blob/main/database/exclusive-shared-lock.md)
- [https://github.com/mangchhe/TIL/blob/main/database/optimistic-pessimistic-lock.md](https://github.com/mangchhe/TIL/blob/main/database/optimistic-pessimistic-lock.md)
- [https://www.inflearn.com/course/%EB%8F%99%EC%8B%9C%EC%84%B1%EC%9D%B4%EC%8A%88-%EC%9E%AC%EA%B3%A0%EC%8B%9C%EC%8A%A4%ED%85%9C/dashboard](https://www.inflearn.com/course/%EB%8F%99%EC%8B%9C%EC%84%B1%EC%9D%B4%EC%8A%88-%EC%9E%AC%EA%B3%A0%EC%8B%9C%EC%8A%A4%ED%85%9C/dashboard)