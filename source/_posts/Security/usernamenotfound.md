---
title: 인증 시, UsernameNotfoundException 발생 안함 문제(BadCredentials Exception만 발생)
date: 2019-09-30 20:58:26
tags: [SpringSecurity, debug]
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

# SpringSecurity

## Debug

### 인증 시, UsernameNotfoundException 발생 안함 문제(BadCredentials Exception만 발생)
<!-- more -->
#### 조건
- SpringSecurity 사용
- 로그인 성공 시, 응답으로 authorization에 'success 200' 문자열을 응답
- 로그인 샐패 시, 응답으로 authorization에 'fail 에러코드' 문자열을 응답
  - ExceptionHandler 설정 및 ReturnCodes 는 미리 설정해두었지만 생략..
- UserDetailsService 및 PasswordEncoder를 생성 및 등록 -> DaoAuthenticationProvider 생성
- AuthenticateManager에 DaoAuthenticationProvider를 Provider로 등록

#### 과정
Exception 및 DTO/VO설정은 제외하고 진행하겠습니다.
- Account 생성
  ```
  @Entity
  @Table(name = "mt_account")
  @Getter
  @NoArgsConstructor
  @EqualsAndHashCode(callSuper = false, of = { "id" })
  public class Account {
  
      @Id
      @Column(name = "id", length = 50)
      @GenericGenerator(name = "system-uuid", strategy = "uuid2")
      @GeneratedValue(generator = "system-uuid")
      private String id;
  
      @Column(name = "email", length = 100, nullable = false)
      private String email;
  
      @Column(name = "password", length = 100, nullable = false)
      private String password;
  
      @Column(name = "name", length = 100)
      private String name;
  
      @ElementCollection(fetch = FetchType.EAGER)
      @Enumerated(EnumType.STRING)
      private Set<AccountRole> roles;
  
      @Builder
      public Account(String email, String password, String name) {
          Set<AccountRole> set = new HashSet<>();
          set.add(AccountRole.USER);
  
          this.email = email;
          this.password = password;
          this.name = name;
          this.roles = set;
      }
  ```
  - id
    - String으로 자동생성
  - email
    - username으로 사용하기 위해 nullable=false 설정
  - password
    - password로 사용하기 위해 nullable=false 설정
  - name

- 테스트 작성
  - ApplicationRunner를 빈으로 등록하여 미리 Account를 저장해 놓을것이다.
  - 성공
    ```
    @Test
    public void getAuth_success() throws Exception {
        AccountDTO.LoginRequestDTO loginRequest = AccountDTO.LoginRequestDTO.builder()
                .email("bong@email.com")
                .password("pass")
                .build();
    
        this.mockMvc.perform(post("/getAuthorization")
                .contentType(MediaType.APPLICATION_JSON_UTF8_VALUE)
                .content(objectMapper.writeValueAsString(loginRequest))
        )
                .andDo(print())
                .andExpect(jsonPath("authorization").value("true"))
        ;
    }
    ```
  - 실패 - password 오류
    ```
    @Test
    public void getAuth_bad_credentials_fail() throws Exception {
        AccountDTO.LoginRequestDTO loginRequest = AccountDTO.LoginRequestDTO.builder()
                .email("bong@email.com")
                .password("passs")
                .build();
    
        this.mockMvc.perform(post("/getAuthorization")
                .contentType(MediaType.APPLICATION_JSON_UTF8_VALUE)
                .content(objectMapper.writeValueAsString(loginRequest))
        )
                .andDo(print())
                .andExpect(jsonPath("authorization").value(Matchers.containsString(ReturnCodes.BAD_CREDENTIALS)))
                ;
    }
    ```
  - 실패 - username 오류
    ```
    @Test
    public void getAuth_bad_usernamenotfound_fail() throws Exception {
        AccountDTO.LoginRequestDTO loginRequest = AccountDTO.LoginRequestDTO.builder()
                .email("bong@email.com")
                .password("passs")
                .build();
    
        this.mockMvc.perform(post("/getAuthorization")
                .contentType(MediaType.APPLICATION_JSON_UTF8_VALUE)
                .content(objectMapper.writeValueAsString(loginRequest))
        )
                .andDo(print())
                .andExpect(jsonPath("authorization").value(Matchers.containsString(ReturnCodes.USER_NOTFOUND)))
        ;
    }
    ```

