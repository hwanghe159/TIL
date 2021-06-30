# 실전! 스프링 데이터 JPA

### 공통 인터페이스 설정

- 원래는 설정코드에 `@EnableJpaRepositories(basePackages = "repository의위치")` 붙여야 하는데 스프링 데이터 JPA를 사용하면 생략 가능
- repository는 인터페이스인데 어떻게 기능을 수행하는가?
  - `repository.getClass()`를 출력해보면 `com.sun.proxy.$Proxy107` 이런게 나옴
  - 인터페이스를 보고 스프링 데이터 JPA가 구현 클래스를 만들어서 꽂아버린 것
  - 스프링 데이터 JPA가 애플리케이션 로딩 시점에 repository들의 구현 클래스를 스스로 만든다.
    - `org.springframework.data.repository.Repository`를 구현한 클래스가 스캔 대상.
  - 컴포넌트 스캔을 스프링 데이터 JPA가 자동으로 처리하기 때문에 `@Repository` 생략 가능

### JpaRepository 뜯어보기

```java
package org.springframework.data.jpa.repository;

public interface JpaRepository<T, ID> extends PagingAndSortingRepository<T, ID>, QueryByExampleExecutor<T> {
  ...
}
```

```java
package org.springframework.data.repository;

public interface PagingAndSortingRepository<T, ID> extends CrudRepository<T, ID> {
  ...
}
```

```java
package org.springframework.data.repository;

public interface CrudRepository<T, ID> extends Repository<T, ID> {
  //기본적인 CRUD
}
```

```java
package org.springframework.data.repository;

public interface Repository<T, ID> {
  //마커 인터페이스
}
```

- `JpaRepository`는 `org.springframework.data.jpa.repository` 안에있음 (스프링데이터JPA 프로젝트)
- `PagingAndSortingRepository`, `CrudRepository`, `Repository`는 `org.springframework.data.repository` 안에 있음 (페이징, 소팅 등등은 어떤 DB든 공통이므로)

### 쿼리 메서드

쿼리 메서드 기능 3가지

1. 메소드 이름으로 쿼리 생성

   - https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.query-methods.query-creation
   - find...By, read...By, query...By,  get...By
     - By는 필수, By뒤에 아무것도 없으면 전체조회
   - count...By, exists...By, delete...By, remove...By
   - findDistinct, findMemberDistinctBy
   - findFirst3, findFirst, findTop, findTop3

2. 메소드 이름으로 JPA NamedQuery 호출

   - ```java
     @Entity
     @NamedQuery(
     	name="abc",
       query="select m from Member m where m.username = :username"
     )
     public class Member {
       ...
     }
     ```

     ```java
     public interface MemberRepository extends JpaRepository<Member, Long> {
       @Query(name = "abc")
       List<Member> findByUsername(@Param("username") String username);
     }
     ```

   - 가장 큰 장점은 문법 오류를 애플리케이션 로딩 시점에 잡아낼 수 있음

   - 실무에서 쓰일 일 없음

3. `@Query` 어노테이션을 사용해서 리파지토리 인터페이스에 쿼리 직접 정의

   - ```java
     @Query("select m from Member m where m.username= :username and m.age = :age")
     List<Member> findUser(@Param("username") String username, @Param("age") int age);
     ```

   - 메서드 이름이 길어지는 걸 막을 수 있음

   - 문법 오류시엔 애플리케이션 로딩 시에 잡아낼 수 있음(이름없는 NamedQuery라 보면 됨)

   - @Query에서 정의한 쿼리는 정적 쿼리니까 애플리케이션 로딩 시점에 파싱해서 sql을 다 만들어놓음

   - 권장하는 기능

   - 메서드 이름으로 메서드 만들땐 간단한거 주로 하고, 좀 복잡해지면 이 방법을 사용

   - 값 조회

     - ```java
       @Query("select m.username from Member m")
       List<String> findUsernameList();
       ```

   - DTO로 직접 조회

     - ```java
       @Query("select new study.datajpa.repository.MemberDto(m.id, m.username, t.name) from Member m join m.team t")
       List<MemberDto> findMemberDto();
       ```

     - `new`와 패키지 정보를 다 적어줘야 한다.

### 파라미터 바인딩

- 이름기반과 위치기반이 있는데 이름기반을 쓰자 (가독성, 유지보수성 측면에서)

- 컬렉션 타입으로 in절 지원

  ```java
  @Query("select m from Member m where m.username = :names")
  List<Member> findByNames(@Param("names") List<String> names);
  ```

### 반환타입

- 반환타입으로 컬렉션, 단건, Optional 모두 가능 (그 외에도 void, primitive, 래퍼타입, T, Iterator, Stream, Future, Page 등..)
- 컬렉션
  - 조회 결과가 없으면 null이 아니라 빈 컬렉션 반환해줌
- 단건
  - 조회 결과가 없으면 null 반환함
  - 순수 JPA는 예외가 터지지만, 스프링 데이터 JPA는 이 예외를 감싸서 null을 반환함
  - 결과가 있을지 모르는 경우는 비추
- Optional
  - 결과가 2개 이상인 경우 예외 발생 (스프링이`NonUniqueResultException`를 잡아서  `IncorrectResultSizeDataAccessException` 터뜨림)

### 페이징

- 페이징과 정렬 파라미터
  - `Sort` -> 정렬 기능
  - `Pageable` -> 페이징 기능
  
