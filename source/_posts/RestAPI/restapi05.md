---
title: 스프링 기반 REST API 개발05 - RestAPI 보안 적용(@Enumerated, @ElementCollection, 스프링 시큐리티 설정, auth서버, 리소스서버, @ConfigurationProperties, form-data  request body, 현재 사용자 조회, @JsonSerialize)
date: 2019-09-23 15:38:41
tags: RestAPI
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

# 스프링 기반 REST API 개발(inflearn)5 - 백기선

## RestAPI 보안 적용
<!-- more -->
### Account 도메인 추가

- SpirngSecurity Auth2 사용
  - Grant Type : password

#### Account 도메인 생성

```java
@Entity
@Getter @Setter @Builder
@NoArgsConstructor @AllArgsConstructor
@EqualsAndHashCode(of = "id")
public class Account {

    @Id @GeneratedValue
    private Integer id;
  
    private String email;
  
  	private String password;

    @ElementCollection(fetch = FetchType.EAGER)
    @Enumerated(EnumType.STRING)
    private Set<AccountRole> roles;

}
```

- id
- email
- password
- Account Roles
  - ADMIN
  - USER

#### JPA 맵핑

- User라는 이름은 PostgreSQL에서 예약어이다.
- 예약어를 피해서 도메인 클래스이 이름을 Account로 결정하였다.\
  - @Tables("Users")  이런식으로 테이블에 저장 될 테이블 명을 변경하는 방법도 있다.

#### JPA enumeration collection mapping

```java
@ElementCollection(fetch = FetchType.EAGER)
@Enumerated(EnumType.STRING)
private Set<AccountRole> roles;
```

- @ElementCollection : collection 속성 (여러개의 enum이 들어갈 수 있다.)
- @Enumerated(EnumType.STRING)를 선언하면 Enum 필드가 테이블에 저장시 숫자형인 1,2,3이 아닌, **Enum의 name**이 저장된다.

<br><br>

### 스프링 시큐리티 적용

#### 스프링 시큐리티

![restapi05-1](/images/restapi/restapi05-1.png)

- 웹 시큐리티 (Filter 기반 시큐리티)
  - 웹요청에 보안인증
- 메소드 시큐리티
  - 웹과 상관없이 메소드가 호출됬을 때 보안인증
- 둘 다 Security Interceptor를 사용한다.
  - 리소스에 접근을 허용할 것이냐, 말것이냐를 결정하는 로직이 들어있다.
- 인증, 인가의 로직을 처리한다.

#### 의존성 추가

```xml
<dependency>
  <groupId>org.springframework.security.oauth.boot</groupId>
  <artifactId>spring-security-oauth2-autoconfigure</artifactId>
  <version>2.1.0.RELEASE</version>
</dependency>
```

#### UserDetailsService 구현

- AccountService

  ```java
  @Service
  @RequiredArgsConstructor
  public class AccountService implements UserDetailsService {
  
    private final AccountRepository accountRepository;
  
    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
      Optional<Account> optionalAccount = accountRepository.findByEmail(username);
      Account account = optionalAccount.orElseThrow(() -> new UsernameNotFoundException(username));
      return new User(account.getEmail(), account.getPassword(), authorities(account.getRoles()));
    }
  
    private Collection<? extends GrantedAuthority> authorities(Set<AccountRole> roles) {
      return roles.stream().map(r -> new SimpleGrantedAuthority("ROLE_" + r.name())).collect(Collectors.toSet());
    }
  }
  ```

  - 해당 username에 해당하는 Account를 찾아서 스프링 시큐리티에서 제공하는 User(**UserDetails를 구현하는**)로 만들어서 return한다.

- Test

  ```java
  @Test
  public void loadUserByUsername() {
    // given
    Set<AccountRole> accountRoleSet = new HashSet<>();
    accountRoleSet.add(ADMIN);
    accountRoleSet.add(USER);
  
    String username = "bong@email.com";
    String password = "bong";
    Account account = Account.builder()
    .email(username)
    .password(password)
    .roles(accountRoleSet)
    .build();
  
    accountRepository.save(account);
  
    // when
    UserDetailsService userDetailsService = accountService;
    UserDetails userDetails = userDetailsService.loadUserByUsername(username);
  
    // then
    assertThat(userDetails.getPassword()).isEqualTo(password);
  }
  ```

- 원래는 유저 저장 시, PasswordEncoder를 사용하여 인코딩해야하지만 이후에 하기로하고 생략하겠다.

<br><br>

### 예외 테스트

