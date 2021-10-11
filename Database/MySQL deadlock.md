# MySQL deadlock

- save를 반복하는 요청을 계속 보내보고 데드락 발생하는지 보기 -> 원인 파악
  - 현재 notification MySQL 버전 : 5.7.12-log
  - 도커로 5.7.12 버전 연결해서 하면 데드락 안걸림
  - `SHOW ENGINE INNODB STATUS;` 하면 락 정보 알수 있는 것 같은데 권한이 없음 (https://mysqldba.tistory.com/54 이 링크에 내역분석하는법 있음)
  - `show variables like 'tx_isolation';` -> 현재 격리레벨은 repeatable-read.. 바꿔야 할까?
- 설정한 인덱스와 데드락이 관련있는지 보기
- mysql 5.7의 기본 락이 어떤지 보기, 기본 락 설정범위가 어떤지 보기
- `com.mysql.cj.jdbc.exceptions.MySQLTransactionRollbackException: Deadlock found when trying to get lock; try restarting transaction` 이 예외가 어떤 상황에서 발생하는지 보기
- S lock
  - Shared Lock = 공유 락 = Read Lock
- X lock
  - eXclusive Lock = 배타 락 = Write Lock



- InnoDB의 락 종류
  1. row-level lock
     - row 단위 락.
     - S lock과 X lock이 있음
       1. S lock
          - Shared Lock = 공유 락 = Read Lock
       2. X lock
          - eXclusive Lock = 배타 락 = Write Lock
     - 락을 거는 규칙
       - 여러 트랜잭션이 동시에 한 row에 S lock을 걸 수 있다
       - S lock상태에서 다른 트랜잭션이 X lock을 시도할 수 없다
       - X lock이 걸려있는 row에는 다른 트랜잭션이 S,X lock을 걸 수 없다
  2. record lock
     - row가 아니라 index record에 걸리는 락
     - s lock과 x lock이 존재함
     - 예
       - 트랜잭션 A : `select id, name from customer where id=10 for update;`  <- x lock 획득
       - 트랜잭션 B : id=10인 레코드를 read, update, delete 시도  <- 트랜잭션 A가 commit하거나 rollback할때까지 대기
  3. gap lock
     - index record의 gap에 걸리는 락 (gap: index중 실제 record가 없는 부분)
     - 예
       - customer의 id컬럼에 1,5,10는 존재, 2,3,4,6,7,8,9는 없을때
       - 트랜잭션 A : `select id from sales where id between 1 and 10 for update;` <- 2,3,4,6,7,8,9에 gap lock 걸림
       - 트랜잭션 B : 2,3,4,6,7,8,9중 하나로 insert 시도 <- 트랜잭션 A가 commit하거나 rollback대기

