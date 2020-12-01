# [토크ON세미나] JPA 프로그래밍 기본기 다지기

유튜브 영상 링크 : https://www.youtube.com/playlist?list=PL9mhQYIlKEhfpMVndI23RwWTL9-VL-B7U

## 6강 JPA내부 구조

JPA는 연관관계 매핑과 영속성 컨텍스트 두가지를 이해하면 끝이다.

### 영속성 컨텍스트?

- JPA를 이해하는데 가장 중요한 용어
- "엔티티를 영구 저장하는 환경"이라는 뜻
- `EntityManager.persist(entity);`
- 엔티티 매니저를 통해서 영속성 컨텍스트에 접근한다.

### 엔티티의 생명 주기

- 비영속 (new/transient) : 영속성 컨텍스트와 전혀 관계가 없는 상태
  - 객체를 생성만 하고 JPA에 안넣었을때
- 영속 (managed) : 영속성 컨텍스트에 저장된 상태
  - 객체를 생성하고 JPA에 넣어 관리되는 상태
- 준영속 (detached) : 영속성 컨텍스트에 저장되었다가 분리된 상태
  - 준영속 상태로 만드는 법 -> `em.detach(entity)`, `em.clear()`, `em.close()`
- 삭제 (removed) : 삭제된 상태

### 영속성 컨텍스트의 이점

- 1차 캐시
  - 영속성 컨텍스트가 new해서 생성되고 없어질때까지만 잠깐 빤짝 존재하는 애. 내부안의 메모리 공간.
  - key가 id, value가 객체 자체
  - DB가기 전에 1차캐시를 먼저 뒤져서 있으면 바로 반환, 없으면 DB로 감
  - DB에서 구한 걸 캐시에 넣어주고 반환
  - 이 캐시는 글로벌이 아니라 서로 공유하지 않고, 트랜잭션이 시작하고 끝날떄까지만 유지하는 굉장히 짧은 캐시 레이어
- 동일성 보장
  - 1차 캐시를 사용하기 때문에 같은 id로 조회한 객체는 같다.
- 트랜잭션을 지원하는 쓰기 지연
  -  `persist(memberA);` 하고 `persist(memberB);`를 하면 DB에 바로 넣지 않고 1차 캐시에 넣어놓고, INSERT SQL을 만들어 놓는다. 커밋하는 시점에 만든 INSERT SQL를 DB에 한번에 보낸다.
- 변경 감지 (더티 체킹)
  - `memberA.setAge(10);`같이 내용을 바꿔도 다시 저장해줄 필요가 없다.
  - 사실은 1차 캐시를 넣을때 스냅샷이란 것도 존재한다.
  - 트랜잭션이 commit이나 flush되는 시점에 스냅삿과 비교해서 바뀐 게 있다면 update쿼리를 만들어서 DB로 보낸다.
  - 그럼 `update()`같이 메서드를 제공해서 업데이트를 해주게 하면 되지 헷갈리게 왜 자동으로 해주냐?? 그건 JPA의 컨셉과 관련있다. (자바 컬렉션의 객체를 다루듯이 하기위해)
- 지연 로딩
  - 지연로딩을 하려면 영속성 컨텍스트로 관리가 되어야 한다.
  - `LazyInitializationException`을 만나면 영속 상태가 아닌걸 지연 로딩하려고 했구나 라고 생각하면 됨.

### 참고

`flush()` : 영속성 컨텍스트의 변경 내용을 DB에 반영

`clear()` : 영속성 컨텍스트 안의 캐시를 비움

<br>

## 7강 JPA 객체지향쿼리

JPA는 다양한 쿼리 방법을 지원함 : JPQL, QueryDSL, JPA Criteria, 네이티브 SQL, JDBC API직접사용 ...

### JPQL 소개

- Java Persistence Query Language

- 가장 단순한 조회 방법

  - `EntityManager.find()`
  - 객체 그래프 탐색 (`a.getB().getC()`)

- JPA를 사용하면 엔티티 객체를 중심으로 개발한다. 문제는 검색 쿼리다.

- 검색을 할 때도 테이블이 아닌 엔티티 객체를 대상으로 검색한다고 생각해야 함.

