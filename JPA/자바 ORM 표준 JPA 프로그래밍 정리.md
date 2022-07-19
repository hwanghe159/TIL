# 자바 ORM 표준 JPA 프로그래밍 정리

## 1장 : JPA 소개

### SQL을 직접 다룰 때 발생하는 문제점

- 객체지향 애플리케이션과 데이터베이스 중간에서 CRUD쿼리를 계속 일일이 변환해주어야 한다.
- 변경사항이 생겼을 때 쿼리문을 다 찾아서 수정해주어야 한다.
- 진정한 의미의 계층 분할이 어렵다
  - Dao의 세부 구현을 다 알아야 한다. 이러면 진정한 의미의 계층 분할이라고 볼 수 없다
- 엔티티를 신뢰할 수 없다
  - SQL과 객체 간 분리가 일어나면 SQL도 직접 들어가서 확인해야됨
  - 객체에서도 `.`찍고 들어가는 것에 대해서 신뢰할 수 없음
- SQL에 의존적인 개발을 피하기 어렵다.

### 패러다임의 불일치

객체와 관계형 데이터베이스는 지향하는 목적이 애초에 달라서 둘의 기능과 표현 방법이 다르다.

애플리케이션은 객체지향 언어로 개발하고, 데이터는 관계형 데이터베이스에 저장해야 한다면, 그 사이의 패러다임의 불일치 문제를 개발자가 중간에서 해결해야 했다.

#### 패러다임의 불일치로 인해 발생하는 문제들

1. 객체는 상속이 있지만 테이블은 상속이 없다.

   슈퍼타입과 서브타입을 사용하여 비슷하게 구현할 수 있지만 저장할때와 조회할 때 매우 복잡하다.

2. 객체는 참조, 테이블은 외래키를 사용하여 연관관계를 가진다.

   개발자가 중간에서 변환 역할을 해야 한다. 그럼 결국엔 관계형 데이터베이스의 방식으로 엔티티를 구성하게 되고, 결국 객체지향의 특징을 잃어버리게 된다.

3. 객체 그래프 탐색을 마음대로 할 수 없다.

   SQL을 직접 다루면 처음 실행하는 SQL에 따라 객체 그래프를 어디까지 탐색할 수 있을지 정해진다. 처음 실행하는 SQL이 다 가져오지 않으면 탐색하다가 NullPointerException이 발생할 수 있다.

4. 같은 키로 조회해도 동일성 비교에 실패한다.

### JPA란 무엇인가?

JPA : 자바 진영의 ORM 기술 표준. 애플리케이션과 JDBC 사이에서 동작한다.

ORM : 객체와 관계형 데이터베이스를 매핑하는것. 

ORM 프레임워크 : 객체와 테이블을 매핑해서 패러다임의 불일치 문제를 개발자 대신 해결해준다. (ex: 하이버네이트)

#### JPA를 사용해야 하는 이유

1. 반복적인 코드와 CRUD용 SQL을 직접 작성하지 않아도 되고, 객체 설계 중심으로 개발할 수 있어 **생산성**이 좋다.

2. 필드를 추가하는 등 변경이 일어나도 필드를 사용하는 기존 코드를 수정할 필요가 없어 **유지보수**에 좋다.

3. **패러다임의 불일치를 해결**한다.

4. 다양한 **성능**최적화 기능을 제공한다.

5. **데이터 접근 추상화**와 **벤더 독립성**

   JPA는 애플리케이션이 특정 데이터베이스 기술에 종속되지 않도록 한다. 

6. 자바 진영의 ORM 기술 **표준**이다.

<br>

## 3장 : 영속성 관리

### 엔티티 매니저 팩토리

- 엔티티 매니저를 만드는 공장

- 공장을 만드는 비용이 매우 커서 앱당 한개만 만들어서 공유

- 여러 스레드가 동시에 접근해도 안전하다.

- `EntityManagerFactory emf = Persistence.createEntityManagerFactory("jpabook")`

  하면 `META-INF/persistence.xml`에서 이름이 jpabook인 `persistence-unit`을 찾아 공장을 생성한다.