- UserDetailsService 구현한 AccountService 생성
  ```java
  @Service
  @RequiredArgsConstructor
  public class AccountService implements UserDetailsService {
  
      private final AccountRepository accountRepository;
  
      @Override
      public UserDetails loadUserByUsername(String email) throws UsernameNotFoundException {
          Optional<Account> byEmail = accountRepository.findByEmail(email);
          Account account = byEmail.orElseThrow(() -> new UsernameNotFoundException("not found"));
          return new User(account.getEmail(), account.getPassword(), authorities(account.getRoles()));
      }
  
      private Collection<? extends GrantedAuthority> authorities(Set<AccountRole> roles) {
          return roles.stream().map(r -> new SimpleGrantedAuthority("ROLE_" + r.name())).collect(Collectors.toSet());
      }
  }
  ```

  - username으로 email을 사용
  - authorities로 사용할 AccountRole을 미리 enum 으로 만들어 놓았다.

- passwordEncoder
  ```java
  @Bean
  public PasswordEncoder passwordEncoder() {
      return PasswordEncoderFactories.createDelegatingPasswordEncoder();
  }
  ```

- SpringSecurity 설정
  ```java
  @Configuration
  @EnableWebSecurity
  @RequiredArgsConstructor
  public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
  
      private final PasswordEncoder passwordEncoder;
      private final AccountService accountService;
  
      @Override
      public void configure(WebSecurity web) throws Exception {
          web.ignoring().antMatchers("/getAuthorization");
      }
  
      public DaoAuthenticationProvider daoAuthenticationProvider() {
          DaoAuthenticationProvider authenticationProvider = new DaoAuthenticationProvider();
          authenticationProvider.setUserDetailsService(accountService);
          authenticationProvider.setPasswordEncoder(passwordEncoder);
          return authenticationProvider;
      }
  
      @Override
      protected void configure(AuthenticationManagerBuilder auth) throws Exception {
          auth.authenticationProvider(daoAuthenticationProvider());
      }
  
      @Bean
      @Override
      public AuthenticationManager authenticationManagerBean() throws Exception {
          return super.authenticationManagerBean();
      }
  }
  ```

  - 테스트를 위해 시큐리티 필터 미적용("/getAuthorization")
  - DaoAuthenticationProvider 생성
    - 빈으로 등록한 passwordEncoder와 UserDetailsService의 구현체인 AccountService를 설정해준다.
  - 생성한 DaoAuthenticationProvider를 AuthenticationManager에 등록한다.
    - configure(AuthenticationManagerBuilder auth) 메소드를 통해 AuthenticationManger를 만들고 해당 AuthenticationManager를 사용한다.
    - configure(AuthenticationManagerBuilder auth) 메소드를 사용하지 않으면 빈으로 등록된 Provider를 찾아 AuthenticationManger를 만들고 사용한다.
  - AuthenticationManager를 빈으로 등록하기 위해 authenticationManagerBean() 을 오버라이딩하고 빈으로 등록해준다.

- Login 맵핑
  ```java
  @RestController
  @RequiredArgsConstructor
  public class AccountController {
  
      private final AuthenticationManager authenticationManager;
  
      @PostMapping("/getAuthorization")
      public ResponseEntity getAuthorization(@RequestBody AccountDTO.LoginRequestDTO requestDTO) {
          UsernamePasswordAuthenticationToken token = new UsernamePasswordAuthenticationToken(requestDTO.getEmail(), requestDTO.getPassword());
  
          try {
              Authentication authenticate = authenticationManager.authenticate(token);
              ResponseVO response = ResponseVO.builder().authorization("success " + ReturnCodes.OK).build();
              return ResponseEntity.ok(response);
          } catch (AuthenticationException e) {
              if (e instanceof BadCredentialsException)
                  throw new BadCredentials();
  
              if (e instanceof UsernameNotFoundException)
                  throw new AccountNotFound();
          }
          ResponseVO fail = ResponseVO.builder().authorization("fail").build();
          return ResponseEntity.ok(fail);
      }
  }
  ```

  - Exception발생 시, @ExceptionHandler, @ResponseBody를 사용하여 Json으로 응답하도록 하였다. (생략)