- 모든 DB데이터를 객체로 변환해서 검색하는 것은 불가능.

- 애플리케이션이 필요한 데이터만 DB에서 불러오려면 결국 검색 조건이 포함된 SQL이 필요하다.

- JPQL은 SQL을 추상화한 객체지향쿼리다.

- 그러니까, 테이블을 대상으로 쿼리하는 게 아니라 엔티티 객체 대상으로 쿼리한다. 

- ```java
  String jpql = "select m From Member m where m.name like '%hello%'";
  List<Member> result = em.createQuery(jpql, Memner.class).getResultList();
  ```

- 특정 데이터베이스 SQL에 의존적이지 않다.

### JPQL 문법

- 나이가 18살 초과인 회원을 모두 검색하고 싶다면?

  ```java
  String jpql = "select m From Member m where m.age > 18";
  List<Member> result = em.createQuery(jpql, Member.class).getResultList();
  ```

- 엔티티와 속성은 대소문자 구분(Member, username)
- JPQL 키워드는 대소문자 구분 안함(SELECT, Select, Where, where)
- 테이블 이름이 아니라, 엔티티 이름을 사용한다.
- 별칭(m)은 필수로 사용해야 한다.

### 결과 조회 API

- query.getResultList() : 결과가 하나 이상, 리스트 반환
- query.getSingleResult() : 결과가 정확히 하나, 단일 객체 반환

### 프로젝션

- SELECT m FROM Member m -> 엔티티 프로젝션

- SELECT m.team FROM Member m -> 엔티티 프로젝션

- SELECT username, age FROM Member m -> 단순 값 프로젝션

- new 명령어 : 단순 값을 DTO로 바로 조회

  SELECT new jpabook.jpql.UserDTO(m.username, m.age) FROM Member m

- DISTINCT는 중복 제거

### 페이징 API

- JPA는 페이징을 다음 주 API로 추상화

  - `setFirstResult(int startPosition)` : 조회 시작 위히(0부터 시작)
  - `setMaxResults(int maxResult)` : 조회할 데이터 수

- ```java
  String jpql = "select m From Member m order by m.name desc";
  List<Member> resultList = em.createQuery(jpql, Member.class)
      .setFirstResult(10)
      .setMaxResults(20)
      .getResultList();
  ```



## 8강 Spring Data JPA와 QueryDSL 이해

JPA 기반 프로젝트로는 Spring Data JPA와 QueryDSL가 있다.

### Spring Data JPA

- 지루하게 반복되는 CRUD 문제를 세련된 방법으로 해결

- 개발자는 인터페이스만 작성

- Spring Data JPA가 구현 객체를 동적으로 생성해서 주입

- 메서드 이름으로 쿼리 생성

  ```java
  public interface MemberRepository extends JpaRepository<Member, Long> {
      List<Member> findByName(String name);
  }
  ```

- 이름으로 검색 + 정렬

  ```java
  public interface MemberRepository extends JpaRepository<Member, Long> {
      List<Member> findByName(String name, Sort sort);
  }
  ```

- 이름으로 검색 + 정렬 + 페이징

  ```java
  public interface MemberRepository extends JpaRepository<Member, Long> {
      List<Member> findByName(String name, Pageable pageable);
  }
  ```

  

### QueryDSL

- SQL, JPQL을 코드로 작성할 수 있도록 도와주는 빌더 API

- JPA 크리테리아에 비해서 편리하고 실용적임

- 오픈소스

- 장점은?

  - 문자가 아닌 코드로 작성
  - 컴파일 시점에 문법 오류 발견
  - 코드 자동완성(IDE의 도움을 받을 수 있음)
  - 단순하고 쉬움: 코드모양이 JPQL과 거의 비슷
  - 동적 쿼리

- 사용 예

  ```java
  //JPQL
  //select m from Member m where m.age > 18
  
  JPAFactoryQuery query = new JPAQueryFactory(em);
  QMember m = QMember.member;
  
  List<Member> list = query.selectFrom(m).where(m.age.gt(18))
      .orderBy(m.name.desc())
      .fetch();
  ```