### 엔티티 매니저

- 말그대로 엔티티 관리자. 엔티티를 저장하고, 수정하고, 삭제하고, 조회한다.
- 여러 스레드가 동시에 접근하면 동시성 문제가 발생하므로 스레드 간에 절대 공유하면 안된다.
- 데이터베이스 연결이 꼭 필요한 시점까지 커넥션을 얻지 않는다. 보통 트랜잭션을 시작할 때 커넥션을 획득한다.
- `EntityManager em = emf.createEntityManager();`

### 영속성 컨텍스트

- 엔티티를 영구 저장하는 환경
- `em.persist(member)`는 엔티티 매니저를 사용하여 엔티티를 영속성 컨텍스트에 저장하고 관리한다.
- 영속성 컨텍스트는 엔티티 매니저를 생성할때 하나 만들어진다.
- 엔티티 매니저를 통해 영속성 컨텍스트에 접근할 수 있고, 영속성 컨텍스트를 관리할 수 있다.

### 엔티티의 생명주기

- 비영속 (new/transient) : 엔티티를 생성했지만 영속성 컨텍스트에 저장하지 않은 상태
- 영속 (managed) : 영속성 컨텍스트에 저장하여 관리받는 상태
- 준영속 (detached) : 영속 상태에서`detach()`,`close()`,`clear()`등을 사용하여 관리받지 않게 된 상태
- 삭제 (removed) : `remove()`를 사용하여 영속성 컨텍스트와 데이터베이스에서 삭제된 상태

### 영속성 컨텍스트의 특징

- 영속성 컨텍스트는 엔티티를 식별자로 구분하기 때문에 식별자 값이 반드시 있어야 한다.
- 트랜잭션을 커밋하는 순간 영속성 컨텍스트에 새로 저장된 엔티티를 데이터베이스에 반영한다. (= 플러시)
- 1차 캐시, 동일성 보장, 트랜잭션을 지원하는 쓰기지연, 변경 감지, 지연 로딩 등등..

#### 1차 캐시

- 영속성 컨텍스트 내부의 캐시(키는 `@Id`로 매핑한 식별자, 값은 엔티티 인스턴스인 Map)
- `em.persist(member)` 하면 먼저 1차 캐시에 엔티티를 저장하고, DB엔 아직 저장하지 않는다.
- `em.find(Member.class, "member1");` 같이 조회하면, 1차 캐시에서 먼저 찾고, 없으면 DB를 조회한다.
- DB를 조회해서 엔티티를 생성했다면 1차 캐시에 저장한 후 엔티티(영속상태)를 반환한다.
- Map을 이용하니까 동일성을 보장한다.

#### 트랜잭션을 지원하는 쓰기지연

- 엔티티 매니저는 트랜잭션을 커밋하기 직전까지 DB에 엔티티를 저장하지 않고 내부 쿼리 저장소에 쿼리를 차곡차곡 모아둔다.
- 모아뒀다가 트랜잭션을 커밋할때 쿼리들을 DB에 보낸다.

#### 변경 감지(dirty checking)

- 스냅샷 : 엔티티를 영속성 컨텍스트에 저장할때, 최초 상태를 복사해서 저장해둔다.
- 플러시 시점에 스냅샷과 엔티티를 비교해서 변경된 엔티티가 있으면 수정쿼리를 쓰기지연 저장소에 보낸다.
- 쓰기지연 저장소의 쿼리들을 DB에 보내고 트랜잭션을 커밋한다.
- 변경 감지는 영속 상태의 엔티티에만 적용된다.

### 플러시

플러시 : 영속성 컨텍스트의 변경 내용을 DB에 반영(동기화)한다.

1. 플러시 실행
2. 변경 감지가 동작해서 스냅샷을 이용하여 수정된 엔티티를 찾는다.
3. 수정된 엔티티는 수정 쿼리를 만들어 쓰기지연 SQL저장소에 등록한다.
4. 쿼리들을 DB에 전송한다.

#### 플러시하는 방법

1. `em.flush()` 직접호출

   강제로 플러시함

