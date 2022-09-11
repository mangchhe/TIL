# Today I Learned

## Java

- [GC(Garbage Collection) 개념과 종류][garbage-collection-type]
- [Reflection 정리][reflection]
- [Synchronized, Volatile, Atomic Type][synchronized-volatile-atomic]
- [ThreadLocal][threadlocal]
- [SerialVersionUID 지정하는 이유][serialVersionUID]
- [Enum 조회 성능 높이기][enum-stream-map]

## Spring

- [Multi Module with Gradle][multi-module-with-gradle]
- [Retryable, Recover 사용법][retryable-recover-basic]
- [Jackson Annotation 정리][jackson-annotation]
- [RequestContextHolder][requestcontextholder]
- [JDK Dynamic Proxy, CGLib][jdk-dynamic-proxy-cglib]
- [로그 추적기][log-trace]
- [Proxy Factory][proxy-factory]
- [Advisor, Advice, Pointcut 개념][advisor-advice-pointcut]
- [빈 후처리기][bean-post-processor]
- [@Aspect][aspect]
- [AOP 개념][aop-concept]
- [AOP 구현에 사용되는 어노테이션 사용법][aop-annotations]
- [Spring Boot Profile 설정][profile-config]
- [Configuration 값 가져오는 방법 - @Value, @ConfigurationProperties][property-config]
- [@Profile, @ActiveProfiles][profile-activeprofiles]
- [날짜 다루기 - @DateTimeFormat, @JsonFormat][datetimeformat-jsonformat]
- [Redisson으로 분산 락 구현][redisson]
- [Spring Events - @EventListener, @TransactionalEventListener][spring-events]
- [Transaction Propagation][transaction-propagation]
- [Servlet][servlet-basic]
- [Interceptor 구현][interceptor]
- [Filter 구현][filter]
- [캐시 사용하기 - @Cacheable, @CachePut, @CacheEvict][cacheable-cacheput-cacheevict]
- [중복 타입 Bean 솔루션 - @Primary, @Qualifier][duplicate-bean-primary-qualifier]
- [동시성 이슈 해결 방법 - Mysql, Redis][concurrency-issue-mysql-redis]

## JPA

- [영속성 컨텍스트 개념 정리][persistence-context]
- [단/양방향 매핑 & 연관관계 주인][directional-association-mapping]
- [연관관계 - N:1, 1:N, 1:1, N:N][association-mapping-type]
- [상속 관계 매핑][inheritance-mapping]
- [@MappedSuperclass, @AttributeOverrides][mapped-superclass-mapping]
- [Cascade, OrphanRemoval][cascade-orphanremoval]
- [쿼리 생성 네이밍에 사용되는 키워드][query-creation]
- [Get vs Find][get-find]
- [Sort, Pageable, Slice, Page][sort-pageable-slice-page]
- [Auditing, AuditorAware][auditing-auditoraware]
- [Entity Lifecycle Events][entity-lifecycle-events]
- [Fetch Join - JPQL, EntityGraph][fetch-join-jpql-entitygraph]
- [Enumerated, AttributeConverter][enumerated-attributeconverter]
- [JPA에서 JSON Column 사용하기][jpa-json-column]
- [JPA 동적 쿼리 만들기 - @DynamicInsert, @DynamicUpdate][dynamicinsert-dynamicupdate]
- [JPA Multi Database 설정][jpa-multi-database]

## DB

- [Index][index]
- [Exclusive Lock, Shared Lock][exclusive-shared-lock]
- [Transaction Isolation Level][transaction-isolation-level]
- [Optimistic Lock, Pessimistic Lock][optimistic-pessimistic-lock]
- [Nested Loop Join, Sort Merge Join, Hash Join][nl-sort-merge-hash-join]

## Web

- [HttpOnly, Secure - Cookie][cookie-httponly-secure]

## OS

- [운영체제의 개념과 구조][os-basic]
- [프로세스][process-basic]

## Network

- [IP 체계, 서브넷 마스크][ip-class-subnetmask]

## Kafka

- [Kafka를 사용하는 이유와 특징][why-use-kafka]

## Tool

- [Intellij .http 사용 방법][intellij-http-request]

