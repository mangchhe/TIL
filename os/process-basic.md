# 프로세스 개념

- 실행 중인 프로그램을 말한다.
- 운영체제는 프로그램들을 실행시켜주는 일을 하고 작업의 단위가 프로세스이다.
- 프로세스가 실행되기 위한 조건
    - CPU
    - Memory
    - files, I/O devices 관리

## 프로세스 영역

- text section : 실행 가능한 명령어 코드
- data section : 전역 변수
- heap section : 런타임 시간 동안 동적으로 메모리를 할당하는 영역
- stack section : 함수 호출 시 임시 데이터 저장소, 파라미터, 반환 주소 값, 지역 변수

<img width="212" alt="memory-section" src="https://user-images.githubusercontent.com/50051656/187215551-7e0add7d-e09c-4291-8832-1e0038eb4184.png">

## 생명주기

- new : 프로세스 생성된 상태
- runnning : 프로세스의 명령어를 cpu에 로드하여 실행시키는 상태
- waiting : 이벤트가 일어나기를 기다리는 상태 (I/O completion, Signal)
- ready : CPU 할당을 기다리는 상태
- terminated : 프로세스의 실행이 종료된 상태

<img width="525" alt="process-state" src="https://user-images.githubusercontent.com/50051656/187215615-1ca585e4-62f9-436c-8d82-f0f3722a361d.png">

## PCB (Process Controll Block) or TCB

- 프로세스가 가져야 할 모든 정보를 저장하는 구조체
- 가지는 정보
    - Process State : new, running etc..
    - Program counter : 다음에 실행할 명령어의 주소를 가짐
    - CPU registers
    - CPU-scheduling information
    - Memory-management information
    - Accounting information
    - I/O status information

<img width="199" alt="pcb" src="https://user-images.githubusercontent.com/50051656/187215605-3ce057f5-59ae-4a33-a49c-e4c76be884ea.png">

## Process Scheduling

- 멀티프로그래밍의 목적은 동시에 여러 개의 프로세스를 실행시켜 CPU의 사용 효율을 높이는 것이다.
- 시분할의 목적은 CPU 코어를 여러 프로세스 사이에서 빈번히 변경하여 사용자가 여러 프로그램들을 동시에 하는 것처럼 보이게 하는 것이다.

### Scheduling Queues

- ready queue : 프로세스들이 CPU를 할당받을 때까지 기다리는 공간
- wait queue : I/O 작업이 있을 때 프로세스들이 대기하는 장소, I/O 작업이 끝나면 ready queue로 이동

<img width="434" alt="ready-queue-wait-queue" src="https://user-images.githubusercontent.com/50051656/187215575-e8209eba-6459-4367-b762-b4e5d87496da.png">

<img width="480" alt="queueing-diagram" src="https://user-images.githubusercontent.com/50051656/187223804-2c36b5aa-1e63-4f0c-baec-2a576900158f.png">


## Context Switch

- 프로세스의 상태를 문맥이라고 할 수 있는데 프로세스의 상태를 저장하는 곳이 PCB이니 PCB가 곧 문맥(Context)이라고 할 수 있다. 
- 인터럽트가 발생하면 현재 실행 중인 프로세스의 문맥을 저장해놓고 다시 ready queue에서 cpu를 할당받아 실행될 때 문맥을 복원한다.
- 하는 일
    - 프로세스 간에 CPU를 스위칭한다.
    - 현재 프로세스의 상태를 저장하고 실행할 다른 프로세스의 상태를 복원한다.

<img width="404" alt="context-switching" src="https://user-images.githubusercontent.com/50051656/187223792-ac7fba94-8b8e-47ec-b04e-08d2344478f1.png">

## Fork

<img width="632" alt="fork" src="https://user-images.githubusercontent.com/50051656/187223799-4c3b5909-7cd4-424a-837a-855d34301fb2.png">

## Zombie and Orphan

- zombie process : 자식 프로세스가 종료되었지만, 부모 프로세스가 자식 프로세스의 종료 상태를 회수하지 않았을 경우에 자식 프로세스를 좀비 프로세스라 한다.
- orphan process : 부모 프로세스가 자식 프로세스보다 먼저 종료되면 자식 프로세스는 고아 프로세스가 된다.