1. @Test(expected)

   ```java
   @Test(expected = UsernameNotFoundException.class)
   public void findByUserNameFail() {
     String username = "random@email.com";
     accountService.loadUserByUsername(username);
   }
   ```

   - @Test에 예상되는 Exception을 기재한다.

2. try-catch

   ```java
   @Test
   public void findByUserNameFail() {
     String username = "random@email.com";
     try {
       accountService.loadUserByUsername(username);
       fail("fail");
     } catch (UsernameNotFoundException e) {
   	  assertThat(e.getMessage()).containsSequence(username);
   }
   ```

3. @Rule ExpectedException

   ```java
   @Rule
   public ExpectedException expectedException = ExpectedException.none();
   
   @Test
   public void findByUserNameFail() {
   	//expected
     String username = "random@email.com";
     expectedException.expect(UsernameNotFoundException.class);
     expectedException.expectMessage(Matchers.containsString(username));
   
     //when
     accountRepository.findByEmail(username);
   }
   ```

   - ExpectedException을 **public**으로 주입 받는다.
   - 먼저 발생될 Exception에 대해서 예상한다. (**후에 넣으면 안됨 반드시 먼저**)

<br><br>

### 스프링 시큐리티 기본 설정

- 스프링 시큐리티가 의존성에 들어있다면, 스프링 부트는 모든요청에 스프링 시큐리티용 자동설정을 적용한다.
  - 사용자를 임의로 인메모리로 만들어준다.

#### 계획

- 시큐리티 필터를 적용하지 않음
  - /docs/index.html
- 로그인 없이 접근 가능
  - GET /api.events
  - GET /api/events/{id}
- 로그인 접근
  - 나머지 다..
- 스프링 시큐리티 OAuth2.0 사용

#### 스프링 시큐리티 설정

- @EnableWebSecurity + extends WebSecurityCOnfigurerAdapter

  ```java
  @Configuration
  @EnableWebSecurity
  public class SecurityConfig extends WebSecurityConfigurerAdapter {
  }
  ```

  - 스프링 부트에서 설정하는 시큐리티 설정을 적용하지 않고 직접 설정한다.
  - 스프링 시큐리티의 기본설정을 상속받는다.

- PasswordEncoder

  ```java
  @Bean
  public PasswordEncoder passwordEncoder() {
    return PasswordEncoderFactories.createDelegatingPasswordEncoder();
  }
  ```

  - 패스워드 인코더 빈등록
  - PasswordEncoderFactories.createDelegatingPasswordEncoder();
    - prefix로 인코딩 방식 기입 및 prefix에 따라 매칭확인

- TokenStore

  ```java
  @Bean
  public TokenStore tokenStore() {
    return new InMemoryTokenStore();
  }
  ```

  - 토큰을 저장하는 곳. (OAuth 토큰을 저장)
  - InMemoryTokenStore

- AuthenticationManagerBean

  ```java
  @Bean
  @Override
  public AuthenticationManager authenticationManagerBean() throws Exception {
  	return super.authenticationManagerBean();
  }
  ```

  - AuthenticationManager를 빈으로 노출시켜준다.
    - AuthorizationServer나 ResourceServer에서 참조할 수 있도록

- configure(AuthenticationManagerBuidler auth)

  ```java
  @Override
  protected void configure(AuthenticationManagerBuilder auth) throws Exception {
  	auth.userDetailsService(accountService).passwordEncoder(passwordEncoder);
  }
  ```

  - AuthenticationManager를 어떻게 만들지 설정
    - UserDetailsService
    - PasswordEncoder 

- configure(WebSecurty web)

  ```java
  @Override
  public void configure(WebSecurity web) throws Exception {
  	web.ignoring().mvcMatchers("/docs/index.html");
  	web.ignoring().requestMatchers(PathRequest.toStaticResources().atCommonLocations());
  }
  ```

  - SecurityFilter를 적용할지 말지를 설정 (http로 가기전...)
  - PathRequest.toStaticResources().atCommonLocations()
    - 스프링 부트에서 지원(**Servlet**, Reactive)
    - 정적 리소스들에 대한 기본위치(favicon, image등..)

- configure(HttpSecurity http)

  ```java
  @Override
  protected void configure(HttpSecurity http) throws Exception {
    http.authorizeRequests()
      .mvcMatchers("/docs/index.html").permitAll()
      .requestMatchers(PathRequest.toStaticResources().atCommonLocations()).permitAll()
      ;
  }
  ```

  - SpirngSecurity로 들어와서 Filter는 탄다.
  - 그외 권한에 대한 설정을 준다.
  - "/docs/index.html", 정적 리소스 요청에 대해 모든접근을 허락하겠다.

