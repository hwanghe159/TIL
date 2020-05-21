# MockMvc를 이용한 스프링 MVC 테스트

>  MockMvc란 웹 어플리케이션을 서버에 배포하지 않고도 스프링 MVC의 동작을 재현할 수 있는 클래스이다.

Presentation Layer 테스트 하기 유리하다.

- 세밀한 설정이 가능(session 등)



```java
@Test
public void createMember() throws Exception {
    Member member = new Member(1L, TEST_USER_EMAIL, TEST_USER_NAME, TEST_USER_PASSWORD);
    given(memberService.createMember(any())).willReturn(member);

    String inputJson = "{\"email\":\"" + TEST_USER_EMAIL + "\"," +
            "\"name\":\"" + TEST_USER_NAME + "\"," +
            "\"password\":\"" + TEST_USER_PASSWORD + "\"}";

    this.mockMvc.perform(post("/members")
            .content(inputJson) // 요청 본문을 설정한다.
            .accept(MediaType.APPLICATION_JSON)
            .contentType(MediaType.APPLICATION_JSON))
            .andExpect(status().isCreated())
            .andDo(print())
            .andDo(MemberDocumentation.createMember());
}
```



전체적인 형태는 이래와 같다

```
mockMvc.perform(요청 메서드, 요청 데이터 설정 등)
	.andExpect(기댓값)
	.andDo(추가로 수행할 메서드)
	.andDo(추가로 수행할 메서드)
	...
```



# 참고

[https://ktko.tistory.com/entry/%EC%8A%A4%ED%94%84%EB%A7%81Spring-MockMvc-%ED%85%8C%EC%8A%A4%ED%8A%B8](https://ktko.tistory.com/entry/스프링Spring-MockMvc-테스트)