# Today I Learned

## Java

- [GC(Garbage Collection) 개념과 종류][garbage-collection-type]
- [Reflection 정리][reflection]
- [Synchronized, Volatile, Atomic Type][synchronized-volatile-atomic]
- [ThreadLocal][threadlocal]
- [SerialVersionUID 지정하는 이유][serialVersionUID]

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

## JPA

- [영속성 컨텍스트 개념 정리][persistence-context]
- [단/양방향 매핑 & 연관관계 주인][directional-association-mapping]
- [연관관계 - N:1, 1:N, 1:1, N:N][association-mapping-type]
- [상속 관계 매핑][inheritance-mapping]
- [@MappedSuperclass, @AttributeOverrides][mapped-superclass-mapping]
- [Cascade, OrphanRemoval][cascade-orphanremoval]
- [쿼리 생성 네이밍에 사용되는 키워드][query-creation]
- [Get vs Find][get-find]

## DB

- [Index][index]
- [Exclusive Lock, Shared Lock][exclusive-shared-lock]
- [Transaction Isolation Level][transaction-isolation-level]
- [Optimistic Lock, Pessimistic Lock][optimistic-pessimistic-lock]
- [Nested Loop Join, Sort Merge Join, Hash Join][nl-sort-merge-hash-join]

## Web

- [HttpOnly, Secure - Cookie][cookie-httponly-secure]

## Tool

- [Intellij .http 사용 방법][intellij-http-request]

[java]: ./java
[garbage-collection-type]: ./java/garbage-collection-type.md
[reflection]: ./java/reflection.md
[synchronized-volatile-atomic]: ./java/synchronized-volatile-atomic.md
[threadlocal]: ./java/threadlocal.md
[jdk-dynamic-proxy-cglib]: ./java/jdk-dynamic-proxy-cglib.md
[serialVersionUID]: ./java/serialVersionUID.md

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

[jpa]: ./jpa
[persistence-context]: ./jpa/persistence-context.md
[directional-association-mapping]: ./jpa/directional-association-mapping.md
[association-mapping-type]: ./jpa/association-mapping-type.md
[inheritance-mapping]: ./jpa/inheritance-mapping.md
[mapped-superclass-mapping]: ./jpa/mapped-superclass-mapping.md
[cascade-orphanremoval]: ./jpa/cascade-orphanremoval.md
[query-creation]: ./jpa/query-creation.md
[get-find]: ./jpa/get-find.md

[db]: ./database
[index]: ./database/index.md
[exclusive-shared-lock]: ./database/exclusive-shared-lock.md
[transaction-isolation-level]: ./database/transaction-isolation-level.md
[optimistic-pessimistic-lock]: ./database/optimistic-pessimistic-lock.md
[nl-sort-merge-hash-join]: ./database/nl-sort-merge-hash-join.md

[web]: ./web
[cookie-httponly-secure]: ./web/cookie-httponly-secure.md

[tool]: ./tool
[intellij-http-request]: ./tool/intellij-http-request.md