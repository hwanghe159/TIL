# 깊은 복사(deep copy) :vs: 얕은 복사(shallow copy)



### 객체의 복사

```java
public class Person {
    private String name;
    private int age;
    
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public void setName(String name){
        this.name = name;
    }
    public void setAge(int age){
        this.age = age;
    }
}

public static void main(String[] args) {
    Person p1 = new Person("준호", 26);
    
    Person p2 = p1;       //p1에서 p2로의 얕은 복사
    p2.setName("그래");   //p1.name도 변경된다.
    p2.setAge(25);        //p1.age도 변경된다.
}
```

`Person` 클래스의 인스턴스 `p1`을 만들고

`Person p2 = p1;`와 같이 `p2`에 복사를 했다.

그리고 `p2`의 인스턴스 변수를 바꾸면 `p1`의 인스턴스 변수의 상태도 변한다.

위와 같은 현상은 데이터를 복사하는 게 아니라 주소값을 복사하는 `얕은 복사`이기 때문에 일어난다.



그렇다면 데이터를 복사하여 서로 영향을 끼치지 않게 하려면?

`깊은 복사`를 이용한다.

`깊은 복사`를 하려면 `Person`클래스가 `Cloneable`인터페이스를 상속받아 `clone()`메서드를 구현해야 한다.

```java
public class Person implements Cloneable {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public void setName(String name) {
        this.name = name;
    }
    public void setAge(int age) {
        this.age = age;
    }

    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}

public static void main(String[] args) {
    Person p1 = new Person("준호", 26);
    
    Person p2 = (Person) p1.clone(); //p1에서 p2로의 깊은 복사
    p2.setName("그래");              //p1.name은 변경 안된다.
    p2.setAge(25);                  //p1.age은 변경 안된다.
}
```



 

### ArrayList 복사

위에서 `=` 기호를 이용해서 얕은복사, `별도의 메서드`를 사용해서 깊은 복사를 했다.

ArrayList에서도 유사하다.

```java
public class Main {
    public static void main(String[] args) {
        List<Person> people1 = new ArrayList<>(Arrays.asList(
                new Person("a", 1), new Person("b", 2), new Person("c", 3)));

        List<Person> people2 = people1; //얕은 복사
		people1.clear();                //people2의 모든 원소도 삭제된다.
    }
}

```



`깊은 복사`를 하려면?

```java
public class Main {
    public static void main(String[] args) {
        List<Person> people1 = new ArrayList<>(Arrays.asList(
                new Person("a", 1), new Person("b", 2), new Person("c", 3)));

        List<Person> people2 = new ArrayList<>();//addAll사용전 초기화
        people2.addAll(people1);                 //깊은 복사
		//List<Person> people2 = new ArrayList<>(people1); 위의 두 줄을 이와 같이 쓸 수도 있다.
        
        people1.clear();                         //people1의 모든 원소만 삭제될뿐,
                                                 //people2엔 영향 없다
    }
}
```



그렇다면 `people2.addAll(people1);` 을 통해 깊은 복사를 했는데,

`people1` 내부의 객체들과 `people2` 내부의 객체들은 얕은 복사가 되었을까? 깊은 복사가 되었을까?

그러니까 `people1` 내부의 `Person("a",1)` 을 수정하면 `people2` 내부의 `Person("a",1)`도 수정될까?



수정이 된다. 그리고 수정을 막으려면 다음과 같이 하면 된다.

```java
public class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public Person(Person person) { //복사 생성자 추가
        this.name = person.name;
        this.age = person.age;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setAge(int age) {
        this.age = age;
    }
}


public class Main {
    public static void main(String[] args) {
        List<Person> people1 = new ArrayList<>(Arrays.asList(
                new Person("a", 1), new Person("b", 2), new Person("c", 3)));

        List<Person> people2 = new ArrayList<>();
        for (Person person : people1) {
            people2.add(new Person(person));
        }

        people1.get(0).setName("z"); //people2에는 영향없다.
    }
}
```



하지만 위와 같이 원소 하나하나 복사하고 앉아있기엔 너무 복잡하다.

위와 같이 하는 이유는 '객체 상태를 변경하면 다른 객체도 변경될까봐' 인데 애초에 상태를 변경할 수 없다면 객체 하나하나 깊은 복사를 할 필요가 없지 않은가?

Person객체가 [VO](https://mommoo.tistory.com/61)라면? (즉, setter 메서드가 없어서 상태를 변경할 수 없다면?) 

객체의 상태 변화에 대해 걱정을 하지 않아도 된다.

추가로, `Collections.unmodifiableList()`를 이용하면 리스트에 대한 수정을 막을 수 있다.

```java
public class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    /* setter 제거 */
}


public class Main {
    public static void main(String[] args) {
        List<Person> people1 = new ArrayList<>(Arrays.asList(
                new Person("a", 1), new Person("b", 2), new Person("c", 3)));

        //깊은복사이다. people2는 이제 add()등의 메서드 사용 불가
		List<Person> people2 = Collections.unmodifiableList(new ArrayList<>(people1)); 
        
        people1.clear(); //people1의 모든 원소만 삭제될뿐,
                         //people2엔 영향 없다
    }
}

```





> 우리가 이렇게 얕은 복사, 깊은 복사를 따지는 이유는
>
> 어떤 한 객체나 리스트를 수정했을 때, 엉뚱한 곳에서 변경이 되는 것을 막기 위해서다.
>
> 그럼 애초에 수정을 못하도록 (상태를 변화시킬 수 없도록) VO로 만든다. (상태를 변화시키는 메서드를 제거한다.)
>
> 그리고 리스트는 unmodifiableList등의 메서드를 활용하여 보호한다.



### 참고

- https://heavenly-appear.tistory.com/298

- https://library1008.tistory.com/47

- https://os94.tistory.com/154

  