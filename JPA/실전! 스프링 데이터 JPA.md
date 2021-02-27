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

- JpaRepository는 org.springframework.data.jpa.repository 안에있음 (스프링데이터JPA 프로젝트)
- PagingAndSortingRepository는 org.springframework.data.repository 안에 있음 (페이징, 소팅 등등은 어떤 DB든 공통이므로)

