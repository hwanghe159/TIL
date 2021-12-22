# Kotlin in action

## 1장. 코틀린이란 무엇이며, 왜 필요한가?

- 코틀린 맛보기

  ```kotlin
  data class Person(val name: String, val age: Int? = null)
  
  fun main(args: Array<String>) {
      val persons = listOf(Person("영희"), Person("철수", age = 20))
      val oldest = persons.maxBy { it.age ?: 0 }
      println("나이가 가장 많은 사람: $oldest")
  }
  ```

  - `name`, `age`를 갖고 있는 데이터 클래스 `Person` 정의
  - `age`의 디폴트 값은 null (`?`으로 디폴트값 지정 가능)
  - null인 경우 지정된 값 반환하는 엘비스 연산자(`?:`)

- 코틀린의 주요 특성

  - 자바가 실행되는 모든 곳이 대상 플랫폼이다
  - 정적 타입 지정 언어다
  - 함수형 프로그래밍과 객체지향 프로그래밍
  - 무료 오픈소스

<br/>

## 2장. 코틀린 기초

<br/>

## 3장. 함수 정의와 호출

<br/>

## 4장. 클래스, 객체, 인터페이스

<br/>

## 5장. 람다로 프로그래밍

<br/>

## 6장. 코틀린 타입 시스템

<br/>

## 7장. 연산자 오버로딩과 기타 관례

<br/>

## 8장. 고차 함수: 파라미터와 반환 값으로 람다 사용

<br/>

## 9장. 제네릭스

<br/>

## 10장. 애노테이션과 리플렉션

<br/>

## 11장. DSL 만들기

