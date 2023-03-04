# ㄷㄷㄷ: Domain Driven Design과 적용 사례공유 / if(kakao)2022

- 강의 → [https://youtu.be/4QHvTeeTsj0](https://youtu.be/4QHvTeeTsj0)

## Domain Driven Design

- 데이터 중심이 아닌 도메인의 모델과 로직에 집중
- Ubiquitous Language, 단일화된 커뮤니케이션 방식
- Software Entity와 Domain간 개념 일치 

## Why DDD?

<img width="1155" alt="ddd-bdd-tdd" src="https://user-images.githubusercontent.com/50051656/222878216-cc7f3b53-c01b-4d4c-90f5-847f10e1f3d1.png">

TDD : 반복적인 설계 수정과 테스트 자동화.
BDD : 비즈니스의 협업 중시로 이루어지면서 테스트 자동화.
DDD : 비즈니스와의 협업을 중시하면서 반복적인 설계 수정.

## 중요한 개념 세 가지

### Bounded Context

<img width="1164" alt="bounded-context" src="https://user-images.githubusercontent.com/50051656/222878219-07aa8a3b-53fe-4055-a20d-dc6c7d6b47e0.png">

- 범위를 구분해놓은 하위 도메인 개념

### Context Map

<img width="1181" alt="context-map" src="https://user-images.githubusercontent.com/50051656/222878220-96b38de8-b63e-432e-8e54-146df9d3a212.png">

- Bounded Context 간에 관계를 나타냄

### Aggregate

<img width="1173" alt="aggregate" src="https://user-images.githubusercontent.com/50051656/222878221-a34c8b89-988f-45b5-9688-4bb87379406e.png">

- 데이터 변경 단위
- life cycle이 같은 도메인들은 한곳에 모아둔 집합

## 단점

- MSA에서 오는 단점 (높은 개발 난이도와 숙련도가 필요)
- Architecture 구현에 생성되는 생각보다 많은 코드
- 각 도메인에 대한 높은 이해도가 필요

## 장점

- 보펀젹인 언어 사용에 따른 빠른 커뮤니케이션
- 도메인간 관계가 복잡한 경우 큰 틀에서 정리가 가능
- 도메인 분리에 따른 유지보수에 대한 편의성
- 새로운 기능 및 요구 사항에 대한 유연성
- Encapsulation
- Loose coupling, High cohesion
- Domain Logic 분리로 Business Logic에 집중
- 코드 가독성