[java]: ./java
[garbage-collection-type]: ./java/garbage-collection-type.md
[reflection]: ./java/reflection.md
[synchronized-volatile-atomic]: ./java/synchronized-volatile-atomic.md
[threadlocal]: ./java/threadlocal.md
[jdk-dynamic-proxy-cglib]: ./java/jdk-dynamic-proxy-cglib.md
[serialVersionUID]: ./java/serialVersionUID.md
[enum-stream-map]: ./java/enum-stream-map.md 

[spring]: ./spring
[multi-module-with-gradle]: ./spring/multi-module-with-gradle.md 
[retryable-recover-basic]: ./spring/retryable-recover-basic.md
[jackson-annotation]: ./spring/jackson-annotation.md
[requestcontextholder]: ./spring/requestcontextholder.md
[log-trace]: ./spring/log-trace.md
[proxy-factory]: ./spring/proxy-factory.md
[advisor-advice-pointcut]: ./spring/advisor-advice-pointcut.md
[bean-post-processor]: ./spring/bean-post-processor.md
[aspect]: ./spring/aspect.md
[aop-concept]: ./spring/aop-concept.md
[aop-annotations]: ./spring/aop-annotations.md
[profile-config]: ./spring/profile-config.md
[property-config]: ./spring/property-config.md
[profile-activeprofiles]: ./spring/profile-activeprofiles.md
[datetimeformat-jsonformat]: ./spring/datetimeformat-jsonformat.md
[redisson]: https://github.com/TIL-Repo/redisson-study
[spring-events]: https://github.com/TIL-Repo/spring-study/tree/main/EventListener
[transaction-propagation]: ./spring/transaction-propagation.md
[servlet-basic]: ./spring/servlet-basic.md
[interceptor]: https://mangchhe.github.io/springboot/2021/12/08/SpringBootInterceptor
[filter]: https://mangchhe.github.io/springboot/2021/12/02/SpringBootFilter
[cacheable-cacheput-cacheevict]: https://mangchhe.github.io/springboot/2021/09/15/SpringBootCache
[duplicate-bean-primary-qualifier]: ./spring/duplicate-bean-primary-qualifier.md
[concurrency-issue-mysql-redis]: ./spring/concurrency-issue-mysql-redis.md

[jpa]: ./jpa
[persistence-context]: ./jpa/persistence-context.md
[directional-association-mapping]: ./jpa/directional-association-mapping.md
[association-mapping-type]: ./jpa/association-mapping-type.md
[inheritance-mapping]: ./jpa/inheritance-mapping.md
[mapped-superclass-mapping]: ./jpa/mapped-superclass-mapping.md
[cascade-orphanremoval]: ./jpa/cascade-orphanremoval.md
[query-creation]: ./jpa/query-creation.md
[get-find]: ./jpa/get-find.md
[sort-pageable-slice-page]: ./jpa/sort-pageable-slice-page.md
[auditing-auditoraware]: ./jpa/auditing-auditoraware.md
[entity-lifecycle-events]: ./jpa/entity-lifecycle-events.md
[fetch-join-jpql-entitygraph]: ./jpa/fetch-join-jpql-entitygraph.md
[enumerated-attributeconverter]: ./jpa/enumerated-attributeconverter.md
[jpa-json-column]: ./spring/jpa-json-column.md
[dynamicinsert-dynamicupdate]: https://mangchhe.github.io/jpa/2021/09/06/EntityDynamicQuery
[jpa-multi-database]: ./jpa/jpa-multi-database.md

[db]: ./database
[index]: ./database/index.md
[exclusive-shared-lock]: ./database/exclusive-shared-lock.md
[transaction-isolation-level]: ./database/transaction-isolation-level.md
[optimistic-pessimistic-lock]: ./database/optimistic-pessimistic-lock.md
[nl-sort-merge-hash-join]: ./database/nl-sort-merge-hash-join.md

[web]: ./web
[cookie-httponly-secure]: ./web/cookie-httponly-secure.md

[os]: ./os
[os-basic]: ./os/os-basic.md
[process-basic]: ./os/process-basic.md

[network]: ./network
[ip-class-subnetmask]: ./network/ip-class-subnetmask.md

[kafka]: ./kafka
[why-use-kafka]: ./kafka/why-use-kafka.md

[tool]: ./tool
[intellij-http-request]: ./tool/intellij-http-request.md