2. 트랜잭션 커밋 시 플러시 자동 호출

   변경 내용을 SQL로 전달하지 않고 커밋하면 DB에 반영되지 않기 때문에 커밋 전 자동으로 플러시된다.

3. JPQL 쿼리 실행 시 플러시 자동 호출

### 준영속

준영속 상태의 엔티티는 영속성 컨텍스트가 제공하는 기능을 사용할 수 없다.

#### 준영속 상태로 만드는 방법

1. `em.detach(entity)`

   특정 엔티티만 준영속 상태로 만든다. 

   1차 캐시와 쓰기지연 SQL저장소에서 해당 엔티티를 관리하기 위한 모든 정보가 제거된다.

2. `em.clear()`

   영속성 컨텍스트를 초기화한다.

   해당 영속성 컨텍스트의 모든 엔티티를 준영속 상태로 만든다. (1차캐시와 쓰기지연SQL저장소가 모두 비워진다.)

   지우고 새로 만든것과 같다.

3. `em.close()`

   영속성 컨텍스트를 종료한다.

#### 준영속 상태의 특징

- 거의 비영속 상태에 가깝다.
- 식별자 값을 갖고 있다.
- 지연 로딩을 할 수 없다.

#### 병합

`merge(entity)` : 준영속 상태의 엔티티를 받아서 영속 상태의 엔티티를 반환한다.

`Member mergeMember = em.merge(member);`에서 member가 준영속 -> 영속이 되는게 아니고,

영속상태인 mergeMember가 반환된다.

1. merge(entity)를 실행한다.
2. 파라미터로 넘어온 준영속 엔티티의 식별자로 1차캐시를 조회한다.
3. 1차캐시에 없으면 DB조회 후 1차캐시에 저장한다.
4. 조회한 영속 엔티티에 파라미터로 넘어온 준영속 엔티티의 모든 정보를 밀어 넣고 반환한다.

<br>

## 4장 : 엔티티 매핑

매핑 어노테이션을 크게 4가지로 분류할 수 있다.

- 객체와 테이블 매핑 : `@Entity`, `@Table`
- 기본 키 매핑 : `@Id`
- 필드와 컬럼 매핑 : `@Column`
- 연관관계 매핑 : `@ManyToOne`, `@JoinColumn`

### @Entity

테이블과 매핑할 클래스에 필수로 붙여야 한다. 

- 기본 생성자는 필수다.(파라미터 없는 public이나 protected 생성자)
  - JPA가 기본 생성자를 사용하여 엔티티 객체를 생성하기 때문
- final클래스, enum, interface, inner클래스에는 사용할 수 없다.
- 저장할 필드에 final을 사용하면 안된다.

### @Table

엔티티와 매핑할 테이블을 지정한다. 생략하면 매핑한 엔티티 이름을 테이블 이름으로 사용한다.

### 데이터베이스 스키마 자동생성

JPA는 매핑 정보를 보고 DB스키마를 자동으로 생성해준다.

`persistence.xml`에 다음 속성을 추가하면 된다.

`<property name="hibernate.hbm2ddl.auto" value="create" />`

> application.properties 방식으로는
>
> ```properties
> spring.jpa.hibernate.ddl-auto=create
> ```

스키마 자동생성 기능을 운영서버에선 사용하지 말자! 초반 학습 차원에서만 사용하기

 `hibernate.hbm2ddl.auto`의 속성은 다음과 같다.

| 옵션             | 설명                                                         |
| ---------------- | ------------------------------------------------------------ |
| create           | 기존 테이블을 삭제하고 새로 생성. DROP+CREATE                |
| create-drop      | DROP+CREATE+DROP                                             |
| update           | DB 테이블과 엔티티 매핑 정보를 비교해서 변경 사항만 수정한다. |
| validate         | DB 테이블과 엔티티 매핑 정보를 비교해서 차이가 있으면 경고를 남기고 실행하지 않음. |
| 속성 자체를 삭제 | 자동생성 기능 사용안함                                       |