- 특별한 반환타입
  - `Page` -> total count가 필요할때 씀. total count쿼리를 따로 날림
    - `getTotalElements()`: 전체 개수, `getNumber()`: 페이지 번호, `getTotalPages()`: 전체 페이지 개수, `isFirst()`: 첫번째 페이지인지, `hasNext()`: 다음 페이지 있는지
  - `Slice` -> total count가 필요없을때 (모바일에서 더보기)
    - 3개 조회요청이라면 4개를 불러옴 -> 다음에 더 있는지만 알 수 있다.
  - `List` -> 추가 count쿼리 없이 결과만 반환
  
- `Page<Member>` -> `Page<MemberDto>` 변환가능

  - ```java
    Page<Member> page = ...;
    Page<MemberDto> dto = page.map(member -> MemberDto.of(member));
    ```

- 카운트 쿼리는 무겁다.

  - 데이터는 left join, 카운트 쿼리는 left join 안해도 됨.
  - `@Query(value = "...", countQuery = "...")`

### 벌크성 수정 쿼리

예를 들어 "20살 이상의 모든 회원의 나이를 +1해라" 라는 명령 같은 경우에는 한명한명 수정해서 더티체킹을 이용하는 것보단 한번에 update쿼리로 하는게 효율적이다. 이를 벌크성 수정 쿼리라고 한다

```java
// @Modifying을 붙여줘야 getResultList()나 getSingleResult()가 아닌 executeUpdate()가 실행되어 오류가 발생하지 않음
// int를 반환해야함 (update가 적용된 row 개수)
@Modifying(clearAutomatically = true)
@Query("update Member m set m.age = m.age + 1 where m.age >= :age")
int bulkAgePlus(@Param("age") int age);
```

- 벌크 연산을 쓰면서 조심해야 할 점!
  - 벌크 연산을 하면 영속성 컨텍스트를 무시하고 DB에 때려버림. 영속성 컨텍스트를 그걸 모름.
  - 벌크 연산 후 api가 끝나서 트랜잭션이 끝나면 상관없지만 트랜잭션이 끝나기 전에 다른 조회가 발생하면 큰일남
  - 그래서 벌크 연산 후엔 영속성 컨텍스트를 다 날려버려야 함
    - `em.clear()` 하거나 `@Modifying(clearAutomatically = true)` 해야함

### `@EntityGraph`

- 들어가기 전.. N+1문제
  - Member가 Team을 객체참조하고 있음

  - ```java
    List<Member> allMembers = memberRepository.findAll();
    for (Member member : allMembers) {
      System.out.println("member.team : " + member.getTeam().getName()); //N+1문제 발생
    }
    ```

  - 해결하려면 fetch join

    ```java
    @Query("select m from Member m left join fetch m.team")
    List<Member> findAllMemberFetchJoin();
    ```

- `@EntityGraph` 이용해서도 해결 가능

  ```java
  @Override
  @EntityGraph(attributePaths = {"team"})
  List<Member> findAll();
  ```

- `@EntityGraph`와 JPQL 섞어서 쓸수도 있음

  ```java
  @EntityGraph(attributePaths = {"team"})
  @Query("select m from Member m")
  List<Member> findMemberEntityGraph();
  ```

- 메서드 네이밍과 `@EntityGraph` 함께 이용 가능

  ```java
  @EntityGraph(attributePaths = {"team"})
  List<Member> findEntityGraphByUsername(@Param("username") String username); //find뒤엔 아무거나 올 수 있음
  ```

영한님 의견 : 간단할땐 `@EntityGraph`쓰고 복잡할땐 JPQL씀

### JPA 힌트

- SQL 힌트가 아니고 JPA 구현체에게 제공하는 힌트
- 쓰기는 못하게 하고 읽기만 가능하게 해서 더티체킹에 대한 리소스를 줄여 최적화할 수 있다
- 그런데 큰 성능향상은 기대하기 어렵다. 성능에 가장 많은 영향을 주는 부분을 최적화하는게 더 효율적임

```java
@QueryHints(value = @QueryHint(name = "org.hibernate.readOnly", value = "true"))
Member findReadOnlyByUsername(String username);
```

### JPA Lock

- JPA에서 락에 대한 어노테이션을 제공함

```java
@Lock(LockModeType.PESSIMISTIC_WRITE) //옵션이 많음.
List<Member> findLockByUsername(String username);
```

영한님 의견 : 실시간 트래픽이 많은 서비스는 가급적이면 락을 많이 걸면 안됨

### 사용자 정의 리파지토리 구현

- 스프링 데이터 JPA가 제공하는 인터페이스를 직접 구현하면 구현해야 하는 기능이 너무 많음

- 다양한 이유로 인터페이스의 메서드를 직접 구현하고 싶다면?

  - JPA 직접 사용(EntityManager)
  - 스프링 JDBC Template 사용
  - MyBatis 사용
  - 데이터베이스 커넥션 직접 사용
  - Querydsl 사용할때 등등..

- 구현하는 법

  - 인터페이스를 따로 만들고(MemberRepositoryCustom), 그 인터페이스를 구현하는 클래스(MemberRepositoryCustomImpl)를 만들고 기능을 구현한다

  - 만든 인터페이스를 기존의 레포지터리가 상속받는다

    ```java
    public interface MemberRepository extends JpaRepository<Member, Long>, MemberRepositoryCustom {
      ...
    }
    ```

  - 자바에서 되는 건 아니고 spring data JPA가 이렇게 동작하도록 해주는 것



