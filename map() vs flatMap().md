# map() :vs: flatMap()

`map()`은 스트림의 요소에 저장된 값 중에서 원하는 필드만 뽑아내거나 특정 형태로 변환해준다.

`flatMap()`은 `Stream<T[]>`를 `Stream<T>`로 변환해준다.

</br>

```java
Stream<String[]> strArrStrm = Stream.of(
	new String[]{"a", "b", "c"},
	new String[]{"d", "e", "f"}
};
```

위의 코드에서 각 문자열을 합쳐서 `Stream<String>`으로 만들고 싶다면 어떻게 해야 할까?

</br>

우선 `map()`을 사용해보자.

```java
Stream<Stream<String>> strStrStrm = strArrStrm.map(Arrays::stream);
```

예상한 것과 달리, `Stream<String>`형이 아니라 `Stream<Stream<String>>`형이다.

</br>

그럼 `flatMap()`을 사용해보자.

```java
Stream<String> strStrm = strArrStrm.flatMap(Arrays::stream);
```

우리가 원하는 `Stream<String>`형이 나왔다.

</br>

즉, `map()`은 각각의 요소 (`String[]`) 에 대해서 `map()`의 파라미터로 제공된 함수(`Arrays::stream`)를 수행하여 변환한다. 

그래서 위의 예에선 각각의 `String[]`이 `Arrays::stream`에 의해 `Stream<String>` 형으로 바뀌었으므로 최종 형은 `Stream<Stream<String>>` 인 것이다.

`flatMap()`은 파라미터로 제공된 함수를 수행하여 하나의 스트림으로 반환한다.

</br>

## 예시

1. 문자열 배열 안의 모든 문자열을 split하여 하나의 스트림으로 만들기

   ```java
   String[] lineArr = {
       "a b  c d",
       "e f g  h"
   };
   
   Stream<String> strStrm = Arrays.stream(lineArr)
                                  .flatMap(line -> Stream.of(line.split(" +")));
   ```

   </br>

2. 스트림의 스트림을 하나의 스트림으로 합칠 때

   ```java
   Stream<String> strStrm1 = Stream.of("a", "b", "c", "d");
   Stream<String> strStrm2 = Stream.of("e", "f", "g", "h");
   Stream<Stream<String>> strmStrm = Stream.of(strStrm1, strStrm2);
   
   Stream<String> strStream = strmStrm.map(s -> s.toArray(String[]::new))
                                      .flatMap(Arrays::stream);
   ```

   </br>

3. 두개의 배열의 모든 조합으로 여러 객체를 생성할때

   ```java
   class Card {
       private String shape;
       private String type;
       
       public Card(String shape, String type) {
           this.shape = shape;
           this.type = type;
       }
   }
   
   
   String[] shapes = {"다이아몬드", "클로버", "하트", "스페이드"};
   String[] types = {"A", "2", "3", "4", "5", "6", "7", "8", "9", "10", "J", "Q", "K"};
   List<Card> cards = Arrays.stream(shapes)
                        .flatMap(shape -> Arrays.stream(types)
                                 .map(type -> new Card(shape, type)))
                        .collect(Collectors.toList());
   ```

   </br>



아래는 `map()`과 `flatMap()`의 원형이다.

```java
<R> Stream<R> map(Function<? super T, ? extends R> var1);
```

```java
<R> Stream<R> flatMap(Function<? super T, ? extends Stream<? extends R>> var1);
```

</br>

## 참고

- 자바의 정석 3판 (p.828, p.833)
