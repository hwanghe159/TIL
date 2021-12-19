## 쓰레드 풀

![img](https://t1.daumcdn.net/cfile/tistory/231B374B595F67F43A)

- 순서
  - 어플리케이션이 뜰때 쓰레드를 미리 만들어놓음
  - 사용자로부터 들어온 요청을 작업 큐에 넣음
  - 작업큐에 들어온 task들을 쓰레드들에게 할당
  - 일을 처리한 스레드는 결과값 리턴
- 쓰는 이유
  - 요청때마다 쓰레드를 생성/수거한다면 퍼포먼스 저하가 일어난다
  - 다수의 요청을 처리하기 위해
- 단점
  - 쓰레드를 너무 많이 만들어놓으면 메모리 낭비다 (이를 방지하기 위해 `ForkJoinPool`이 있음)
- 생성
  - 초기 쓰레드 수 : 처음 기본적으로 생성되는 수
  - 코어 쓰레드 수 : 최소 쓰레드 수
  - 최대 쓰레드 수
  - `newCachedThreadPool()`, `newFixedThreadPool(int nThreads)`
- 종료
  - `executorService.shutdown();` : 작업큐에 남아있는 작업까지 모두 마무리 후 종료
  - `executorService.shutdownNow();` : 작업큐 작업 잔량 상관없이 강제 종료
  - `excutorService.awaitTermination(long timeout, TimeUnit unit);` : 모든 작업 처리를 timeout 시간안에 처리하면 true 리턴 ,처리하지 못하면 작업스레드들을 `interrupt()`시키고 false리턴
- 작업
  - `execute();` : 결과 반환X, 예외발생시 쓰레드 종료 -> 쓰레드풀에서 제거됨, 다른 작업을 위해 새 쓰레드 생성
  - `submit();` : 결과 반환O, 예외발생시 쓰레드 종료X 재사용

