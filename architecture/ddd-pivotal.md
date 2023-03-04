# 마이크로서비스 개발을 위한 Domain Driven Design

- 강의 → [https://youtu.be/QUMERCN3rZs](https://youtu.be/QUMERCN3rZs)

> DDD
>
> 마이크로서비스아키텍처를 말할 때 많이 언급되는 디자인 패턴 중 하나
> 실제 비즈니스 도메인을 우리가 만들려고 하는 아키텍처에 투영함에 따라 도메인을 정의하고 그것을 바탕으로 서로 커뮤니케이션하는데 좋은 툴이 된다.
> 현업에서 기획, 사업부, 개발 기획, 설계, 개발 등이 서로 동일한 언어를 사용함으로써 comunication cost를 minimize 하게 할 수 있다.

## Event Storming

<img width="1324" alt="event-storming" src="https://user-images.githubusercontent.com/50051656/222873920-5c0e05c9-a8a5-44cc-94cf-d4e5b918c5bd.png">

<span style='background-color: orange'>Item Added to Cart</span>

- 비즈니스에서 일어나는 이벤트
- 과거, 수동형으로 작성

<span style="color: blue">Add Item to Cart</span>

- 이벤트가 일어나게 되는 트리거

<span style="color: yellow">Item</span>

- 엔티티
- 하나 이상 Attribute를 포함하는 객체
- 트랜잭션 경계를 구성하는 단위
- aggregate끼리는 object끼리 reference 하는 것이 아닌 id로 reference 하여 loose coupling 한 구조를 가져간다.
- 이벤트가 발생되면 aggregate도 state change가 있다. (atomic)

<span style="color: red">SMTP</span>

- 외부 시스템

## Boris Diagram

<img width="992" alt="boris-diagram" src="https://user-images.githubusercontent.com/50051656/222873923-6e7c8a8f-66ec-4bcf-b5f5-2e2cac1816e4.png">

- aggregate 간의 통신을 sync, async로 표현하여 technical 한 implementation을 구현한다.
- BFF(Backend For Frontend)를 구성

## SnapE

<img width="318" alt="snapE" src="https://user-images.githubusercontent.com/50051656/222873925-d67d85cc-e77c-4df4-84fe-864297c71382.png">

- 서비스들의 상세 spec을 작성하게 된다.
- API, DATA, STORIES, RISK, UI를 정의함.