운영 서버에서 절대 사용하면 안됨. 개발서버나 개발단계에서 사용 : create, create-drop, update

개발 초기 단계 : create, update

초기화 상태로 자동화된 테스트 진행 :  create, create-drop

테스트 서버 : update, validate

스테이징과 운영서버 : validate, 또는 사용안함

### DDL 생성 기능

- 회원 이름이 필수고, 10자를 초과하면 안된다면?

  ```java
  @Column(nullable = false, length = 10)
  private String username;
  ```

  위와 같이 수정하고 생성된 DDL을 보면

  ```sql
  create table MEMBER (
      ...
  	USERNAME varchar(10) not null
      ...
  )
  ```

- JPA는 위처럼 애플리케이션 실행 동작에는 영향을 주지 않지만, 자동 생성되는 DDL을 위한 기능들이 있다.

### @Id

기본 키를 직접 할당하려면 `@Id`만 붙이면 되고,

자동 생성 하려면 `@GeneratedValue`도 함께 붙여준다.

- `IDENTITY` 전략

  - `@GeneratedValue(strategy = GenerationType.IDENTITY)`만 붙이면 됨

  - 기본 키 생성을 DB에 위임한다.

  - 트랜잭션을 지원하는 쓰기 지연이 동작하지 않는다.

- `SEQUENCE` 전략

  - 데이터베이스 시퀀스(유일한 값을 순서대로 생성하는 특별한 DB 오브젝트)를 사용해서 기본키를 할당한다.

  - ```java
    @Entity
    @SequenceGenerator(
    	name = "BOARD_SEQ_GENERATOR",
    	sequenceName = "BOARD_SEQ",
    	initialValue = 1, allocationSize = 1)
    public class Board {
        @Id
    	@GeneratedValue(strategy = GenerationType.SEQUENCE,
                       generator = "BOARD_SEQ_GENERATOR")
        private Long id;
        ...
    }
    ```

  - IDENTITY 전략은 `엔티티를 DB에 저장 -> 식별자 조회 -> 엔티티의 식별자에 할당` 이었다면,

    SEQUENCE 전략은 `DB시퀀스를 사용해서 식별자 조회 -> 엔티티에 할당 -> 영속성 컨텍스트에 저장`이다.

- `TABLE` 전략

  - 키 생성 전용 테이블을 하나 만들고 이름과 값으로 사용할 컬럼을 만들어서 시퀀스를 흉내내는 전략이다.

  - ```java
    @Entity
    @TableGenerator(
      name = "BOARD_SEQ_GENERATOR",
      table = "MY_SEQUENCES",
      pkColumnValue = "BOARD_SEQ", allocationSize = 1)
    public class Board {
      @Id
      @GeneratedValue(strategy = GenerationType.TABLE, generator = "BOARD_SEQ_GENERATOR")
      private Long id;
        ...
    }
    ```
  
- AUTO 전략

  - `@GeneratedValue.strategy`의 기본값이다.
  - 선택한 DB방언에 따라 전략을 자동으로 선택한다.
  - `@GeneratedValue(strategy = GenerationType.IDENTITY)`라고 붙이거나 아니면 `@GeneratedValue`만 붙여도 된다.

### 필드와 컬럼 매핑

- `@Column` : 컬럼 매핑

  생략해도 매핑은 되지만 기본타입일땐 not null 제약조건이 붙고, 객체 타입일땐 not null이 안붙는다.

- `@Enumerated` : enum타입 매핑

  웬만하면 `@Enumerated(EnumType.STRING)`으로 사용한다.  

- `@Temporal` : 날짜 타입 매핑

- `@Lob` : 데이터베이스의 BLOB, CLOB 타입과 매핑

- `@Transient` : 매핑하고 싶지 않은 필드에 붙임

- `@Access` : 엔티티 데이터에 접근하는 방식 지정

<br>

## 5장 : 연관관계 매핑 기초

객체와 테이블을 관계를 맺는 방법이 다르다 : 객체는 참조, 테이블은 외래키

그러므로 객체의 참조와 테이블의 외래키를 매핑해야 한다.