> **configure(HttpSecurity http) vs configure(WebSecurty web)**
>
> - web에서 거를 경우, SecurityFilter자체를 타지 않고, http에서 거를 경우 SecurityFIlter는 적용되나 모든권한을 허용하는것이다.
> - 확인하기 위해 logging.level.org.springframework.security=DEBUG 설정 후 확인해 보자.
>   - web
>
>  ![restapi05-3](/images/restapi/restapi05-3.png)
>
> - http
>
>  ![restapi05-2](/images/restapi/restapi05-2.png)
>
> - 같은 요청에 대하여 http가 더 많은 시큐리티 관련 filterchain 로그가 찍히는 것을 확인 할 수 있었다.

<br><br>

### 스프링 시큐리티 폼 인증 설정

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
  http
    .anonymous()
    .and()
    .formLogin()
    .and()
    .authorizeRequests()
    .mvcMatchers(HttpMethod.GET, "/api/**").permitAll()
    .anyRequest().authenticated()
    ;
}
```

- 익명 사용자를 허용
- 폼 로그인 사용
  - loginpage() 등을 설정 가능하다.
- get 방식 api에 대해서 익명사용자를 허용한다.
  - permitAll()을 사용하도 인증 필요없이 접근 가능하게 할 수 있다.
- 나머지 요청(anyRequest)에 관하여는 인증을 요청한다.

<br><br>

### 스프링 시큐리티 OAuth 2 설정: 인증 서버 설정

#### GrantType

- Spring OAuth2.0이 인증하는 6가지 인증 방법중 하기 2가지 인증방식을 사용할 것이다.
  - password
    - 최초 oauth 토큰 발급
    - 다른 GrantType과 다르게 홉이 한번(요청과 응답이 한쌍)
      - 인증을 제공하는 **서비스 오너가 만든 클라이언트에서만 사용**
        - 인증정보를 보유하고 있는... 우리서비스에 가입한 유저에 대한 인증토큰
      - https://developer.okta.com/blog/2018/06/29/what-is-the-oauth2-password-grant
  - refreshToken

#### AuthorizationServer 설정

- @EnableAuthorizationServer
  - AuthorizationServer 설정
  - extends AuthorizationServerConfigurerAdapter와 함께 사용
  - configure 메소드들을 오버라이드 한다.

- configurer
  - security
    ```java
    @Override
    public void configure(AuthorizationServerSecurityConfigurer security) throws Exception {
        security.passwordEncoder(passwordEncoder);
    }
    ```

    - password encoder 설정

  - clients
    ```java
    @Override
    public void configure(ClientDetailsServiceConfigurer clients) throws Exception {
        clients.inMemory()
                .withClient("myApp")
                .authorizedGrantTypes("password", "refresh_token")
                .scopes("read", "write")
                .secret(passwordEncoder.encode("pass"))
                .accessTokenValiditySeconds(10 * 60)
                .refreshTokenValiditySeconds(6 * 10 * 60)
        ;
    }
    ```

    - clients 관련 내용 설정
    - id
    - password
    - grant_type
    - scope
    - accessTokenValiditySeconds(액세스 토큰 유지 시간)
    - refreshTokenValiditySeconds(리프레시 토큰 유지 시간)

  - endpoints
    ```java
    @Override
    public void configure(AuthorizationServerEndpointsConfigurer endpoints) throws Exception {
        endpoints.authenticationManager(authenticationManager)
                .userDetailsService(accountService)
                .tokenStore(tokenStore);
    }
    ```

    - AuthentiacationManager 설정
    - UserDetailsService설정
    - TokenStored설정

#### 응답결과

![restapi05-4](/images/restapi/restapi05-4.png)

<br><br>

### 리소스 서버 설정

- 리소스 서버는 이벤트 리소스를 제공하는 서버와 같이 있는게 맞다.
- 인증서버는 분리하는게 맞으나, 작은서비스는 같이 있어도 무방하다.

#### 설정

- @EnableResourceServer + extends ResourceServerConfigurerAdapter
- configure(ResourceServerSecurityConfigurer resources)

  ```java
  @Override
  public void configure(ResourceServerSecurityConfigurer resources) throws Exception {
  	resources.resourceId("event");
  }
  ```

- configure(HttpSecurity http)

  ```java
  @Override
  public void configure(HttpSecurity http) throws Exception {
    http
      .anonymous()
   	   .and()
      .authorizeRequests()
    	  .mvcMatchers(HttpMethod.GET, "/api/**")
      		.permitAll()
  	    .anyRequest()
    		  .authenticated()
      	.and()
      .exceptionHandling()
      	.accessDeniedHandler(new OAuth2AccessDeniedHandler());
  }
  ```

  - anonymous
  - GET /ap/** : permit all
  - POST : authenticated
  - PUT : authenticated
  - 에러처리
    - accessDeninedHandler(OAuth2AccessDeninedHandler())

- 기존엑 작성한 EventControllerTest 테스트 결과 GET을 제외한 나머지 테스트 실패
  ![restapi05-5](/images/restapi/restapi05-5.png)
  - -> 실패 테스트에 대해서 액세스 토큰을 가지고 요청하도록 테스트 수정

    ```java
    private String getBaererToken() throws Exception {
      //given
      Set<AccountRole> accountRoleSet = new HashSet<>();
      accountRoleSet.add(ADMIN);
      accountRoleSet.add(USER);
    
    	Account account = Account.builder()
    		.email("bong@email.com")
    		.password("bong")
    		.roles(accountRoleSet)
    		.build();
    	accountService.saveAccount(account);
    
    	String clientId = "myApp";
    	String clientSecret = "pass";
    
      //when && then
      ResultActions perform = this.mockMvc.perform(post("/oauth/token")
        .with(httpBasic(clientId, clientSecret))
        .param("username", "bong@email.com")
        .param("password", "bong")
        .param("grant_type", "password"));
      String responseBody = perform.andReturn().getResponse().getContentAsString();
      Jackson2JsonParser parser = new Jackson2JsonParser();
      String access_token = parser.parseMap(responseBody).get("access_token").toString();
      return "Bearer " + access_token;
    }
    ```

    ```java
    public void createEvent() {
    	mockMvc.perform(post("/api/events")
                      .header(HttpHeaders.AUTHORIZATION, getBaererToken())
                      .contentType(MediaType.APPLICATION_JSON_UTF8)
                      .accept(MediaTypes.HAL_JSON)
                      .content(objectMapper.writeValueAsString(event)))
        							.andDo(print());
    }
    ```

  - 단일 테스트는 성공하나 EventControllerTest 전체를 테스트할 때는 실패한다.
    - 같은 유저가 생성되면서 오류 발생
      - 어플리케이션 컨텍스트를 공유하고 인메모리 DB도 공유하고 있다.
      - 매 테스트 마다 DB를 비워준다.

        ```java
        @Before
        public void setUp() {
          this.eventRepository.deleteAll();
          this.accountRepository.deleteAll();
        }
        ```

<br><br>
### 문자열을 외부 설정으로 빼내기

#### @ConfiguationProperties

```java
@Component
@ConfigurationProperties(prefix = "my-app")
@Getter @Setter
public class AppProperties {

