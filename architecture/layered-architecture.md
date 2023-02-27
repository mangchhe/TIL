# Layered Architecture (계층형 아키텍처)

- 소프트웨어 개발에 있어서 가장 일반적으로 널리 사용되는 아키텍처
- 구성되는 계층의 숫자에 따라 N 계층 아키텍처라고 한다.

# 4-Tier Layered Architecture

![layered-architecture](https://user-images.githubusercontent.com/50051656/221592464-2a1f4350-9854-4209-8c7a-0f3ddeedd401.png)

## Presentation Layer

- 사용자에게 데이터를 전달하는 정보를 표시하는 것에 관심사를 둔다.
- 비즈니스 로직이 어떻게 수행되는지 알 필요가 없다.
- 대표적인 구성요소는 View, Controller가 있다.

## Business Layer

- 비즈니스 로직을 수행하는 것에 관심사를 둔다.
- 데이터를 어떻게 출력하는지, 어디서 데이터를 가져오는지에 대해서 알 필요가 없다.
- 대표적인 구성요소는 Service, Domain Model이 있고 때에 따라서 별도의 계층으로 분리한다.

## Persistence Layer

- 애플리케이션의 영속성을 구현하기 위해 데이터를 가져오거나 다루는 데에 있어 관심사를 둔다.
- 대표적인 구성요소는 Repository, DAO가 있다.

## Database Layer

- 데이터베이스가 위치한 계층을 말한다.

# 닫힌 계층

![layered-architecture2](https://user-images.githubusercontent.com/50051656/221592440-b4fef88f-0e02-44df-b12f-5ddbc178b0f8.png)

- **각 계층은 닫혀있고** 이것은 계층형 아키텍처 패턴의 중요한 개념이다.
- 사진에서 보면 각 계층을 통과하기 위해서는 이전 계층을 먼저 거쳐서 통과해야 한다.
- 그 결과 Presentation 계층이 Persistence 계층을 바로 통과하지 못하는 것에 대해 매우 비효율적으로 보인다.
- 해답은 **격리** 라는 핵심 개념에 있다.
- 각 계층은 다른 계층의 변화에 대해서 영향을 최소화해야 한다. 즉, 격리되어 있으면 변화에 있어서 주변에 영향을 적게 주게 된다.
- 만약 Presentation 계층과 Business 계층이 Persistence 계층에 접근하게 되면 Persistence의 변경에 해당 두 계층이 모두 영향을 받게 되므로 상호 밀접한 애플리케이션을 만들게 된다.
- 결과적으로 유지보수 비용이 커진다.

# 열린 계층

![layered-architecture3](https://user-images.githubusercontent.com/50051656/221592451-9700a3b1-7e43-45f1-9073-4ed0e8eb1d39.png)

- Service 계층이 추가되었고 열려있다.
- 열려있다는 소리는 Service 계층을 우회해서 바로 Persistence 계층에 접근할 수 있는 것을 의미한다.
- 필요에 따라 우회할 수 있도록 설계할 수 있고 변경 가능성을 잘 고려한다면 좋은 생산성도 가질 수 있다.