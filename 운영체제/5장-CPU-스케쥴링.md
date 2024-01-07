## [CPU 스케쥴링 #1](https://core.ewha.ac.kr/publicview/C0101020140328151311578473?vmode=f)

### CPU and I/O Bursts in Program Execution

![img](/img/CPU%20and%20IO%20Bursts%20in%20Program%20Execution.png)

### CPU-burst Time의 분포

![img](/img/CPU_burst%20Time의%20분포.png)

- 여러 종류의 job(=process)이 섞여 있기 때문에 CPU 스케줄링이 필요하다
    - Interactive job에게 적절한 response 제공 요망
    - CPU와 I/O 장치 등 시스템 자원을 골고루 효율적으로 사용

### 프로세스의 특성 분류

- 프로세스는 그 특성에 따라 다음 두 가지로 나눔
    - I/O-bound process
        - CPU를 잡고 계산하는 시간보다 I/O에 많은 시간이 필요한 job
        - ( many short CPU bursts )
    - CPU-bound process
        - 계산 위주의 job
        - ( few very long CPU bursts )

### CPU Scheduler & Dispatcher

- CPU Scheduler
    - Ready 상태의 프로세스 중에서 이번에 CPU를 줄 프로세스를 고른다

- Dispatcher
    - CPU의 제어권을 CPU scheduler에 의해 선택된 프로세스에게 넘긴다
    - 이 과정을 context switch(문맥 교환)라고 한다

- CPU 스케줄링이 필요한 경우는 프로세스에게 다음과 같은 상태 변화가 있는 경우
    1. Running -> Blocked (예 : I/O 요청하는 시스템 콜)
    2. Running -> Ready (예 : 할당시간만료로 timer interrupt)
    3. Blocked -> Ready (예 : I/O 완료후 인터럽트)
    4. Terminate

> 1, 4에서의 스케줄링은 (비선점형) nonpreemptive(=강제로 빼앗지 않고 자진 반납)

> All other scheduling is (선점형) preemptive(=강제로 빼앗음)

### 스케쥴링 성능 척도

- 이용률(CPU utilization)
  - 전체 시간 중 CPU가 일하는 시간
- 처리량(Throughput)
  - 시간 단위별 CPU가 최대로 처리하는 양
- 소요 시간(Turnaround time)
  - CPU가 들어와서 작업 후 나갈 때까지 걸린 시간
- 대기 시간(Waiting time)
  - ready queue에서 CPU를 쓰기위해 기다리는 시간
- 응답 시간(Response time)
  - ready queue에 들어와서 처음 CPU를 얻기까지 걸리는 시간

> 대기 시간과 응답 시간의 차이 : 비선점형에서는 CPU를 얻고 뺏고를 반복함. 기다리는 시간을 다 합친 시간이 Waiting time, 처음 CPU를 얻는 시간이 Response time

## [CPU 스케쥴링 #2](https://core.ewha.ac.kr/publicview/C0101020140401134252676046?vmode=f)

### 스케쥴링 알고리즘 (복수개)

- FCFS (First-Come First-Served)
  - Example
    | Process | Burst Time |
    | ------- | ---------- |
    |    P1   |     24     |
    |    P2   |     3      |
    |    P3   |     3      |
  - 프로세스 도착 순서가 P1, P2, P3 순이면 Waiting time은 P1 = 0, P2 = 24, P3 = 27, 평균 대기시간은 (0 + 24 + 27) / 3 = 17
  - 프로세스 도착 순서가 P2, P3, P1 순이면 Waiting time은 P1 = 6, P2 = 0, P3 = 3, 평균 대기시간은 (6 + 0 + 3) / 3 = 3
  > Convoy effect -> short process behind long process

- SJF (Shortest-Job-First)
  - 각 프로세스의 다음번 CPU burst time을 가지고 스케줄링에 활용
  - CPU burst time이 가장 짧은 프로세스를 제일 먼저 스케줄
  - Two schemes
    - Nonpreemptive : 일단 CPU를 잡으면 이번 CPU burst가 완료될 때까지 CPU를 선점(preemption) 당하지 않음
    - Preemptive : 현재 수행중인 프로세스의 남은 burst time보다 짧은 CPU burst time을 가지는 새로운 프로세스가 도착하면 CPU를 빼앗김 (Shortest-Remaining-Time-First : SRTF라고도 부름)
  - SJF is optimal
    - 주어진 프로세스들에 대해 minimum average waiting time을 보장
  - SJF Example

    ![img](/img/SJF-Nonpreemptive.png)

    ![img](/img/SJF-Preemptive.png)

  - SJF의 문제점
    - Starvation : 극단적으로 burst time이 길면 영원히 이용할 수 없음
      - 100초 짜리는 중간에 1초 및 5초와 같은 CPU가 들어오면 계속 대기
    - CPU 사용량을 미리 알 수 없음
      - 다음번 CPU burst time은 추정(estimate)만 가능 -> 과거의 데이터 이용

- Priority Scheduling
  - 각 프로세스에 정수값으로 주어진 우선순위 숫자 주어짐
  - highest priority를 가진 프로세스에게 CPU 할당
  - SJF는 일종의 priority scheduling
    - priority = predicted next CPU burst time

- RR (Round Robin)
  - 각 프로세스는 동일한 크기와 할당 시간(time quantum)을 가짐 (일반적으로 10-100 milliseconds)
  - 할당 시간이 지나면 프로세스는 선점(preempted)당하고 ready queue의 제일 뒤에 가서 다시 줄을 선다
  - n개의 프로세스가 ready queue에 있고 할당 시간이 q time unit인 경우 각 프로세스는 최대 q time unit단위로 CPU 시간의 1/n을 얻는다 (어떤 프로세스도 (n-1)q time unit 이상 기다리지 않는다)
  - Performance
    - q large -> FCFS
    - q small -> context switch 오버헤드가 커진다
  > 일반적으로 SJF보다 average turnaround time이 길지만 response time은 더 짧다

### 스케쥴링 알고리즘 평가 

-