연관관계 매핑을 이해하기 위한 핵심 키워드 : **방향**, **다중성**, **연관관계의 주인**

### 단방향 연관관계

다대일, 단방향인 `Member`와 `Team`클래스를 보자.

```java
@Entity
public class Member {
    
    @Id
    @Column(name = "MEMBER_ID")
    private String id;
    
    private String username;
    
    @ManyToOne //1.
    @JoinColumn(name = "TEAM_ID") //2.
    private Team team;
    
    ...
}
```

```java
@Entity
public class Team {
    
    @Id
    @Column(name = "TEAM_ID")
    private String id;
    
    private String name;
    
    ...
}
```

1. `@ManyToOne`

   - 회원과 팀은 다대일 관계.
   - 연관관계를 매핑할때 다중성을 나타내는 어노테이션은 필수다.

2. `@JoinColumn(name = "TEAM_ID")`

   - 외래키를 매핑할때 `@JoinColumn`을 사용한다.

   - 외래키 이름으로 `TEAM_ID`라고 설정한 것.

   - 생략할 수 있지만, 생략할 경우 외래키 이름으로 `team_TEAM_ID`를 사용한다.

     (기본전략 : 필드명 + 밑줄 + 참조하는 테이블의 칼럼명)

### 연관관계 사용

#### 저장

주의! **엔티티를 저장할 때, 연관된 모든 엔티티는 영속 상태여야 한다.**

```java
Team team1 = new Team("team1", "팀1");
em.persist(team1);

Member member1 = new Member("member1", "회원1");
member1.setTeam(team1);
em.persist(member1);
```

- `team1`이 영속상태여야 `member`1이 제대로 저장된다.

#### 조회

연관관계가 있는 엔티티를 조회하는 방법은 객체 그래프 탐색과 JPQL사용이 있다.

1. 객체 그래프 탐색 -> 8장
2. JPQL(객체지향쿼리) 사용 -> 10장

#### 수정

불러온 엔티티 값만 변경하면 트랜잭션을 커밋할때 플러시가 일어나면서 변경 감지 기능이 작동한다.

#### 삭제

연관된 엔티티를 삭제하려면 다음과 같이 연관관계를 먼저 제거해야 한다.

```java
Member member1 = em.find(Member.class, "member1");
member1.setTeam(null); //연관관계 제거
```

연관관계를 제거한 후 엔티티를 삭제할 수 있다.

```java
member1.setTeam(null);
member2.setTeam(null);
em.remove(team); //얘만 하려고 하면 오류 발생
```

### 양방향 연관관계

다대일, 양방향인 `Member`와 `Team`클래스를 보자.

`Member`엔티티에는 변함이 없고 `Team`엔티티엔 `members` 필드가 추가되어 양방향이 되었다.

```java
@Entity
public class Member {
    
    @Id
    @Column(name = "MEMBER_ID")
    private String id;
    
    private String username;
    
    @ManyToOne
    @JoinColumn(name = "TEAM_ID")
    private Team team;
    
    ...
}
```

```java
@Entity
public class Team {
    
    @Id
    @Column(name = "TEAM_ID")
    private String id;
    
    private String name;
    
    @OneToMany(mappedBy = "team") //1.
    private List<Member> members = new ArrayList<Member>();
    
    ...
}
```

1. `@OneToMany(mappedBy = "team")`

   일대다 관계를 매핑한다. 

   `mappedBy = "team"`는 연관관계의 주인을 `Member.team`으로 정한다는 뜻이다.

### 연관관계의 주인

사실 객체에는 양방향 연관관계라는 게 없다. 단방향 2개를 양방향인 것처럼 보이게 한 것 뿐이다.

그에반해 테이블은 외래키 하나만으로 양방향 연관관계를 맺는다.

엔티티를 단방향으로 매핑하면 참조를 하나만 사용하므로 이 참조로 외래키를 관리하면 되지만

양방향으로 매핑하면 두 참조 중 하나를 선택해서 외래키를 관리시켜야 한다.

선택된 외래키 관리자를 연관관계의 주인이라고 하며, 

