# ApplicationContextAware, BeanNameAware

## ApplicationContextAware

- Bean으로 사용될 객체에서 스프링 컨테이너에 접근해야 할 때 사용한다.
- 해당 인터페이스를 상속받은 Bean 객체는 초기화 과정에서 `ApplicationContext`를 전달받는다.

```kotlin
@Component
class ApplicationContextProvider: ApplicationContextAware {

    companion object {
        lateinit var applicationContext: ApplicationContext

        fun getBean(beanName: String): Any {
            return applicationContext.getBean(beanName)
        }
    }

    override fun setApplicationContext(context: ApplicationContext) {
        applicationContext = context
    }
}
```

## BeanNameAware

- Bean Factory에서 Bean 이름을 설정할 때 사용한다.

```kotlin
@Component
class HelloClass: BeanNameAware {

    companion object {
        lateinit var beanName: String
    }

    override fun setBeanName(name: String) {
        println("BeanNameAware.setBeanName() called : name = $name")
        beanName = name
    }
}
```