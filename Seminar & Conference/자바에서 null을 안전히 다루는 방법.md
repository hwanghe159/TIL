- 발표 영상 : https://youtu.be/vX3yY_36Sk4

## null에 대해서

- "레코드 핸들링"이라는 객체지향의 시초가 된 논문에서 null 탄생
- 특별한 값이 없음을 나타내려고 null 도입
- 자바에서의 null참조
  - 의미가 모호함.(초기화되지 않음? 정의되지 않음? 값이 없음? null값?)
  - 모든 참조의 기본 상태
  - 모든 참조는 null 가능
- 소프트웨어 결함 통계 : Native crash 1등, NullPointer 2등

## null을 안전하게 다루기 위한 자바 기본 장치

### 단정문(assertion)

```java
assert 식1;
assert 식1 : 식2;
```

- bool식인 식1이 거짓이면 AssertionError 발생
- 식2는 AssertionError에 포함될 상세 정보를 만드는 생성식
- 공개 메서드에는 사용하지 말아야 함
  - 스스로가 소비자이면서 생산자일때 사용 가능.
  - 소비자가 누구일지 모르면 쓰면 안됨
- -enableassertions 또는 -ea 옵션으로 활성화 (런타임엔 무시됨. 기본이 disable상태임)

### `java.util.Objects`

자바8

- `isNull(Object obj)`
- `nonNull(Object obj)`
- `requireNonNull(T obj)`
- `requireNonNull(T obj, String message)`
- `requireNonNull(T obj, Supplier<String> messageSupplier)`

자바9

- `requireNonNullElse(T obj, T defaultObj)`
- `requireNonNullElseGet(T obj, Supplier<? extends T> supplier)`

### `java.util.Optional`

Optional은 만들다 말았다!

- Serializable이 안돼서 필드에 쓸 수 없다
- `isPresent()`나 `get()`같은 메서드는 만들면 안됐다

Optional Rules

1. 절대로 Optional변수와 **반환값에 null을 사용하지 말라**
2. Optional에 값이 들어있다는 걸 확신하지 않는 한 Optional.get()을 쓰지 말라
3. Optional.isPresent()나 Optional.get() 외 API를 가능한 사용하라
4. Optional에서 여러 메서드를 연속해서 호출하고 값을 얻기 위해서 Optional을 생성하는 건 권장하지 않는다
5. Optional로 값을 처리하는 중에 그 안에 중간값을 처리하기 위해 또 다른 Optional이 사용되면 너무 복잡해진다
6. Optional을 **필드, 메서드 매개변수, 집합 자료형에 쓰지 말라**
7. 집합 자료형(List, Set, Map)을 감싸는데 Optional을 쓰지 말고 빈 집합을 사용하라

## null 잘 쓰는 법

1. API(매개변수, 반환값)에 null을 최대한 쓰지 말아라

   - null로 지나치게 유연한 메서드를 만들지 말고 명시적인 메서드를 만들어라

     (`null이 들어올땐 이렇게 작동하고 안들어올땐 저렇게 작동하는` 메서드를 만들지 말라)

   - null을 반환하지 말라

     - 반환값이 꼭 있어야 한다면 null을 반환하지 말고 예외를 던져라
     - 빈 반환값은 빈 컬렉션이나 Null객체를 활용하라
     - 반환값이 없을 수도 있다면 Optional을 반환하라

   - 선택적 매개변수는 null대신 다형성(오버로딩)을 사용해서 표현하라

2. 사전 조건과 사후 조건을 확인하라: 계약에 의한 설계(design by contract)

   - API 소비자와 제공자 사이의 지켜야 할 규약을 지켜야 함. 계약으로 여기는 설계 방법
   - 형식적 규약 외에 사전 조건과 사후 조건과 유지 조건을 포함
   - 개방폐쇄원칙의 상위개념
   - 간단히 말하면, null이 들어오면 안되는데 null이 들어왔을때 확인하라는 것.
   - 자바에서 사용할 수 있는 것들
     - java doc, 단정문, 스프링 Assert클래스, AssertJ Preconditions클래스 등..

3. null의 범위를 지역(클래스, 메서드)에 제한하라

   - 기본 문제 해결 원칙: 큰 문제는 제어 가능한 작은 문제로 나누어 정복하고 다시 통합한다
   - 상태와 비슷하게 null도 지역적으로 제한할 경우 큰 문제가 안된다
   - 클래스와 메서드를 작게 만들어라
   - 설계가 잘 된 코드에서는 null의 위험도 약해진다

4. 초기화를 명확히 하라

   - 초기화 시점과 실행 시점이 겹치지 않아야 한다
   - 실행 시점엔 초기화되지 않은 필드가 없어야 한다
   - 실행 시점에 null인 필드는 초기화되지 않았다는 의미가 아닌 값이 없다는 의미여야 한다
   - 객체 필드의 생명주기는 모두 객체의 생명주기와 같아야 한다
   - 지연 초기화 필드의 경우 팩토리 메서드로 null처리를 캡슐화 하라

## null 안정성을 도와주는 자바 도구

- CheckerFramework

  - JSR-308 타입 어노테이션의 결과물

  - @Nullable과 @NonNull

    - 기본적으로 @NonNull이고 필요할때만 @Nullable을 붙여줌

  - 패키지나 클래스 수준에서 기본 정책을 설정할 수 있음

    ```java
    @DefaultQualifier(value = NonNull.class, locations = TypeUseLocation.LOCAL_VARIABLE)
    package com.my.package;
    ```

    ```java
    @DefaultQualifier(value = NonNull.class, locations = TypeUseLocation.FIELD)
    class MyClass {
      private String nullableField = null;
      @NonNull private String nunNullField;
    }
    ```