연관관계의 주인만 외래키를 관리(등록, 수정, 삭제)를 할 수 있고, 주인이 아닌쪽은 읽기만 가능하다.

- 연관관계의 주인은 테이블에 외래키가 있는 곳이다. (다대일 관계에서 다 쪽)
- 주인이 아닌 곳은 `mappedBy`속성을 사용하여 연관관계의 주인을 지정해야 한다.

### 양방향 연관관계 저장

저장하는 법은 단방향일때와 완전히 같다.

```java
Team team1 = new Team("team1", "팀1");
em.persist(team1);

Member member1 = new Member("member1", "회원1");
member1.setTeam(team1);
em.persist(member1);
```

- `team1.getMembers().add(member2);`와 같이 연관관계의 주인이 아닌 곳에서 입력된 값은 무시된다.

### 양방향 연관관계의 주의점

- 연관관계의 주인이 아닌 곳에만 값을 저장하고, 주인인 곳엔 저장을 안한다면 정상적으로 저장되지 않는다.
- 그럼 주인이 아닌 곳엔 저장을 안해도 되는가?
- JPA를 사용할 땐 문제없지만, JPA를 사용하지 않는다면 버그가 발생할 수 있다. 그래서 객체 관점에선 양쪽에 모두 값을 입력해주는게 안전하다.
- 그럼 매번 양쪽 관계를 맺어줘야 한다. 이때 편의를 위해 연관관계 편의 메서드를 사용한다.
- 연관관계 편의 메서드를 구현할 때, 기존 연관관계가 있다면 기존 관계를 끊는 코드를 추가해야 한다.

<br>

## 6장 : 다양한 연관관계 매핑

모두 왼쪽을 연관관계의 주인으로 설정했다. 예를 들어 다대일 양방향이면 다쪽이 연관관계의 주인이다.

### 다대일

회원(N)과 팀(1)을 이용해 다대일 관계를 매핑한다. (회원을 연관관계의 주인이라고 가정)

회원과 팀의 테이블은 단방향이던, 양방향이던 둘이 똑같다. (외래키는 항상 다쪽에 존재한다.)

1. 다대일 단방향 ( 다(주인) -> 일 )

   - 회원과 팀은 다대일 관계.

   - `Member.team`는 존재하지만 `Team.members`는 존재하지 않으므로, `Member -> Team`은 가능하지만 `Team -> Member` 불가능 (단방향)

   - `Member.team`에 다대일 표시 (`@ManyToOne`)

   - `Member.team`가 연관관계의 주인이니까 외래키(`TEAM_ID`)를 관리하기 위해 외래키와 매핑

     (`@JoinColumn(name = "TEAM_ID")`)

   - `@JoinColumn`은 생략할 수 있지만, 생략할 경우 외래키 이름으로 `team_TEAM_ID`를 사용한다.

2. 다대일 양방향 ( 다(주인) <-> 일 )

   - 회원과 팀은 다대일 관계.

   - `Member.team`와 `Team.members`둘 다 존재하므로, `Member -> Team`와 `Team -> Member` 둘 다 가능 (양방향)

   - `Member.team`에 다대일 표시 (`@ManyToOne`)

   - `Team.members`에 일대다 표시 (`@OneToMany`)

   - `Team.members`은 연관관계의 주인이 아니므로 `Member.team`의 거울일 뿐이라는 것을 표시.

     (`@OneToMany(mappedBy = "team")`)

   - `Member.team`가 연관관계의 주인이니까 외래키(`TEAM_ID`)를 관리하기 위해 외래키와 매핑

     (`@JoinColumn(name = "TEAM_ID")`)

   - 단방향일때와 마찬가지로 `@JoinColumn`를 생략해도 될까?

### 일대다

팀(1)과 회원(N)을 이용해 일대다 관계를 매핑한다.

팀을 연관관계의 주인이라고 가정한다. (하지만 일(1)쪽을 연관관계의 주인으로 설정하는건 비추천)

팀과 회원의 테이블은 단방향이던, 양방향이던 둘이 똑같다. (외래키는 항상 다쪽에 존재한다.)

