---
layout: single
title: 프로세스와 스레드의 라이프사이클
categories: [CS]
tag: [OS]
toc: true
---


📘 프로세스와 스레드

[https://giwon512.github.io/cs/process/](https://giwon512.github.io/cs/process/)

# 📖 프로세스의 라이프 사이클

1. 생성 - 메모리로 프로그램이 올라가서 실행 준비
2. 준비 - 큐에서 실행을 기다리는 상태
3. 수행 
4. sleep, block - 특정 조건이 해결될 때까지 작업이 중지된 상태
5. 종료

# 📖 프로세스의 상태 변화 과정

![processLifecycle.png]({{"../../../../assets/images/CS/lifecycle/processLifecycle.png" | relative_url}})

## 1. a

프로세스의 생성은 운영체제의 시스템 콜을 통해서만 가능하다. fork() 시스템 콜을 통해 부모 프로세스를 복사하고, 그 후 exec() 시스템 콜을 통해 새로운 프로그램을 메모리에 올린다.

### 관련 시스템 콜

📝 **fork() system call**

- 부모 프로세스에게는 양수 pid를, 자식 프로세스는 0을 리턴해주는 함수로, 부모인지 자식인지 구별하는 방법은 0인지 아닌지 확인하는 방법밖에 없다. 코드 진행 상황까지 그대로 복사된다.

📝 **exec() system call**

- fork로 복제된 시스템 콜을 새로운 프로세스로 덮어 버리는 시스템 콜이다.

## 2. c

가져온 사진이 이상해 c부터 본다. 준비 상태에 있는 프로세스들을 ready queue에서 대기하다가 정해진 알고리즘에 따라서 cpu time을 할당받는다.

### 스케줄링 알고리즘

📝 **FCFS(First Come First Served)**

- 말 그대로 먼저 요청한 프로세스부터 자원을 할당받는다.
- 비선점형이므로 대화형 작업에 부적합하다.
- 경우에 따라 대기시간이 길어질 수 있다.

📝 **SJF(Shrotest Job First)**

- 실행시간이 짧은 프로세스부터 자원을 할당받는다.
- 선점형, 비선점형 모두 가능하다.
- 평균 대기시간이 가장 짧지만 **starvation이 발생**할 수 있다.
- **사실상 실행 시간을 예측할 수 없어** 사용하기 어렵다.

📝 **RR(Round-Robin)**

- 작은 단위의 시간 할당량을 주어 시간 안에 끝내지 못한 경우 다음 프로세스에게 차례가 넘어가는 알고리즘
- 시간 할당이 너무 길면 FCFS가 되고, 너무 짧으면 context switching 비용이 증가한다.
- 따로 하드웨어적 타이머가 필요하다.

![MLFQ.png]({{"../../../../assets/images/CS/lifecycle/MLFQ.png" | relative_url}})

📝 **MLFQ(Multi Level Feedback Queue)**

- 라운드 로빈과 유사하지만 단점을 어느 정도 개선한 알고리즘
- 우선순위가 다른 여러 단계의 ready queue를 만들고, 우선순위가 높을 수록 시간 할당량인 time quantum을 짧게, 낮을수록 time quantum을 길게 준다.
- 제 시간 안에 마치지 못한 프로세스는 그 다음 단계의 queue로 이동하여 대기한다.
- 최하위 우선 순위 큐의 경우에는 그냥 FCFS로 처리한다.

## 3. b

수행 상태에서 ready queue로 프로세스가 돌아간다.

## 4. d

입출력 요청이 발생하면 프로세스가 sleep 상태로 들어간다.

### 관련 시스템 콜

📝 **wait() system call**

- wait() 시스템 콜이 호출되면 자식 프로세스가 종료될 때까지 해당 프로세스를 block 시킨다.

## 5. e

입출력 요청이 끝나 프로세스가 다시 준비 상태가 된다.

## 6. f

작업이 성공적으로 완료되거나 에러가 발생하여 종료된 경우이다.

### 관련 시스템 콜

📝 **exit() system call**

- 프로세스를 종료하는 시스템 콜로, 컴파일러가 main 함수가 return되는 위치에 자동으로 exit() 시스템 콜을 넣어준다.

# 📖 스레드의 라이프 사이클

![threadLifecycle.png]({{"../../../../assets/images/CS/lifecycle/threadLifecycle.png" | relative_url}})

## 1. 스레드 생성

## 2. Runnable

- start() 메서드를 통해 실행 가능한 상태로 바뀐다.

## 3. Running

- start() 메서드가 호출된 이후, 정의된 run() 메서드의 내용이 수행된다.
- run() 메서드는 JVM만이 호출 가능하다.
- yeild() 메서드는 스레드의 실행을 일시 정지시키고, 다른 스레드에게 실행을 양보한다. 멀티스레드 프로그래밍에서 사용될 수 있다.

## 4. Terminated

일정한 조건이 충족되기 전까지 스레드의 실행이 block되는 상태를 말한다.

### Wating

- wait()은 동기화를 위해서 사용되는 메서드로, 실행이 중지되었다가 notify() 또는 notifyAll() 메서드가 호출되면 waiting 상태였던 스레드들이 다시 동작하게 된다.

### Timed Wating

- sleep() 함수는 지정된 시간 만큼 스레드를 block시키고, 시간이 지나면 runnable 상태로 바꾼다.

### Blocked

- Join() 메서드는 다른 스레드가 종료될 때까지 동작을 멈추게 한다.

## 5. 종료

- run() 메서드 완료시 스레드가 종료되고, **해당 스레드는 다시 시작할 수 없다**. 다시 start() 메서드를 실행하면 `IllegalThreadStateException` 이 발생한다.

참고

---

[https://link2me.tistory.com/1730](https://link2me.tistory.com/1730)

[https://vmilsh.tistory.com/374](https://vmilsh.tistory.com/374)

[https://velog.io/@sunaookamisiroko/4.-프로세스-생성](https://velog.io/@sunaookamisiroko/4.-%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4-%EC%83%9D%EC%84%B1)

[https://reakwon.tistory.com/132](https://reakwon.tistory.com/132)

[https://velog.io/@infoqoch/CPU-스케줄링이란](https://velog.io/@infoqoch/CPU-%EC%8A%A4%EC%BC%80%EC%A4%84%EB%A7%81%EC%9D%B4%EB%9E%80)