- 테스트 실행
  - 성공
    ![notfound1](/images/springsecurity/usernamenotfoundexception/notfound1.png)

  - 실패 - password 오류
    ![notfound2](/images/springsecurity/usernamenotfoundexception/notfound2.png)

  - 실패 - username 오류
    ![notfound3](/images/springsecurity/usernamenotfoundexception/notfound3.png)

#### 문제점

- AuthenticationManager를 빈으로 등록 후, 다른 서비스에서 authenticateManager.authenticate()를 사용하여 인증할 때 username이 틀려도 password가 틀려도 **모두 BadCredentialsException만 발생**

#### Debug

- Controller
  ![notfound4](/images/springsecurity/usernamenotfoundexception/notfound4.png)

- WebSecurityConfigurerAdapter.authentiacate(Autentication authentication)
  ![notfound5](/images/springsecurity/usernamenotfoundexception/notfound5.png)
  - delegate(AuthenticationManager)가 있으면 사용
  - 없으면 빌더로 가져오기

- ProviderManager.authenticate(Authentication authentication)
  ![notfound6](/images/springsecurity/usernamenotfoundexception/notfound6.png)
  - this.providers : AnonymousProvider (어떤 프로파이더인지는 잘모르겠다.)
  - parents providers : DaoAuthenticationProvider (설정했던 provider)
  - AnonymousProvider는 지원하지 않아서 지나감.
  
  ![notfound7](/images/springsecurity/usernamenotfoundexception/notfound7.png)
  - result(파라미터로 넘긴 authentication.getClass())와 parent가 있으면 parent.authenticate(authenticate); 재귀함수 호출
  
  ![notfound8](/images/springsecurity/usernamenotfoundexception/notfound8.png)
  - 다시 왔을때는 provider가 설정한 DaoAuthenticationProvider인것을 알 수있다.
  - provider.authenticate(authentication);을 호출한다.
  
  ![notfound9](/images/springsecurity/usernamenotfoundexception/notfound9.png)
  - AbstractUserDetailsAuthenticationProvider
  - retrieveUser() 안에서 UserDetailsService.loadUserByUsername(String email); 을 호출한다.
    ![notfound10](/images/springsecurity/usernamenotfoundexception/notfound10.png)
  - **hideUserNotFoundExceptions 를 확인해서 BadCredentialsException을 던지고 있다.**

hideUserNotFoundExceptions을 확인해보자.
- 기본적으로 true
  ![notfound11](/images/springsecurity/usernamenotfoundexception/notfound11.png)

- setHideUserNotFoundException을 통해 false로 변경 될 수 있는데.. 이건 DaoAuthenticationProvider 설정 시에 설정할 수 있다.
  ![notfound12](/images/springsecurity/usernamenotfoundexception/notfound12.png)
  
  ![notfound13](/images/springsecurity/usernamenotfoundexception/notfound13.png)

#### 이렇게 설정 되어있는 이유는 ?

![notfound14](/images/springsecurity/usernamenotfoundexception/notfound14.png)
- **아이디 체크와 비밀번호 체크를 따로 exception을 날리는거보다 BadCredential Exception하나만 날리는것이 보다 더 보안이 강하기 때문에...**

#### 해결

![notfound15](/images/springsecurity/usernamenotfoundexception/notfound15.png)
- AuthenticationProvider 생성 시에 setHideUserNotFoundException(false); 로 설정한다.
- 그러나 더 강한 보안을 위해서라면 모두 BadCredential을 호출하는것이 맞다.
<br><br>