    @NotEmpty
    private String adminUsername;

    @NotEmpty
    private String adminPassword;

    @NotEmpty
    private String userUsername;

    @NotEmpty
    private String userPassword;

    @NotEmpty
    private String clientId;

    @NotEmpty
    private String clientSecret;
}
```

- 문자열을 외부설정에서 적용가능하도록 해준다.
- spring-boot-configuration-processor 의존성 추가 필요 (인텔리제이의 경우 레퍼런스 문서를 참조하는 알림이 발생한다.)

  ```xml
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-configuration-processor</artifactId>
      <optional>true</optional>
  </dependency>
  ```

- 필드값 적용 시, properties파일에서 자동완성이 가능하다.

  ![restapi05-6](/images/restapi/restapi05-6.png)

<br><br>

### 이벤트 API 점검

#### 이벤트 목록 조회

- /api/events

- 인증없이 GET요청

  ![restapi05-9](/images/restapi/restapi05-9.png)

- 인증 -> access token

  ![restapi05-7](/images/restapi/restapi05-7.png)

- 인증 후, GET요청

  ![restapi05-8](/images/restapi/restapi05-8.png)

- 모두 정상 작동

#### 이벤트 생성

- /api/events

  ![restapi05-11](/images/restapi/restapi05-11.png)

#### 이벤트 조회

- /api/events/{id}

  ![restapi05-10](/images/restapi/restapi05-10.png)

  - 로그인 했을 때 링크 부족
    - 이벤트 Manager인 경우 수정 링크를 제공하고 싶다.. 뒤에서 알아보자.

<br><br>

### 현재 사용자 조회

#### SecurityContext

- 자바 ThreadLocal 기반 구현으로 인증 정보를 담고 있다.
- 인증정보 꺼내기

  ```java
  Authentication authentication = SecurityContextHolder.getContext().getAuthentication();
  Object principal = authentication.getPrincipal();
  ```

  - 여기서 pricipal 객체는 UserDetailsService에서 리턴한 SpringSecurity.User이다.

#### @AuthenticationPricinpal

- 인증안한 경우에는 null
- 인증 한 경우에는 username, authorities 참조가능
- spring.security.User를 파라미터로 받을 수 있다.

  ```java
  @GetMapping
  public ResponseEntity queryEvents(Pageable pageable, PagedResourcesAssembler<Event> assembler, @AuthenticationPrincipal User currentUser) {
    Page<Event> page = eventRepository.findAll(pageable);
    PagedResources<Resource<Event>> pagedResources = assembler.toResource(page, e -> new EventResource(e));
    pagedResources.add(new Link("/docs/index.html#resources-events-list").withRel("profile"));
    if (currentUser != null) {
      pagedResources.add(linkTo(EventController.class).withRel("create-events"));
    }
  
    return ResponseEntity.ok(pagedResources);
  }
  ```

  ![restapi05-15](/images/restapi/restapi05-15.png)

  - spring.security.User를 상속받는 클래스를 구현하면 도메인 User를 얻을 수 있다.

    ```java
    @Getter
    public class AccountAdapter extends User {
    
        private Account account;
    
        public AccountAdapter(Account account) {
            super(account.getEmail(), account.getPassword(), authorities(account.getRoles()));
            this.account = account;
        }
    
        private static Collection<? extends GrantedAuthority> authorities(Set<AccountRole> roles) {
            return roles.stream().map(r -> new SimpleGrantedAuthority("ROLE_" + r.name())).collect(Collectors.toSet());
        }
    }
    ```

    ![restapi05-14](/images/restapi/restapi05-14.png)

    ![restapi05-13](/images/restapi/restapi05-13.png)

  - SpEL을 사용하면 도메인 User를 파라미터로 받을 수 있다.

    ![restapi05-12](/images/restapi/restapi05-12.png)

  - 위와 같은 작업을 annotation을 생성하여 코드를 줄일 수 있다.

    ```java
    @Target(ElementType.PARAMETER)
    @Retention(RetentionPolicy.RUNTIME)
    @AuthenticationPrincipal(expression = "account")
    public @interface CurrentUser {
    }
    ```

    - @Target : 파라미터에 사용하겠다.
    - @Retention : 런타임까지 유지 시키겠다.

    ![restapi05-16](/images/restapi/restapi05-16.png)

  - 인증을 안하고 접근하면....

    - 'anonymousUser'라는 문자열로 principal을 리턴한다. expression이 제대로 동작할 수 없다.
    - SpEL을 잘 활용하면 이러한 문제를 피할 수 있다.

      ```java
      @Target(ElementType.PARAMETER)
      @Retention(RetentionPolicy.RUNTIME)
      @AuthenticationPrincipal(expression = "#this == 'anonymousUser' ? null : account")
      public @interface CurrentUser {
      }
      ```

      - 현재 인증 정보가 'anonymousUser'라는 문자열이면 null을 리턴하고 아니면 account를 리턴하라.

<br><br>

### Event API 개선 : 출력값 제한하기

#### 문제점

- Event create 응답결과

  ![restapi05-17](/images/restapi/restapi05-17.png)

  - 매니저에 대한 과도한 정보를 가지고 있음 -> id값만을 응답하고 싶다.

### 해결

1. JsonSerializer 사용

   - ErrorSerializer에서 사용했던 JsonSerializer 사용(com.fasterxml.jackson.databind.JsonSerializer)

     ```java
     public class AccountSerializer extends JsonSerializer<Account> {
         @Override
         public void serialize(Account account, JsonGenerator jsonGenerator, SerializerProvider serializerProvider) throws IOException {
             jsonGenerator.writeStartObject();
             jsonGenerator.writeNumberField("id", account.getId());
             jsonGenerator.writeEndObject();
         }
     }
     ```

     - 그러나, @JsonComponent로 등록 시, 모든 Account에 대한 응답이 id값만 응답한다.
       -> @JsonComponent를 사용하여 등록하지 않는다.

   - 사용을 원하는곳에서만 @JsonSerialize를 사용하여 해결한다.

     ![restapi05-18](/images/restapi/restapi05-18.png)

   - 응답결과

     

2. VO or DTO등을 사용하여 Response를 만든다.

<br><br>