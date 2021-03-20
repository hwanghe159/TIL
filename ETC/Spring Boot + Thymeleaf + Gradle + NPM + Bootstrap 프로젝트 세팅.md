## Spring Boot + Thymeleaf + Gradle + NPM + Bootstrap 프로젝트 세팅

1. `build.gradle`에 `thymeleaf`와 개발 생산성을 위해 `devtools` 추가

   ```
   implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
   developmentOnly 'org.springframework.boot:spring-boot-devtools'
   ```

2. 서버 재시작 없이 template, static resource 실시간 반영 설정하기 -> https://jojoldu.tistory.com/48
3. `src/main/resources/static` 에서 `npm init`
4. `npm install bootstrap` 하여 부트스트랩 설치
5. 

