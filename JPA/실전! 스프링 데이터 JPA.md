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

