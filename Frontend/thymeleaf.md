- 스프링 공식 문서 스프링 MVC로 웹 컨텐츠 서빙 https://spring.io/guides/gs/serving-web-content/
- 타임리프3 공식문서 https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html#introducing-thymeleaf
- Spring Boot로 웹 출시까지 재생목록 https://youtube.com/playlist?list=PLPtc9qD1979DG675XufGs0-gBeb2mrona

---

- 표현식
  - 변수 : ${…}
  - 선택 변수 : *{…}
  - 메시지 : #{…}
  - Link URL : @{…}
- 리터럴
  - 텍스트 : ‘one text’, ‘Another one’,…
  - 숫자 : 0, 34, 1.0, …
  - boolean : true, false
  - Null : null
  - token : one, sometext, main, …
- text opeation
  - 문자열 연결 : +
  - 문자 대체 : |The name is ${name}|
- 연산
  - Binary : +, -, *, /, %
  - 마이너스 : -
- boolean 연산
  - Binary : and, or
  - 부정 : !, not
- 비교 연산
  - 비교연산자 : >, <, >=, <= (gt, lt, ge, le)
  - 균등연산자 : ==, != (eq, ne)
- 조건 연산
  - if-then : (if) ? (then)
  - if-then-else : (if) ? (then) : (else)
  - Default : (value) ?: (defaultValue)



- `th:fragment`와 `th:replace`로 코드 재사용하기

```html
<!-- fragment/navbar.html -->
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<nav th:fragment="navbar">
  ...
</nav>
</html>
```

```html
<!-- index.html -->
<div th:replace="fragment/navbar :: navbar"></div>
```