# Spring Boot Profile 설정

- 애플리케이션이 서로 다른 환경에서 다른 환경 세팅을 가지게 할 수 있게 하는 것을 말한다.
- profile을 이용하면 로컬/개발/운영마다 서로 다른 PATH, DB, URL을 손쉽게 설정하여 사용할 수 있다.

## 목차

- [profile 설정 방법](#profile-설정-방법)
- [profile include](#profile-include)
- [profile group](#profile-group)
- [profile import](#profile-import)

## profile 설정 방법

### Case 1. 파일명으로 구분

- `application-{env}.yml`와 같이 파일명으로 profile을 구분하는 방식

```yml
application-dev.yml
application-real.yml
application-common.yml
```

### Case 2. 한 파일에 여러 profile 구분

- Spring Boot 2.4.0v을 기준으로 사용하는 방법이 달라진다.

#### 2.4.0v 이전

- `spring.profiles`로 profile 설정

```yml
spring:
  profiles:
    active: dev
---
spring:
  profiles: dev
---
spring:
  profiles: real
```

#### 2.4.0v 이후

- `spring.config.activate.on-profile`로 profile 설정

```yml
spring:
  profiles:
    active: dev
---
spring:
  config:
    activate:
      on-profile: dev
---
spring:
  config:
    activate:
      on-profile: real
```

## profile include

- `spring.profiles.include`로 profile로 여러 개 지정할 수 있다.

```yml
spring:
  profiles: dev-common
    include:
      - dev
      - common
```

### 주의사항

- v2.4.0 이후부터는 `include`는 **특정 profile이 적용된 곳에서는 사용할 수 없다.**
- profile 별로 include로 적용해야 할 profile이 달라야 하지만 불가능해졌다.
- 아래 group으로 해결 

## profile group

- `spring.profiles.group`로 profile group을 생성
- `profile명` : `적용할 profiles`로 여러 개의 profile을 가지는 profile을 만들 수 있다.
- 만약 적용한 profile이 같은 설정 정보를 가진다면 후에 적용된 profile 정보로 덮어 씌워져 적용된다.

```yml
# 추가
spring:
  config:
    activate:
      on-profile: common

spring:
  profiles:
    group:
      "dev-common" : "dev, common"
      "real-common" : "real, common"

```

## profile import

- `spring.config.import`로 하나 또는 두 개 이상의 설정 파일을 적용할 수 있다.
- 만약 파일이 존재하지 않는다면 `ConfigDataResourceNotFoundException` 발생
    - Exception을 무시하려면 optional:classpath:/application-dev.yml 접미사로 optional을 붙이면 무시된다.


```yml
spring:
  config:
    import:
      - classpath:/application-dev.yml
      - classpath:/application-common.yml
```

## Reference

- [https://spring.io/blog/2020/08/14/config-file-processing-in-spring-boot-2-4](https://spring.io/blog/2020/08/14/config-file-processing-in-spring-boot-2-4)
- [https://multifrontgarden.tistory.com/277](https://multifrontgarden.tistory.com/277)