1. 일대다 단방향

   - 팀(1)과 회원(N)은 일대다 관계.

   - `Team.members`는 존재하지만 `Member.team`는 존재하지 않으므로, `Team -> Member`은 가능하지만 `Member -> Team` 불가능 (단방향)

   - `Team.members`에 일대다 표시 (`@OneToMany`)

   - `Team.members`가 연관관계의 주인이니까 외래키(`TEAM_ID`)를 관리하기 위해 외래키와 매핑

     (`@JoinColumn(name = "TEAM_ID")`)

   - 하지만 외래키는 회원테이블쪽에, 연관관계의 주인은 팀 엔티티쪽에 있다.

     - 매핑한 객체가 관리하는 외래키가 다른 테이블에 있다는 것이 단점이다.
     - 본인 테이블에 외래키가 있으면 저장과 연관관계 처리를 INSERT로 한번에 처리할 수 있지만 이 상황에선 UPDATE를 추가로 실행해야 한다.
     - 그래서 일대다 단방향보단 다대일 양방향을 사용하자.

   - 일대다 단방향일땐 `@JoinColumn(name = "TEAM_ID")`을 생략하면 안된다.

     - 생략하면 연결 테이블을 중간에 두고 연관관계를 관리한다. (조인 테이블 전략이 기본이다.)

2. 일대다 양방향

   - 일대다 양방향일때, 일(1)쪽은 연관관계의 주인이 될 수 없다.
     - 양방향인 경우 `@ManyToOne`와 `@OneToMany` 둘 중 연관관계의 주인은 항상 `@ManyToOne`을 사용한 쪽(다 쪽)이기 때문이다.
   - 아예 불가능하진 않지만, 사용하지 말자. (다대일 양방향을 사용하자.)

### 일대일

일대다 관계에선 다 쪽에서 외래키를 가졌지만, 일대일 관계에선 둘 중 하나를 선택해야 한다.

회원(주 테이블)과 사물함(대상 테이블)을 이용해 일대일 관계를 매핑한다.

#### 주 테이블에 외래키

객체지향 개발자들이 선호한다.

1. 단방향

   - `Member.locker`에 연관관계, 외래키 매핑

     ```java
     @OneToOne
     @JoinColumn(name = "LOCKER_ID")
     private Locker locker;
     ```

2. 양방향

   - `Member.locker`에 연관관계, 외래키 매핑

     ```java
     @OneToOne
     @JoinColumn(name = "LOCKER_ID")
     private Locker locker;
     ```

   - `Locker.member`에 연관관계 매핑

     ```java
     @OneToOne(mappedBy = "locker")
     private Member member;
     ```

#### 대상 테이블에 외래키

1. 단방향

   `Member -> Locker` 방향인 경우는 지원하지 않는다.

   `Locker -> Member`방향으로 수정하거나 양방향으로 만들고 `Locker`을 연관관계의 주인으로 설정해야 한다. 

2. 양방향

   - `Member.locker`에 연관관계 매핑

     ```java
     @OneToOne(mappedBy = "member")
     private Locker locker;
     ```

   - `Locker.member`에 연관관계, 외래키 매핑

     ```java
     @OneToOne
     @JoinColumn(name = "MEMBER_ID")
     private Member member;
     ```

### 다대다

- 다대다 관계에선 두개의 테이블로 관계를 표현하지 못한다. 중간에 연결 테이블이 필요하다.
- 하지만 객체는 객체 2개로 다대다 관계를 만들 수 있다. `@ManyToMany`를 사용한다.
- 회원과 상품으로 다대다를 설명한다.

#### 다대다 단방향

- 회원과 상품은 다대다 관계

- `Member.products`는 존재하지만 `Product.members`는 존재하지 않음 (단방향)

