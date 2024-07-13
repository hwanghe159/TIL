# Gradle

`build.gradle` 은 파일 자체가 Project 인터페이스를 구현하는 객체. groovy로 작성됨

```java
public interface Project extends Comparable<Project>, ExtensionAware, PluginAware {
}
```

## Groovy 기초 문법

```groovy
println ('hello') // 메소드 호출
println 'hello' // 괄호 생략 가능

String message = 'hi' // 변수 할당. String말고 var로도 가능

def sayHi = { // 클로저 생성
  println 'hi'
}
sayHi.call() // 클로저 실행
sayHi() // 클로저 실행
```

## Gradle 예시

```groovy
plugins { // { }는 groovy의 클로저(java의 람다와 비슷). Project의 plugins 메서드
    id 'java' // plugins 메서드가 closure를 인자로 받고 있음. 해당 closure는 id('java')
}

// Project가 상속받고 있는 PluginAware에 
// void apply(Map<String, ?> var1); 있음. 아래에서 from은 key, 'dep...'은 value
apply from: 'dependencies.gradle'

group 'com.idus' // Project객체의 group 재정의
version '0.3.1' // Project객체의 version 재정의

ext { // 커스텀 프로퍼티 만들기
    set('snippetsDir', file("build/generated-snippets"))
}

dependencies {
    implementation "org.springframework.boot:spring-boot-starter-web"
    implementation "org.springframework.boot:spring-boot-starter-data-jpa"
}
```

```java
// 위 gradle 파일을 내맘대로 java 문법으로 변환해보기

plugins(() -> id("java"));

apply(Map.of("from", "dependencies.gradle"));

setGroup("com.idus");
setVersion("0.3.1");

ext(() -> set("snippetsDir", file("build/generated-snippets"));

dependencies(() -> {
  implementation("org.springframework.boot:spring-boot-starter-web");
  implementation("org.springframework.boot:spring-boot-starter-data-jpa");
})
```


## Gradle build 라이프사이클

- `./gradlew build` 를 하면?
	1. 초기화 : 루트 디렉토리에서 `settings.gradle`을 찾고,  이를 이용하여 빌드에 참여할 프로젝트를 결정하고, 각 프로젝트에 대한 `Project` 인스턴스를 생성한다
	2. 구성 : 각 프로젝트의 `build.gradle`을 이용하여 `Project` 객체들이 구성된다. 모든 태스크의 디펜던시 그래프를 만든다.
	3. 실행 : 선택된 각 태스크를 실행한다