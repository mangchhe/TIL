# GC(Garbage Collection) 개념과 종류

## GC란?

- JVM에서 해주는 기능 중 하나로 Heap 영역에서 사용되고 있지 않은 객체를 제거해주는 역할을 한다.

### 수거(제거) 대상

- 객체 상태가 두 가지로 나뉨
    - Unreachable : 참조되고 있지 않은 객체 (사용되지 않는 객체)
    - Reachable :  참조되고 있는 객체 (사용되고 있는 객체)

### GC 동작 순서

- GC Root에서 주소를 따라가 참조하고 있는지 확인하여 Unreachable, Reachable을 판단한다.
- GC Root의 종류
    - Stack 영역의 데이터(지역 변수, 파라미터)
    - Method 영역의 static 데이터
    - JNI에의 생성된 데이터
- Mark And Sweep (Compact) 알고리즘이 대표적
    - Mark : GC Root로부터 변수를 찾아가며 객체를 참조하고 있는지를 Unreachable, Reachable로 구분한다.
    - Sweep : Unreachable 객체들을 Heap에서 제거한다.
    - Compact : 객체들이 제거되고 중간중간 비어있는 공간들을 한곳으로 모아 메모리 단편화를 막아주는 역할을 한다.

### Heap 메모리 영역 구조

- 크게 Young Generation, Old Generation 영역으로 나뉜다.
- Young Generation
    - Eden
    - Survivor1, 2
- Old Generation
    - Old
    - Permanant

### Heap 메모리 영역이 꽉 찼을 때?

- Eden 영역부터 객체들이 채워지며 꽉 찼을 경우에는 Minor GC(Young Generation)가 발생한다.
- Eden 영역에서 참조되지 않은 객체들은 해제되고 참조되고 있는 객체들은 Suvivor1으로 이동한다.
    - 이때 규칙이 있는데 Eden이 채워져 Survivor로 이동할 때 Survivor1, 2를 번갈아 가며 이동한다. 기존에 있던 객체도 포함
    - Survivor에서 살아남은 객체들은 age 값이 계속 증가한다.
- Age 값의 임계치를 초과한다면 해당 객체들은 Old 영역으로 이동하게 된다.
- Old 영역 또한 가득 차게 되면 Major GC(Old Generation)가 발생한다.

### Stop the world

- GC 스레드가 진행되는 동안 애플리케이션 스레드가 모두 중단되는 상황을 말함

## GC의 종류

### Serial GC

- 싱글 스레드로 GC를 수행하며 Mark-Sweep-Compact 알고리즘을 사용한다.

### Parallel GC

- Java8의 Default GC
- Serial GC랑은 다르게 Young 영역일 때 멀티 스레드로 동작하여 stop-the-world 시간이 단축된다.

### Parallel Old GC

- Parallel GC를 개선하기 위해 나온 방법
- Parallel GC에서 Old 영역까지 멀티 스레드로 GC를 수행한다.
- Mark-Summary-Compact 알고리즘으로 수행한다.
    - Summary : Old 영역을 멀티스레드로 처리

### CMS GC (Current Mark Sweep)

- stop-the-world의 시간을 줄이기 위해 나온 방법
- Reachable 객체를 찾는 동시에 참조되지 않는 객체들을 제거한다. Mark-Sweep이 동시에 일어남
- Compact가 일어나지 않기 때문에 단편화 문제는 발생할 수 있다.

### G1 GC (Garbage First)

- CMS GC를 개선하기 위해 나온 방법
- Java 9+ Default GC
- 이전처럼 Young, Old Generation으로 나누지 않고 Heap을 일정한 크기의 Region으로 나눔
- 전체 Heap이 아닌 할당된 Region 단위로 검색

## Referece

- [https://www.youtube.com/watch?v=Fe3TVCEJhzo](https://www.youtube.com/watch?v=Fe3TVCEJhzo)