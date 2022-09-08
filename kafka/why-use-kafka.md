# Kafka를 사용하는 이유와 특징

## Kafka 창시 배경

- [Kafka 개발자 왈](https://www.youtube.com/watch?v=3F4XwgCfQc8)
- 링크드인에서 두 가지 이슈
    - 데이터 중앙저장소가 무엇인가?
    - 다양한 데이터 소스가 존재
- 데이터를 어떻게 통합하여 관리할 수 있을지 고민

### Kafka 도입 이전

- 각 서비스가 End-To-End 통신을 하면서 서로 연결되어있고 데이터를 주고받는다.
- 관리하는 서비스가 많아질수록 아키텍처가 복잡해지고 관리하기 어렵다.
    - 파편화된 데이터들을 수집할 때 어려움을 겪는다.
    - 연결되어 있기 때문에 한쪽이 문제가 생기면 비정상적으로 작동하게 된다.
- 요즘 MSA를 이용하고 Docker, k8s 사용하는 시점에서 매우 치명적이다.

![before](https://user-images.githubusercontent.com/50051656/188687075-c8f8a09c-b8f3-42b4-90c1-8c45aa02257d.png)

### Kafka 도입 후

- 애플리케이션, RDB 등 직접적으로 연결하지 않고 중간에 오고 가는 데이터를 관리할 수 있는 Kafka를 둬서 이를 해결함

![after](https://user-images.githubusercontent.com/50051656/188687064-721f0fb5-1533-422b-940d-4ec91723c892.png)

## Kafka 특징

### Message Queue

- 메시지 큐 기반 (FIFO) 구조를 활용한다.
- 애플리케이션은 target 애플리케이션 설정이 필요 없이 데이터를 넣고 target 애플리케이션은 데이터를 순서대로 가져가기만 된다.
- 이로 인해 애플리케이션 어느 한쪽이 에러가 발생해도 문제가 생기지 않는다. (Loose Coupling 한 상태를 유지)

### 장애 발생에 대비하는 영속성

- kafka는 다른 플랫폼과 다르게 데이터를 메모리가 아닌 파일 시스템에 저장한다.
- 갑작스럽게 서비스가 다운되더라도 데이터가 사라지지 않고 남게 된다.
- 파일 시스템으로 저장했기 때문에 I/O 작업에 시간이 많이 소요되는데 따로 캐시 영역을 만들어서 한번 읽은 파일은 캐싱 처리하여 속도 문제를 해결하였다.

### 카프카 내부의 고가용성

- 일반적으로 3개 이상의 카프카 클러스터를 운영한다.
- 일부 서버가 장애가 발생하더라도 나머지 서버가 살아있기 때문에 무중단으로 안전하고 지속적으로 운영이 가능하다.
- producer로 받은 데이터는 하나의 broker에 저장하는 것이 아니라 여러 broker에 저장하여 복제된 데이터를 통해서 지속적인 데이터 처리가 가능하다.

### 배치 처리를 통한 높은 처리량

- 서로 다른 애플리케이션이 통신하기 위해서 데이터 전송이 있을 때마다 HTTP 3-handshake 과정을 맺고 통신한다.
- 이 프로세스는 통신 비용에 있어서 단점을 가지고 있다.
- kafka에서 동일한 양의 데이터 묶음으로 처리하여 네트워크 비용을 절감하였다.

## References

- [https://www.confluent.io/blog/event-streaming-platform-1/](https://www.confluent.io/blog/event-streaming-platform-1/)
- [https://www.youtube.com/watch?v=3F4XwgCfQc8](https://www.youtube.com/watch?v=3F4XwgCfQc8)
- [https://moonsupport.oopy.io/post/22](https://moonsupport.oopy.io/post/22)