# 운영체제

## 프로그램이란?

- 컴퓨터 하드웨어에 어떤 업무를 수행하는 명령의 집합

## 운영체제는 프로그램인가?

- 컴퓨터에서 항상 실행되는 **프로그램**이고 컴퓨터 시스템을 운영하는 소프트웨어이다.
- 애플리케이션 프로그램에 시스템 서비스를 제공한다.
- 프로세스, 자원, 유저 인터페이스를 관리한다.

## 운영체제가 하는 일

- 컴퓨터 하드웨어를 관리하는 소프트웨어이다.
- 애플리케이션, 유저, 컴퓨터 하드웨어 사이에서 중간 매개 역할을 한다.

<img width="423" alt="os" src="https://user-images.githubusercontent.com/50051656/186690716-066010a8-2756-4771-925c-195de33b6cf2.png">

## 커널(Kernel)

- os의 핵심 부품을 담당
- 시스템 프로그램, 애플리케이션 프로그램 두 가지 종류 프로그램을 제공한다.

## Bootstrap

- 부팅될 때 처음으로 메모리에 부팅되는 프로그램
- 부트스트랩이 메모리에 올라간 후 os가 돌면서 나머지 응용프로그램들을 메모리에 올렸다가 삭제하며 동작한다.

## Interrupts

- I/O device에서 입력이 있을 때 CPU에게 이를 알려줘야 하는데 사용되는 방법 (둘의 통신 방법 중 하나)
- 하드웨어는 언제나 인터럽트를 발생시킬 수 있고 시스템 버스를 통해서 CPU에게 시그널을 전송한다.

<img width="487" alt="os" src="https://user-images.githubusercontent.com/50051656/186693072-dfc344da-1e12-4f5c-9b60-bf9eb8c2aea3.png">

## 폰 노이만 아키텍처

- 명령어들의 집합 프로그램을 메모리에 적재하고 CPU에서 해당 명령어를 fetch하고 execute한다.
- fetch - execute cycle을 폰 노이만 아키텍처라 한다.

## 컴퓨터 시스템 구성요소

- CPU
- Processor
- Core
- Multicore
- Multiprocessor

## Multiprocessing Architecture

<img width="495" alt="multiprocessing" src="https://user-images.githubusercontent.com/50051656/187083778-bf61e54f-7a45-4ec4-a539-f6c6121f5668.png">

## Multi-Core Desgin

<img width="445" alt="multi-core" src="https://user-images.githubusercontent.com/50051656/187083775-92cacca5-2823-4074-8a76-b53ff8ceb32d.png">

## Multiprogramming

- 하나 이상의 프로그램이 같은 시간에 동시에 실행되는 것을 말한다.
- 메모리에 여러 개의 프로세스가 동시에 올라가 있어 CPU 효율을 높일 수 있다.

<img width="191" alt="multiprogramming" src="https://user-images.githubusercontent.com/50051656/187083808-f33cd482-3e47-4413-a75e-bc2bad118c15.png">

## Multitasking(=multiprocessing)

- 하나의 CPU로 하나의 작업만을 하기에는 너무 널널하고 자원이 낭비된다.
- 여러 개의 프로세스를 동시에 바꿔주며 실행시키면 사용자 입장에서 여러 개의 작업이 동시에 실행되는 것처럼 보이게 된다.
- CPU Scheduling
    - CPU를 어떤 프로세스에 할당시킬지 스케줄러에 의해서 결정된다.
    - CPU의 개수는 한정적이기 때문에 모든 프로세스에 하나씩 할당할 수 없기 때문에 좋은 효율을 내기 위해 효율적인 스케줄러가 필요하다.

## Two Seperate mode of operations

- user mode, kernel mode 두 가지가 존재한다.
- 커널 모드에서만 하드웨어 직접 제어를 할 수 있다. (메모리 접근 등)
- 사용자가 만든 애플리케이션이 올바르지 않은 비정상적인 접근을 하여 시스템을 크래쉬할 수 있는 것을 방지할 수 있다.
- 유저 프로세스는 실행되다가 OS에 `System Call`을 요청하여 커널 모드에서 작업을 처리한 후에 유저 모드로 돌아간다.

<img width="706" alt="two-operations" src="https://user-images.githubusercontent.com/50051656/187084251-bd20d41c-f4d7-4b01-8470-1e75037923ac.png">

## Virtualization

- 멀티프로그래밍하여 멀티 프로세스를 하나의 메모리에 여러 개를 올리고 멀티 프로세싱을 하여 CPU가 time sharing 하면서 여러 개를 동시에 실행시킬 수 있게 한다.
- 이때 운영체제도 여러 개를 올려서 실행시킬 수 있지 않을까 해서 나온 개념이다.
- VMM(Virtual Machine Manager)
    - CPU 스케줄링처럼 하드웨어와 OS 사이에서 자원을 관리해주는 역할
- ex) VMware, XEN, WSL

<img width="602" alt="virtualization" src="https://user-images.githubusercontent.com/50051656/187084610-2e74f250-8a2b-4920-aa08-068c1a09638c.png">

## Computing Environment

- Tranditional Computing
- Mobile Computing
- Client-Server computing

<img width="481" alt="client-server" src="https://user-images.githubusercontent.com/50051656/187084832-15c33f3c-f4ec-49ae-8639-cdfae36f6e0e.png">

- Peer-To-Peer Computing

<img width="313" alt="peer-to-peer" src="https://user-images.githubusercontent.com/50051656/187084830-abe6974c-c7f9-4947-9f45-d57e6fd267e3.png">

- Cloud Computing

<img width="577" alt="cloud" src="https://user-images.githubusercontent.com/50051656/187084839-c317307a-5326-453d-a8ef-b265a1497300.png">

- Real-Time Embedded Systems

## OS Services

- 프로그램이 실행될 환경을 제공해준다.
    - User Interface
    - Program execution
    - I/O operation
    - File-system manipulation
    - Communications
    - Error detection
    - Resource allocation
    - Logging
    - Protection and Security

<img width="939" alt="operating-system-services" src="https://user-images.githubusercontent.com/50051656/187085055-10f8daaa-977b-4675-8bbb-42361c68e9a1.png">

## User and Operating-System Interface

- User가 OS와 의사소통하는 방법
    - CLI(Command Line Interface) : shell(sh, bash, csh, zsh etc)
    - GUI(Graphical User Interface) : Windows
    - Touch-Screen Interface : AOS, IOS
- Program가 OS와 의사소통하는 방법
    - System Calls : OS API