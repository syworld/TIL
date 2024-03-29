# 운영체제 개요 


### **OS란**

컴퓨터 하드웨어 위에 설치되어서 사용자 또는 다른 모든 소프트웨어, 하드웨어를 연결하는 소프트웨어 계층이다. 

- 좁은 의미의 OS: 커널 (메모리에 상주)
- 넓은 의미의 OS: 커널 + 각종 시스템 유틸리티


### **OS의 목적**

자원을 효율적으로 관리한다.

- 자원?
    - 하드웨어 자원: CPU, memeory, 입출력 장치
    - 소프트웨어 자원: 프로세스, 파일, 메시지


### **OS의 분류**

- 동시 작업 가능 여부: single tasking vs multi tasking
- 사용자의 수: single user vs multi user
- 처리 방식
    - batch processing
    - time sharing (interactive한 방식) - 범용 컴퓨터에 사용됨
    - realtime os - 특수 목적 컴퓨터에 사용됨 ex) 미사일 제어


### 용어 정리

- Multitasking : CPU는 매순간은 하나의 작업만 수행하고 있는데, 매우 짧은 시간에 스케줄링을 하므로 마치 동시에 여러 작업을 실행하는 것 처럼 보일 수 있음.
- Multiprogramming: 여러 프로그램이 **메모리**에 올라가 있음을 강조함
- Time sharing: CPU의 시간을 분할하여 나누어 쓴다는 것을 강조함
- Multiprocessor 다중처리기: 하나의 컴퓨터에 여러 개의 CPU(processor)가 붙어 있음. (여러 프로그램이 각기 다른 CPU에서 실행 될 수 있음)


### OS의 기능

CPU ↔ Memory ↔ Disk, I/O device 

- CPU: 어떤 프로그램한테 CPU를 할당할 지 (CPU 스케줄링)
- Memory: 한정된 메모리를 어떻게 분배할 지
- Disk: 디스크에 파일을 어떻게 보관할 지. 디스크도 스케줄링이 필요함 (디스크 헤드의 이동을 최소화하면서 최대한 많은 일을 처리할 수 있을 지)
- I/O device:  느린 입출력장치와 빠른 컴퓨터 간에 어떻게 정보를 주고 받을 지. 프로그램 실행하려고 하는데 I/O 작업이 필요한 경우 CPU가 I/O에 요청하고 다른 작업을 수행한다. I/O에서 CPU가 요청한 작업이 끝나면 I/O에서 CPU에 인터럽트를 발생시킴.
- 프로세스 관리: 프로세스 생성 및 삭제. 자원 할당 및 반환
- CLI 등