- `Member.products`에 다대다 표시(`@ManyToMany`), 조인테이블 매핑(`@JoinTable`)

  ```java
  @ManyToMany
  @JoinTable(name = "MEMBER_PRODUCT", //1.
            joinColumns = @JoinColumn(name = "MEMBER_ID"), //2.
            inverseJoinColumns = @JoinColumn(name = "PRODUCT_ID")) //3.
  private List<Product> products = new ArrayList<Product>();
  ```

  1. name 속성으로 연결테이블의 이름을 설정한다.
  2. joinColumns 속성으로 현재 방향(회원)과 매핑할 조인 컬럼 정보를 지정한다.
  3. inverseJoinColumns 속성으로 반대 방향(상품)과 매핑할 조인 컬럼 정보를 지정한다.

- 저장할땐 상품테이블, 회원테이블, 연결테이블 모두 INSERT문이 실행된다.

- `member.getProducts()`을 실행하면 연결테이블과 상품테이블을 INNER JOIN해서 조회한다.

#### 다대다 양방향

- 회원과 상품 중 연관관계의 주인을 정한다. (여기선 회원이 주인)

- 정했으면, 연관관계의 주인이 아닌 곳에 `mappedBy` 속성을 사용한다.

- `Member.products`에 다대다 표시(`@ManyToMany`), 조인테이블 매핑(`@JoinTable`)

  ```java
  @ManyToMany
  @JoinTable(name = "MEMBER_PRODUCT",
            joinColumns = @JoinColumn(name = "MEMBER_ID"),
            inverseJoinColumns = @JoinColumn(name = "PRODUCT_ID"))
  private List<Product> products = new ArrayList<Product>();
  ```

  `Product.members`에 다대다 표시, 연관관계의 주인이 아니라고 표시

  ```java
  @ManyToMany(mappedBy = "products")
  private List<Member> members;
  ```

- 연관관계 편의 메서드를 사용해서 편하게 양방향 연관관계를 편하게 설정한다.

#### 연결 엔티티 사용

하지만 `@ManyToMany`를 사용하면 연결 테이블안에 추가 컬럼을 넣을 수 없다.

연결 테이블에 컬럼을 추가하고 싶으면 연결 테이블을 매핑하는 연결 엔티티를 만들어야 한다.

```java
@Entity
@IdClass(MemberProductId.class) //1.
public class MemberProduct {
    
    @Id
    @ManyToOne
    @JoinColumn(name = "MEMBER_ID") //2.
    private Member member;
    
    @Id
    @ManyToOne
    @JoinColumn(name = "PRODUCT_ID") //2.
    private Product product;
    
    //컬럼 추가 ..
    
}
```

1. `@IdClass`로 복합 기본키를 매핑
2. 기본키를 매핑하는 `@Id`와 외래키를 매핑하는 `@JoinColumn`을 동시에 사용해서 기본키와 외래키를 한번에 매핑한다.

복합 기본키를 위한 식별자 클래스의 특징

- 복합키는 별도의 식별자 클래스로 만들어야 한다.
- `public` 클래스여야 한다.
- `Serializable`을 구현해야 한다.
- 기본 생성자가 있어야 한다.
- `equals()`와 `hashCode()`를 구현해야 한다.
- `@IdClass`외에 `@EmbeddedId`를 사용하는 방법도 있다. -> 7.3절

회원상품을 저장하려면 회원과 상품을 저장한 후, 둘을 이용하여 회원상품을 저장해야 한다.

조회하려면 `MemberProduct memberProduct = em.find(MemberProduct.class, memberProductId);`와 같이 식별자 클래스의 객체로 조회해야 한다.

#### 복합키 사용하지 않기

복합키를 사용하지 않고 데이터베이스에서 자동으로 생성해주는 대리 키를 기본키로 사용하는걸 추천한다.

`MEMBER_ID`와 `PRODUCT_ID`를 복합키가 아닌 외래키로 사용하는 것이다.

```java
@Entity
public class Order { // MemberProduct -> Order
    
    @Id @GeneratedValue
    @Column(name = "ORDER_ID")
    private Long id;
    
    @ManyToOne
    @JoinColumn(name = "MEMBER_ID")
    private Member member;
    
    @ManyToOne
    @JoinColumn(name = "PRODUCT_ID")
    private Product product;
    
    //컬럼 추가 ..
    
}
```

