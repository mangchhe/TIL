# `@Aspect`

- 스프링 애플리케이션에 프록시를 적용하기 위해 포인트컷, 어드바이스로 구성된 Advisor를 만들어 빈으로 등록했다.
- 자동 프록시 생성기가 자동으로 빈 중 어드바이저를 찾고 포인트컷에 매칭되는 것만 프록시로 생성한다.
- 이때 `@Aspect`를 이용하게 되면 손쉽게 어드바이저 생성 기능을 제공한다.

## 사용법

- 프록시를 적용할 클래스에 `@Aspect`를 붙여준다.
- `@Around` : Pointcut를 AspectJ 표현식으로 작성한다. (적용 위치)
- execute : 메서드에 advice를 구현한다. (부가 기능 구현)
- ProceedingJointPoint.proceed() : target 클래스의 메서드 실행

```java
@Aspect
public class AspectAdvisor {

	@Around("execution(* me.hajoo.beanpostprocessor.service..*(..))")
	public Object execute(ProceedingJoinPoint joinPoint) throws Throwable {
		String message = joinPoint.getSignature().toShortString();
		log.info("create advice methodName = {}", message);
		return joinPoint.proceed();
	}
}
```

- `@Aspect` 붙인 클래스를 Bean으로 등록한다.

```java
@Configuration
public class AutoProxyConfig {

	@Bean
	public AspectAdvisor advisor() {
		return new AspectAdvisor();
	}
}
```

## `@Aspect`를 Advisor로 변환해서 저장하는 과정

1. 스프링 애플리케이션 로딩 시 자동 프록시 생성기 생성
1. `@Aspect`가 붙은 스프링 빈 조회
1. Advisor 빌더를 통해 `@Aspect`가 붙은 빈 Advisor 생성
1. Advisor 빌더에 생성한 Advisor를 저장

> BeanFactoryAspectJAdvisorsBuilder (Advisor 빌더)
>
> `@Asepect`의 정보를 기반으로 포인트컷, 어드바이스, 어드바이저를 생성하고 보관하는 것을 담당한다.
> 어드바이저를 만들고 빌더 내부 저장소에 캐시한다. 캐시에 이미 만들어져 있는 경우 캐시에 저장된 어드바이저를 반환한다.

## Advisor 기반으로 프록시 생성

1. 스프링 빈 대상이 되는 객체를 생성
1. 생성된 객체를 빈 저장소에 등록하기 전에 빈 후처리기에 전달
1. 조회
    1. Advisor 빈 조회 : 스프링 컨테이너에서 Advisor 빈 모두 조회
    1. **@Aspect Advisor 조회 : `@Aspect`가 붙은 빈, 어드바이저 빌더 내부에 저장된 Advisor 모두 조회**
1. 포인트컷을 보고 프록시 대상인지 확인 후 프록시 생성
1. 프록시로 생성된 객체 또는 아닌 객체 스프링 빈으로 등록