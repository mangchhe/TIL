# Clean Architecture

- 기존 Layered Architecture가 가지던 의존성에서 벗어나게 하는 설계를 제공
- 외부 인터페이스에 독립적으로 구현할 수 있게 한다.
- 확장 가능하고 테스트가 가능한 애플리케이션을 만드는 것에 용이한 구조를 가진다.

![clean-architecture](https://user-images.githubusercontent.com/50051656/222731222-059662be-c937-498b-b38d-12a11f10d0c9.jpeg)

- 의존성이 원 안쪽으로 향한다. (저수준 -> 고수준)
- 즉, 바깥쪽 원에 해당하는 어떠한 것들도 안쪽 원에 영향을 주지 못한다.

> 저수준, 고수준
>
> 저수준(세부 사항들) : 추상화된 개념을 실제 어떻게 구현할지에 대한 세부적인 개념
>
> 고수준(업무 로직) : 상위 수준의 개념, 추상화된 개념

## 구성 요소

- Entity : 실제 객체라는 의미로 업무에 필요하고 유용한 정보를 저장하고 관리하기 위한 집합적인 것
- UseCase : 비즈니스 규칙 정의
- Controller : 애플리케이션 API 엔드포인트
- Gateway : API 게이트웨이는 실제 백엔드 서비스 또는 데이터와 접속하고 API 호출에 대한 정책. 인증 및 일반 액세스 제어를 적용하여 중요한 데이터를 보호하는 트래픽 관리자
- Presenter : Container에서 처리한 상태를 props로 전달받아 상태를 화면에 출력하는 컴포넌트

## 왜 사용하나?

- 예를 들어 특정 서비스가 웹으로 런칭하고 성공했을 때 앱으로도 확장을 원할 때
- 핵심 비즈니스 로직이 외부 인터페이스에 의존하고 있다고 한다면 같은 기능이라도 변경해야 할 사항이 많아진다.
- 하지만 해당 아키텍처가 적용되어 있다면 외부와 통신하는 인터페이스 영역만 스펙을 일부 변경하여 동일한 기능을 제공할 수 있다.
- 적은 리소스로 확장하기 용이한 애플리케이션으로 만들 수 있다.

# Reference

- [http://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html](http://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)
- [https://techblog.woowahan.com/2647/](https://techblog.woowahan.com/2647/)
- [https://velog.io/@___pepper/%ED%81%B4%EB%A6%B0-%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98-%EA%B5%AC%EC%A1%B0-%EB%B0%8F-%EC%A3%BC%EC%9A%94-%EA%B0%9C%EB%85%90](https://velog.io/@___pepper/%ED%81%B4%EB%A6%B0-%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98-%EA%B5%AC%EC%A1%B0-%EB%B0%8F-%EC%A3%BC%EC%9A%94-%EA%B0%9C%EB%85%90)