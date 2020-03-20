---
title: oauth2 - 구글 로그인
date: 2019-04-21 17:07:08
tags: SpringSecurity
toc: true
widgets:
  - type: toc
    position: right
  - type: tagcloud
    position: right
sidebar:
  right:
    sticky: true
---
![google_logo](/images/google_logo.png)
# Spring
## SpringSecurity-OAuth2
<!-- more -->
최근 서비스들은 자체적으로 계정을 가입하고, 접근하는 방식도 있지만, 구글, 카카오, 네이버, 페이스북등으로 계정관리를 하고있습니다.
이번 포스팅에서는 Spring Security & 구글 OAuth를 사용하여 계정 권한관리에 대해 알아보려고 합니다.
빌드 도구로는 gradle을 사용해 보려고 합니다.

---
### 구글 OAuth

#### 의존성 추가
```
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-actuator'
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'

    compileOnly 'org.projectlombok:lombok'

    runtimeOnly 'com.h2database:h2'
    runtimeOnly 'mysql:mysql-connector-java'

    annotationProcessor 'org.projectlombok:lombok'

    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    testCompile('org.springframework.security:spring-security-test')
    testCompile('io.rest-assured:rest-assured:3.0.3')
    
    compile("org.mariadb.jdbc:mariadb-java-client")
    compile group: 'org.springframework.security.oauth', name: 'spring-security-oauth2', version: '2.3.5.RELEASE'
}

```
- SpringBoot 버전이 2점대로 올라가면서 의존성 추가 방법이 조금 다를 수 있다.
- rest-assured은 Rest API Test를 위한 의존성

#### application.yml 설정
```
spring:
  profiles: local
  
  jpa:
    show-sql: true
    hibernate:
      ddl-auto: create-drop
  h2:
    console:
      enabled: true

  logging:
    level:
      org.hibernate.type: trace # JPA로 생성되는 쿼리의 파라미터 값 확인
```

### SpringSecurity 기본인증 옵션 비활성화
- SpringSecurity가 의존성에 있으면 기본인증이 자동으로 추가된다.
- 현재 상황에 필요한 기능이 아님으로 비활성화
    - 기존 스프링부트 1점대에서는 properties파일 또는 yml 파일에서 security.basic.enabled: false로 변경할 수 있었다.
    
    - 2점대 부터는 아래와 WebConfigurerAdapter를 상속하는 @Configuration 클래스를 만들어서 설정해줘야한다.
    ![oauth2](/images/springboot/oauth01-1.png)
    - 모든 요청에 대하여 인증을 하지 않은것으로 설정한다.
    ![oauth2](/images/springboot/oauth01-2.png)
    
#### index.html 호출 test 파일작성
- 기본 루트로 접속 시, index.html을 호출하는지 확인하기 위해 테스트 클래스를 작성한다.
![oauth2](/images/springboot/oauth01-3.png)

