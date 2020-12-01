# 스프링 부트와 AWS로 혼자 구현하는 웹 서비스 정리

## Chapter 5 스프링 시큐리티와 OAuth 2.0으로 로그인 기능 구현하기

OAuth 로그인 구현 시 다음과 같은 것들을 모두 구글, 페이스북, 네이버 등에 맡기고 서비스 개발에 집중할 수 있다.

- 로그인 시 보안
- 회원가입 시 이메일 혹은 전화번호 인증
- 비밀번호 찾기
- 비밀번호 변경
- 회원정보 변경



1. `build.gradle`에 스프링 시큐리티 관련 의존성 하나를 추가한다.

   `compile('org.springframework.boot:spring-boot-starter-oauth2-client')`

2. `build.gradle` 설정이 끝났으면 소셜 로그인 설정 코드를 작성한다. (OAuth라이브러리를 이용)

   1. `WebSecurityConfigurerAdapter`을 구현하는 시큐리티 설정 클래스를 생성한다.
   2. `configure(HttpSecurity http)`를 오버라이드 하여 다음을 설정한다.
      - URL, HTTP 메소드 별 권한 관리 대상을 지정
      - 로그아웃 후 리다이렉트할 경로
      - 로그인 성공 시 후속조치를 진행할 구현체 등록
      - 등등..
   3. 위에서 설정한 것들 중 '로그인 성공 시 후속조치를 진행할 구현체'에 해당하는 `CustomOAuth2UserService` 클래스를 생성한다.
      - 로그인 이후 가져온 사용자의 정보들을 기반으로 가입, 정보수정, 세션저장 등의 기능을 제공하려 한다.
      - `OAuth2UserService<OAuth2UserRequest, OAuth2User>`을 구현한다.
      - 

