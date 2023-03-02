# Hexagonal Architecture

- Layered Architecture에 DIP를 적용해도 한계는 존재
    - Presentation, Data Access 계층을 보통 저수준 계층으로 정의
    - 하지만, 현대 애플리케이션에서는 해당 두 가지 계층 말고도 다양한 인터페이스를 필요로 한다.
- 애플리케이션을 호출하는 시스템의 유형과 애플리케이션과 상호작용하는 다양한 저장소가 존재
- Hexagonal Architecture는 위와 같은 문제점을 해결한다.
- ports and adapters architecture라고도 부른다.

![hexagonal-architecture](https://user-images.githubusercontent.com/50051656/222454861-ce02a5e7-a4d6-4776-8449-b7919041623d.png)

- 내부 영역은 순수한 비즈니스 로직을 표현하는 기술 독립적인 영역. 외부 영역과 연계되는 포트를 가진다.
- 외부 영역은 외부에서 들어오는 요청을 인 바운드 어댑터와 비즈니스 로직에 의해 호출되어 외부와 연계되는 아웃 바운드 어댑터로 구성된다.
- 해당 아키텍처는 고수준의 내부 영역이 **포트**를 이용하여 어댑터에 전혀 의존하지 않게 한다는 것이다.
    - 포트는 인 바운드 포트, 아웃 바운드 포트로 나뉜다.
    - 인 바운드 포트는 내부 영역을 사용하기 위해 표출된 API이며 외부 영역의 인바운드 어댑터가 호출한다.
    - 아웃 바운드 포트는 내부 영역이 외부를 호출하는 방법을 정의한다.
    - DIP 원칙과 같이 아웃 바운드 포트가 외부의 아웃 바운드 어댑터를 호출하여 외부 시스템과 연계하는 것이 아닌 아웃 바운드 어댑터가 아웃 바운드 포트에 의존하여 구현된다.

![hexagonal-architecture2](https://user-images.githubusercontent.com/50051656/222454875-fa81239a-da59-4420-ad6e-4c1fa37a23ce.png)

# Reference

- [https://vaadin.com/blog/ddd-part-3-domain-driven-design-and-the-hexagonal-architecture](https://vaadin.com/blog/ddd-part-3-domain-driven-design-and-the-hexagonal-architecture)
- [https://herbertograca.com/2017/11/16/explicit-architecture-01-ddd-hexagonal-onion-clean-cqrs-how-i-put-it-all-together/](https://herbertograca.com/2017/11/16/explicit-architecture-01-ddd-hexagonal-onion-clean-cqrs-how-i-put-it-all-together/)