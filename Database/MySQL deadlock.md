# MySQL deadlock

- save를 반복하는 요청을 계속 보내보고 데드락 발생하는지 보기 -> 원인 파악
- 설정한 인덱스와 데드락이 관련있는지 보기
- mysql 5.7의 기본 락이 어떤지 보기, 기본 락 설정범위가 어떤지 보기
- `com.mysql.cj.jdbc.exceptions.MySQLTransactionRollbackException: Deadlock found when trying to get lock; try restarting transaction` 이 예외가 어떤 상황에서 발생하는지 보기
- S lock
  - Shared Lock = 공유 락 = Read Lock
- X lock
  - eXclusive Lock = 배타 락 = Write Lock

