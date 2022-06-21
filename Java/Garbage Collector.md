- JVM(Java Virtual Machine)

  - 운영체제의 메모리 영역에 접근하여 메모리를 관리하는 프로그램
  - 메모리 관리, Garbage Collector 수행

- 스택

  - 정적으로 할당된 메모리 영역
  - 원시 타입의 데이터가 값과 함께 할당됨
  - 힙 영역에 생성된 Object 타입의 데이터의 참조 값이 할당됨
  - 1 스택 메모리 - 1 쓰레드

- 힙

  - 동적으로 할당된 메모리 영역
  - 모든 Object 타입의 데이터가 할당됨

- Reachability

  - Reachable Object는?
    - 스택영역에 있는 데이터의 참조를 받는 객체
    - 메서드영역에 있는 데이터의 참조를 받는 객체
    - 힙 영역의 다른 객체의 참조를 받는 객체

- Garbage Collector

  - 동적으로 할당한 메모리 영역(힙) 중 사용하지 않은 영역을 탐지하여 해제하는 기능
  - 과정(Mark and Sweep)
    1. 스택의 모든 변수를 스캔하면서 각각 어떤 객체를 참조하고 있는지 찾아서 마킹한다(Mark)
    2. reachable object가 참조하고 있는 객체도 찾아서 마킹한다.(Mark)
    3. 마킹되지 않은 객체를 힙에서 제거한다.(Sweep)

- Garbage Collection은 언제 일어날까?

   - 새로운 객체는 Eden에 할당
    - Eden영역의 메모리가 다 사용되었으면 Minor GC 발생
        - Eden영역에서 Mark and Sweep이 일어남
        - Eden영역의 Reachable 객체는 Survival 0 또는 1 로 옮겨지고, unreachable 객체는 메모리에서 해제됨
            - Survival 0과 Survival 1 둘 중 하나만 차있어야 한다
        - 살아남은 객체는 age값 1추가
        - 반복
    - Survival 0에 있던 메모리들이 Survival 1로 이동하면서 이동한 객체는 Age값 증가
    - Survival 1의 메모리가 다 사용되었으면 Survival 0으로 이동, Age값 증가
    - 특정 Age가 넘는 객체들은 Old Generation으로 옮겨진다(Promotion)
    - Old Generation이 꽉차면 Major GC 발생

- 그럼 왜 Minor GC와 Major GC를 나눌까?

  - 대부분의 객체는 금방 unreachable 상태가 된다. 즉, 금방 garbage가 된다.
  - 오래된 객체에서 젊은 객체로의 참조는 아주 적게 존재한다.

- Garbage Collector 종류

  1. Serial GC

     - GC를 처리하는 스레드가 1개

     - CPU코어가 1개만 있을때 사용하는 방식

     - Mark-Compact collection 알고리즘 사용

  2. Parallel GC

     - GC를 처리하는 쓰레드가 여러개

     - java8의 기본 GC

     - young 영역에 한해서 멀티쓰레드로 수행

     - stop-the-world시간 감소

     - Serial GC보다 빠르게 객체를 처리할 수 있다

     - 메모리가 충분하고 코어의 개수가 많을 때 사용하면 좋다

  3. Parallel Old GC

     - Parallel GC를 개선

     - Old 영역에서도 멀티쓰레드 방식의 GC수행

     - Mark-Summary-Compact 알고리즘 사용
       - sweep : 단일쓰레드가 old영역 전체를 훑는다
       - summary : 멀티쓰레드가 old 영역을 분리해서 훑는다

  4. Concurrent Mark Sweep GC(CMS GC)

     - Stop-The-World 시간이 짧다

     - 응답시간이 중요한 애플리케이션에서 사용

     - 다른 GC방식보다 메모리와 CPU를 더 많이 사용한다

     - Compaction 단계가 제공되지 않는다
     - Stop-The-World란?
       - GC를 실행하기 위해 JVM이 애플리케이션 실행을 멈추는 것
       - stop-the-world가 발생하면 GC를 실행하는 쓰레드를 제외한 나머지 스레드는 모두 작업을 멈춘다
       - GC작업을 완료한 이후에 중단한 작업을 다시 실행한다.'

     - Concurrent Mark에선 Initial Mark에서 마킹된 객체를 탐색하면서 계속 마킹된다. 다른 애플리케이션 스레드와 동시에 실행됨

     - 애플리케이션이 생성되면서 갱신되는 객체가 있을거니까 Remark에서 재검토

     - Concurrent Sweep에서 메모리 해제

  5. G1 GC(Garbage First)

     - CMS GC를 개선

     - java 9+의 기본 GC

     - 힙 영역을 일정한 크기의 Region 으로 나눈다

     - 전체 힙이 아닌 Region 단위로 탐색

     - compact 진행

     - GC가 일어날때 전체 영역(Eden, Survival, Old Generation)을 탐색하지 않는다
