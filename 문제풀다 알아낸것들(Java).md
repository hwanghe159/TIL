

### 정렬

```java
//배열
Arrays.sort(arr); //오름차순
Arrays.sort(arr, Comparator.reverseOrder()); //내림차순 (int[]이 아닌 Integer[]로 선언해야함)

//리스트
Collections.sort(list);
Collections.reverse(list);
```



### 초기화(배열)

```java
Arrays.fill(arr,값);
```



### 형변환

```java
//String -> char 배열
String str = "abc";
char[] charArray = str.toCharArray();
System.out.println(Arrays.toString(charArray)); //[a, b, c] 이렇게 출력됨

//String -> int
int result = Integer.parseInt("123");

//int -> String
String result = Integer.toString(123);

//String -> Boolean
Boolean result = Boolean.parseBoolean("true"); // "true"->true
    
//int[] -> List<Integer>
int[] arr = new int[]{1,2,3,4};
List<Integer> result = IntStream.of(arr).boxed().collect(Collectors.toList());
```



### 진수변환

```java
//int -> String
Integer.toBinaryString(127); //"1111111"
```



### 문자열 뒤집기

```
String str = "abc";
String result = new StringBuffer(str).reverse().toString();
```



### 문자열 합치기

```java
List<String> -> String
List<String> strings = ??;
String.join(", ", strings); //이렇게 하면 ", "으로 이어짐
```