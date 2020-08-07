## Spring Security - Auth0 JWT Library

![주석 2020-08-04 160156](C:\Users\junho\TIL\images\주석 2020-08-04 160156.png)

- request가 들어오면 필터를 거친다
  - 필터는 여러개다. 가장 앞에 있는건 UsernamePasswordAuthenticationFilter
  - 입맛에 맞는 필터들을 적용한다. 커스텀 필터를 만들수도 있다
- 필터를 거친 객체는 인증요청 객체로 변환된다.(리퀘스트의 body부분에 인증객체들이 JSON형태로 묻어있다.)
- 묻어 있는 인증 객체들을 DTO클래스로 빼고 DTO클래스를 객체(Authentication을 상속받음)로 만들어서 ProviderManager의 authenticate()로 전달한다.
  - ProviderManager는 여러개의 AuthenticationProvider를 제공할 수 있다
  - 이터레이션을 돌면서 이 Authentication 클래스에 맞는 Provider를 자동으로 검색해서 인증처리를 한다. 그 후 인증된 객체를 돌려준다
- 인증된 객체도 Authentication를 상속받는다.
  - 인증됐으니까 isAuthenticated()를 호출하면 true다. (인증요청 객체의 isAuthenticated()는 false)
- 인증 콘텍스트? 인증 콘텍스트 관리자?
  - 인증 콘텍스트를 만들면 그 인증 콘텍스트를 만든 것을 인증 콘텍스트 관리자에게 부여하는 것.
  - 인증 콘텍스트 관리자가 인증 콘텍스트 객체를 들고 있다가 필요한 순간에 그 객체를 뿌려준다.
- 모든 절차가 끝나고 나면 인증이 어떻게 이루어졌는지를 response를 통해서 user에게 프론트단으로 돌려준다

### AuthenticationManager: AuthenticationProvider 주머니

- Builder 패턴으로 구현
- 등록된 Authentication Provider들에 접근하는 유일한 객체
- 단순 인터페이스에 불과하다. 내장 구현체: ProviderManager
- AuthenticationManager를 구현해서 쓰지 말자. Pivotal 기술자보다 더 잘 만들 자신 없으면.
- 구현해서 쓰라고 넣어준 인터페이스가 아니다.

### WebSecurityConfigurerAdapter를 상속해서 설정해준다.

### AuthenticationProvider: 진짜 인증이 일어나는 곳

- 스프링 시큐리티의 알파와 오메가
- 인증 "전" 객체를 받아 인증 가능 여부를 체크한 후, 예외를 던지던지 인증 "후" 객체를 만들어 돌려준다.
- 구현하라고 넣어준 인터페이스다.
- 필요에 맞게 정교하게 구현하고 인증 관리자에 등록시키자.

### AuthenticationProvider를 상속해서 구현해준다.

- authenticate(), supports() 등을 오버라이드
  - authenticate() : 인증을 진행하고 인증 객체를 만들어줌
  - supports() : 이 클래스가 어떤 인증 객체를 지원할지 명시해줌. 여기서 등록된 인증 객체로 들어오는 요청은 다 이 필터에 걸림

### 인증 객체는 뭔가? Authentication 클래스의 모든 서브클래스.

- Authentication은 인터페이스.
  - **getAuthorities()**, getCredentials(), getDetails(), getPrincipal(), **isAuthenticated()**, setAuthenticated() 등이 있다.

### UsernamePasswordAuthenticationToken

- 가장 간단한 형태
- 두개의 생성자를 갖고 있음
  - `UsernamePasswordAuthenticationToken(java.lang.Object principal, java.lang.Object credentials)`
    - 이 생성자를 통해 만든 `UsernamePasswordAuthenticationToken`은 인증 전 객체임. Provider의 손을 타지 않은 객체. 
    - 그래서 `AbstractAuthenticationToken.isAuthenticated()`는 항상 `false`
  - `UsernamePasswordAuthenticationToken(java.lang.Object principal, java.lang.Object credentials, java.util.Collections<? extends GrantedAuthority> authorities)`
    -  manager나 provider의 인증 과정을 거쳐서 인증이 끝난 객체만 담아야 한다.
    - 권한정보(authorities)가 들어있으면 "이 사용자는 인증을 받았구나" 하고 간주하는 것.
  - principal은? 인증의 주체가 되는 오브젝트.
  - 이땐 principal이 username, credential이 password 



### 결국 우리가 구현해야 할 것

- 요청을 받아낼 (AbstractAuthenticationFilter을 상속하는)필터
  - 인증의 의미단위별로 구현해야 함.
  - ex) form로그인, 소셜로그인 두개를 만든다면 필터를 2개 만들어야한다 
  - attemptAuthentication(), successfulAuthentication(), unsuccessfulAuthentication() 오버라이드
    - attemptAuthentication() : Provider의 authenticate()메서드로 인증을 시도함.
    - successfulAuthentication() : 성공했다면 jwt토큰을 HttpResponse로 내려줘야함
    - unsuccessfulAuthentication() : 실패했다면 후속조치
- Manager에 등록시킬 Auth Provider
  - 인증로직 구현
- AJAX 방식이라면, 인증 정보를 담을 DTO
- 각 인증에 따른 추가 구현체. 기본적으로 성공/실패 핸들러
- 소셜 인증의 경우 각 소셜 공급자의 규격에 맞는 DTO와 HTTP req객체.(RestTemplate?)
- (선택사항) 인증 시도 / 인증 성공시에 각각 사용할 Authentication객체



### Implicit Grant Flow

![주석 2020-08-04 163854](C:\Users\junho\TIL\images\주석 2020-08-04 163854.png)

### 유저 객체 설계

- 유저 인증을 위해 필요한 정보, 서비스 제공을 위한 정보를 필요한 만큼 저장한다.
- 비밀번호를 비롯한 민감 정보는 암호화하는 것이 원칙이다.(BCryptPasswordEncoder)
- 소셜 회원도 담을 수 있게 확장성있게 구현한다.
- @ElementCollection, Enum 등을 활용하자.



## FormLoginFilter + JWT Factory + JWT Authentication Provider / Filter로 AJAX로그인 구현하기!

- FormLoginFilter : 최초 로그인을 시도할때 로그인 요청을 걸러내는 필터
- JWT Factory : JWT를 만듦 + 검증
- JWT Authentication Provider : API에 접근하는 요청에 묻어있는 HTTP Authorization헤더의 값을 가져와서 권한을 갖고 있는지 확인
- JWT Authentication Filter : 해당 요청에 대한 필터링, Provider연결하여 사용자가 인증된 사용자인지 확인

JWT에 대한 [좋은 글](https://blog.outsider.ne.kr/1160) (댓글을 꼭 읽어보자)



- FormLoginAuthenticationSuccessHandler
  - PostAuthorizationToken이 Provider에서 넘어오면 (Provider의 authenticate()의 리턴값이 PostAuthorizationToken임.) 넘어온 토큰을 가지고 successfulAuthentication