#### 구글 OAuth 등록
- [구글 개발자 콘솔](http://console.developers.google.com)에 접속한다.
- 프로젝트 생성 
![oauth2](/images/springboot/oauth01-4.png)

- 프로젝트 정보 입력 및 생성
![oauth2](/images/springboot/oauth01-5.png)

- 만들어진 프로젝트를 선택한다.

- 사용자 인증 정보 페이지로 이동한다.
![oauth2](/images/springboot/oauth01-6.png)

- OAuth 클라이언트 ID 생성
![oauth2](/images/springboot/oauth01-7.png)

- OAuth 동의 화면에서 어플리케이션 이름을 설정해준다.
![oauth2](/images/springboot/oauth01-8.png)

- 정보 입력
![oauth2](/images/springboot/oauth01-9.png)
    - 이름(어플리케이션 이름이 아니다.)
    - 테스트용으로 localhost 주소를 사용했다.

- 완료하게 되면 클라이언트 ID 및 보안(security)를 확인할 수 있다.

#### 구글 인증 정보를 프로젝트에 적용하기
- 구글 인증 정보를 프로젝트 yml파일에 등록한다.
    ```
       google:
         client:
           clientId: 
           clientSecret: 
           accessTokenUri: https://accounts.google.com/o/oauth2/token
           userAuthorizationUri: https://accounts.google.com/o/oauth2/auth
           clientAuthenticationScheme: form
           scope: email, Profile
         resource:
           userInfoUri: https://www.googleapis.com/oauth2/v2/userinfo
       ```

- **git 인증정보가 노출 될 수 있기 때문에 별도의 yml파일을 생성하고 gitignore설정을 해준다.**
    ![oauth2](/images/springboot/oauth01-10.png)
    
- 프로젝트 실행 시, 호출될 수 있도록 Application.java에 추가해준다.(**여러개일때는 , 필수**)
![oauth2](/images/springboot/oauth01-11.png)

- SecurityConfig에 아래와 같은 설정을 해준다.
    ```
    @Configuration
    public class SecurityConfiguration extends WebSecurityConfigurerAdapter {
    
        @Override
        protected void configure(HttpSecurity http) throws Exception {
            http.antMatcher("/**")
                    .authorizeRequests()
                    .antMatchers("/", "/h2-console/**", "/favicon.ico", "/login**").permitAll()
                    .anyRequest().authenticated()
                    .and().logout().logoutSuccessUrl("/").permitAll()
                    .and().headers().frameOptions().sameOrigin()
                    .and().csrf().disable();
        }
    }
    ```

    - csrf를 끈 이유
        - 로컬환경에서 H2 Console에 접근하기위해
        - 불필요한 테스트 코드 작성을 줄이기 위해
    - 루트, h2-console, 파비콘, login 등에 권한체크 제외설정을 해준다.

#### Google OAuth 설정
- OAuth 설정 파일 생성
	- @Configuration
	- @EnableOAuth2Client
		- OAuth2ClientContext가 빈으로 등록이 가능하다.
	- 구글로그인 정보를 받을 빈을 등록한다.
        ```
        @Bean
        @ConfigurationProperties("google.client")
        public OAuth2ProtectedResourceDetails googleClient() {
            return new AuthorizationCodeResourceDetails();
        }
        
        @Bean
        @ConfigurationProperties("google.resource")
        public ResourceServerProperties googleResource() {
            return new ResourceServerProperties();
        }	
        ```

	- 필터를 빈으로 등록하고 설정한다.
        ```
        @Bean
        public Filter ssoFilter(){
            OAuth2ClientAuthenticationProcessingFilter oauth2Filter = new OAuth2ClientAuthenticationProcessingFilter("/login/google");
            OAuth2RestTemplate oAuth2RestTemplate = new OAuth2RestTemplate(googleClient(), oAuth2ClientContext);
            oauth2Filter.setRestTemplate(oAuth2RestTemplate);
            oauth2Filter.setTokenServices(new UserInfoTokenServices(googleResource().getUserInfoUri(), googleClient().getClientId()));
            oauth2Filter.setAuthenticationSuccessHandler(google);
            return oauth2Filter;
        }
        	
        ```
		- OAuth2ClientAuthenticationProcessingFilter의 인자값 = OAuth 로그인 시작 포인트
		- OAuth2RestTemplate 설정 및 필터에 설정
		- UserInfoTokenServices 설정 (토큰을 얻을 클라이언트 설정)
		- SuccessHandler 등록
	
	- 필터를 등록 할 FilterRegistrationBean을 빈으로 등록
        ```
        @Bean
        public FilterRegistrationBean oauth2ClientFilterRegistration(OAuth2ClientContextFilter filter) {
            FilterRegistrationBean registration = new FilterRegistrationBean();
            registration.setFilter(filter);
            registration.setOrder(-100);
            return registration;
        }
        ```

	- Authentication 객체를 주입받아 로그인 정보를 얻는다.
        ```
        private LoginAccount getGoogleUser(Authentication authentication) {
            OAuth2Authentication oAuth2Authentication = (OAuth2Authentication) authentication;
            return objectMapper.convertValue(oAuth2Authentication.getUserAuthentication().getDetails(), LoginAccount.class);
        }
        ```
		- Authentication객체를 OAuth2Authentication로 형변환
		- ObjectMapper 객체를  미리 만들어놓은 LoginAccount 객체와 매핑하여  컨버트 한다.
- 문제발생..
    - 스프링부트 2점대 버전이 되면서 Spring Security OAuth 기능의 일부가 Spring Security로 마이그레이션 되는중이라 지원안하는 기능 발생
    - UserInfoTokenService, ResourceServerProperties 클래스 및 @EnableOAuth2Sso 애노테이션 사용이 불가능
    - 2점대에서 사용하는 방법을 숙지하고 싶었으나.. OAuth2에 대한 이해가 적고, SpringSecurity에 대한 이해가 부족하여 임시방편을 사용하였다.
        - spring-security-oauth2 → spring-security-oauth2-autoconfigure로 변경
        - spring-cloud-security로 변경하는것도 가능하다고 한다.

- 생성한 ssoFilter가 Security를 거치도록 설정한다.
    ![oauth2](/images/springboot/oauth01-12.png)
    - OAuthConfig에서 생성한 ssoFilter 추가
    - 테스트 파일
        ![oauth2](/images/springboot/oauth01-13.png)
        -  google.yml파일을 테스트 시에도 사용할 수 있도록 @TestPropertySource로 추가
        - OAuth2 로그인의 경우 인증코드(code)를 발급 받고, 발급 받은 인증코드로 AccessToken을 다시 발급 받는 과정이 있어 중간 리다이렉션이 발생합니다.
            - 이런 과정들로 login URL 테스트 결과를 확인하기 어려워 리다이렉션을 방지 하였다.
            >**302코드**
             "3xx Redirection"클래스에 속한다.
             301 : 완전히 새로운 URL로 이동
             302 : 임시적으로 새로운 URL로 이동
             
#### 브라우저에서 확인
- localhost:8080/login
![oauth2](/images/springboot/oauth01-14.png)

- [구글계정으로 가입된 서비스](https://myaccount.google.com/permissions)에서 확인 할 수 있다.
![oauth2](/images/springboot/oauth01-15.png)

### 로그인 세션 관리
- OAuth2를 사용하는것은 사용자 인증 및 허가된 정보를 가져오는것
- 인증된 정보를 통해 로그인 세셔관리가 필요하다.

- 세션을 사용하는 방법
    - 톰캣 세션 사용
        - HttpSession을 사용할 경우
        - 2대이상의 WAS가 구동되는 환경에서는 톰캣들간의 세션 공유를 위한 추가설정이 필요
    - **Database를 세션저장소로 사용(진행할 예정)**
        - WAS들간의 공용 세션을 사용할 수 있는 가장 쉬운 방법
        - 많은 설정이 필요없지만, 결국 로그인 요청마다 DB IO가 발생하여 성능상 이슈가 발생할 수 있다.
        - 보통 로그인이 요청이 많이 없는 백오피스, 사내시스템 용도에서 사용합니다.
    - Redis, Memcached등의 메모리 DB를 세션 저장소로 사용
        - 사용자 서비스에서 가장 많이 사용되는 방식입니다.
        - 실제 서비스로 사용하기 위해서는 Embedded Redis와 같은 방식이 아닌 외부 메모리 서버가 필요합니다.
        - 참고 : [Havi님 블로그](https://haviyj.tistory.com/38)

### Database를 세션저장소로 사용
#### 의존성 추가
- jdbc 및 session 의존성을 추가한다.
```
compile group: 'org.springframework.session', name: 'spring-session', version: '1.3.5.RELEASE'
compile('org.springframework.boot:spring-boot-starter-jdbc')
```
#### Session 테이블 생성
- 세션의 저장을 DB에 하기 위해서 세션 테이블이 생성되어야한다.
    - SpringSession에서 어떠한 형태로 쿼리문을 사용하는지 알려준다.
    - 맥/intelij 기준으로 , Command + Shift + o schema-검색하면 아래와 같이 DBMS에 맞춰 스키마 쿼리를 확인할 수 있다.
    ![oauth2](/images/springboot/oauth01-16.png)
    
    - 복사하여 새로 작성한다.
        - resources 아래 schema-h2.sql
        
- 새로 작성한 스키마를 적용하도록 application.yml에 설정한다.
```
spring:
  datasource:
    data: classpath:schema-h2.sql
```
#### JdbcSession 옵션 활성화
- @EnableJdbcHttpSession만 사용하면 된다.
    ![oauth2](/images/springboot/oauth01-17.png)
    
#### SuccessHandler 생성
- AuthenticationSuccessHandler를 implements하는 클래스 생성
    ```
    @Component
    public class GoogleAuthenticationSuccessHandler implements AuthenticationSuccessHandler {
    
        private HttpSession httpSession;
        private ObjectMapper objectMapper;
    
        public GoogleAuthenticationSuccessHandler(HttpSession httpSession, ObjectMapper objectMapper) {
            this.httpSession = httpSession;
            this.objectMapper = objectMapper;
        }
    
        @Override
        public void onAuthenticationSuccess(HttpServletRequest request, HttpServletResponse response, Authentication authentication) throws IOException, ServletException {
            httpSession.setAttribute(SessionConstants.LOGIN_USER, getGoogleUser(authentication)); 
            /* 간단한 구글계정 정보를 세션에 저장*/
            response.sendRedirect("/me");
        }
        
        /* OAuth 인증정보를 통해 GoogleUser 인스턴스 생성 */
        private GoogleUser getGoogleUser(Authentication authentication) { 
            OAuth2Authentication oAuth2Authentication = (OAuth2Authentication) authentication;
            return objectMapper.convertValue(oAuth2Authentication.getUserAuthentication().getDetails(), GoogleUser.class);
        }
    }
    ```

    - ObjectMapper의 경우 스프링부트에서 빈으로 등록해준다.(과거에는 아니었지만..)
    - 이 SuccessHandler는 로그인 유저의 기본적인 정보를 LOGIN_USER라는 키값에 담는다.
    
- SessionConstants.class 생성
![oauth2](/images/springboot/oauth01-18.png)
- 리다이렉트 되는 "/me" 컨트롤러
![oauth2](/images/springboot/oauth01-19.png)
- GoogleUser.class 생성
![oauth2](/images/springboot/oauth01-20.png)
    - ObjectMapper가 필드를 명확히 인식하기 위해 @JsonProperty로 필드명을 지정
    - @JsonIgnoreProperties(ignoreUnknown = true) : 멤버변수로 지정되지 않은 필드는 무시
    - Serializable
    
#### SuccessHandler 적용
- 완성된 SuccessHandler를 필터에 적용시킨다.
    ![oauth2](/images/springboot/oauth01-21.png)

#### 결과
- localhost:8080/me 접속
    - 인증 오류 403 발생
    ![oauth2](/images/springboot/oauth01-22.png)
- localhost:8080/login 접속 및 google 로그인
    - 유저 정보 확인
    ![oauth2](/images/springboot/oauth01-23.png)

---
많은 자료들이 스프링부트 1점대를 기준으로 작성되어 작업이 생각보다 오래걸렸습니다..😂
구글로그인을 해보고나니, 여러가지 다른 인증들도 해보고 싶어졌습니다.
음.. 이번 내용들을 블로깅하면서 내가 security부분도 많이 부족하고 강의를 보고 복습도했지만.. 
아직 스프링 그리고 스프링부트를 완벽히 이해하고 있지 않다는 생각이 들었습니다.
<br><br>
