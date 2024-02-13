### Paging

- Paging이란 프로세스가 할당받은 메모리 공간을 동일한 페이지 단위로 나누어 연속되지 않는 서로 다른 위치에 저장하는 물리적 메모리 관리 기법
  - 주소 바인딩 위해 모든 프로세스가 각각의 주소 변환을 위한 page table 가짐

### Paging Example

![img](/img/Paging%20Example.png)

### Address Translation Architecture

![img](/img/Address%20Translation%20Architecture.png)

### Implementation of Page Table

- Page table은 main memory에 상주
- Page-table base register (PTBR)가 page table을 가리킴
- Page-table length register (PTLR)가 테이블 크기를 보관
- 모든 메모리 접근 연산에는 2번의 memory access 필요
- page table 접근 1번, 실제 data/instruction 접근 1번
- 속도 향상을 위해 associative register 혹은 translation look-aside buffer (TLB)라 불리는 고속의 lookup hardware cache 사용

### Paging Hardware with TLB

![img](/img/Paging%20Hardware%20with%20TLB.png)

### Two-Level Page Table

- Address-Translation Scheme
  - 현대의 컴퓨터는 address space가 매우 큰 프로그램 지원
    - 32 bit address 사용시: 2의32승 B(4GB)의 주소 공간 필요
      - page size가 4K시 1M 개의 page table entry 필요
      - 각 page entry가 4B시 프로세스당 4M의 page table 필요
      - 그러나, 대부분의 프로그램은 4G의 주소 공간 중 지극히 일부분만 사용하므로 page table 공간이 심하게 낭비됨

  -> page table 자체를 page로 구성

  -> 사용되지 않는 주소 공간에 대한 outer page table의 엔트리 값은 NULL (대응하는 inner page table이 없음)

- Two-Level Paging Example
  - logical address (on 32-bit machine with 4K page size)의 구성
    - 20 bit의 page number
    - 12 bit의 page offset
  - page table 자체가 page로 구성되기 때문에 page number는 다음과 같이 나뉜다 (각 page table entry가 4B)
    - 10-bit의 page number
    - 10-bit의 page offset

### Multilevel Paging and Performance

- Address space가 더 커지면 다단계 페이지 테이블 필요
- 각 단계의 페이지 테이블이 메모리에 존재하므로 logical address의 physical address 변환에 더 많은 메모리 접근 필요
- TLB를 통해 메모리 접근 시간을 줄일 수 있음
- 4단계 페이지 테이블을 사용하는 경우
  - 메모리 접근 시간이 100ns, TLB 접근 시간이 20ns (nano seconds)
  - TLB hit ratio가 98%인 경우
    - effective memory access time = 0.98 x 120 + 0.02 x 520 = 128 ns
    - 결과적으로 주소변환을 위해 28ns만 소요

### Valid (v)/ Invalid (i) Bit in a Page Table

![img](/img/Valid,%20InValid%20bit%20in%20a%20page%20table.png)

### Memory Protection

- Page table의 각 entry 마다 아래의 bit를 둔다
  - Protection bit
    - page에 대한 접근 권한 (read/write/read-only)

  - Valid-invalid bit
    - "valid"는 해당 주소의 frame에 그 프로세스를 구성하는 유요한 내용이 있음을 뜻함 (접근 허용)
    - "invalid"는 해당 주소의 frame에 유효한 내용이 없음을 뜻함 (접근 불허)

### Inverted Page Table

- page table이 매우 큰 이유
  - 모든 process 별로 그 logical address에 대응하는 모든 page에 대해 page table entry가 존재
  - 대응하는 page가 메모리에 있든 아니든 간에 page table에는 entry로 존재

- inverted page table
  - Page frame 하나당 page table에 하나의 entry를 둔 것 (system-wide)
  - 각 page table entry는 각각의 물리적 메모리의 page frame이 담고 있는 내용 표시 (process-id, process의 logical address)
  - 단점
    - 테이블 전체를 탐색해야 함
  - 조치
    - associative register 사용 (expensive)

- Inverted Page Table Architecture

  ![img](/img/Inverted%20Page%20Table%20Architecture.png)

### Shared Page

- Shared code
  - Re-entrant Code (=Pure code)
  - read-only로 하여 프로세스 간에 하나의 code만 메모리에 올림 (eg, text editors, compilers, window systems)
  - Shared code는 모든 프로세스의 logical address space에서 동일한 위치에 있어야 함

- Private code and data
  - 각 프로세스들을 독자적으로 메모리에 올림
  - Private data는 logical address space의 아무 곳에 와도 무방

- Shared Pages Example

  ![img](/img/Shared%20Pages%20Example.png)