- 발표 영상 : https://youtu.be/vX3yY_36Sk4

## null에 대해서

- "레코드 핸들링"이라는 객체지향의 시초가 된 논문에서 null 탄생
- 특별한 값이 없음을 나타내려고 null 도입
- 자바에서의 null참조
  - 의미가 모호함.(초기화되지 않음? 정의되지 않음? 값이 없음? null값?)
  - 모든 참조의 기본 상태
  - 모든 참조는 null 가능
- 소프트웨어 결함 통계 : Native crash 1등, NullPointer 2등

## null을 안전하게 다루는 방법

### 자바 기본 장치

#### 단정문(assertion)

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

#### `java.util.Objects`

자바8

- `isNull(Object obj)`
- `nonNull(Object obj)`
- `requireNonNull(T obj)`
- `requireNonNull(T obj, String message)`
- `requireNonNull(T obj, Supplier<String> messageSupplier)`

자바9

- `requireNonNullElse(T obj, T defaultObj)`
- `requireNonNullElseGet(T obj, Supplier<? extends T> supplier)`

#### `java.util.Optional`

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



## null에 안전하다고 보장해주는 도구

