---
title: 스프링 부트 활용14 - 스프링 시큐리티 
date: 2019-04-16 22:18:31
tags: SpringBoot
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


![springboot](/images/springboot_logo.png)
# 스프릥 부트 개념과 활용14(inflearn) - 백기선 
## Spring boot
### 스프링 시큐리티

<!-- more -->

---
##### 참고 - 뷰 컨트롤러 사용
![springboot](/images/springboot/springboot14-1.png)
- Webconfigure 생성
    - @Configuration
    - implements
- @Overide - addViewControllers
    - registry.addViewController("<path>").setViewName("<View_name>");
- 추가적인 일이 있다면 @Controller를 쓰는것이 유리하다.
---

#### spring-boot-starter-security
- 스프링 시큐리티
    - 웹 시큐리티
    - 메소드 시큐리티
    - 다양한 인증 방법 지원
    - LDAP, 폼 인증, Basic 인증, OAuth, ...

- spring-boot-starter-security 의존성 추가

- 의존성을 추가하게 되면 정상작동하던 컨트롤러테스트가 실패하게 된다.
    ![springboot](/images/springboot/springboot14-2.png)
    - 401 Unauthorized
    - 스프링 부트가 제공하하는 스프링 시큐리티 자동설중 하나이다.
        - 모든요청에 스프링 시큐리티로 인해 인증이 필요하다.
        - Basic Authenticate, form 인증이 둘다 적용된다.
        (상기 이미지는 Basic Authenticate 오류)
            - Basic 인증은 Accept 헤더에 따라 달라진다. 
            - accept 헤더에 text/html을 보내게 되면 form 인증으로 넘어간다.
                ![springboot](/images/springboot/springboot14-3.png)
                - 보통 브라우저에서 accpet 헤더를 text/html을 쓴다. 

- 브라우저에서 루트를 요청해도 로그인으로 이동된다.
    - 인증정보가 없기 때문에 스프링 시큐리티에서 만들어준 로그인 form페이지로 이동된다.(스프링 부트 자동설정)
        ![springboot](/images/springboot/springboot14-5.png)

- 스프링 부트 시큐리티 자동 설정
    - SecurityAutoConfiguration
        - DefaultAuthenticationEventPublisher 빈 등록
            - 여러상황에 대해 이벤트를 발생시킨다.
            ex) 비빌번호 오류, 아이도 오류, ....
            - 다양한 인증 에러 핸들러 등록 가능
    - SpringBootWebSecurityConfiguration
        - WebSecurityConfigurerAdapter가 빈으로 등록되어 있지 않을때
        - WebSecurityConfigurerAdapter의 내용을 그대로 쓰고있다.
        (상속만하고 아무것도 정의되어 있지않음)
        ![springboot](/images/springboot/springboot14-6.png)
            - WebSecurityConfigurerAdapter 핵심내용
            ![springboot](/images/springboot/springboot14-7.png)
    - WebSecurityConfigurerAdapter를 상속하는 Configuration클래스(@Configuration 빈등록)를 만들면 거의 동일하게 동작하는 나만의 시큐리티 설정을 가질 수 있다.
    ![springboot](/images/springboot/springboot14-9.png)
    
    - UserDetailsServiceAutoConfiguration
        ![springboot](/images/springboot/springboot14-8.png)
        - **UserDetailsService**, AuthenticationManager, 
        AuthenticationProvider가 없을 때
        - inMemoryDetailsManager 객체를 만든다.
            - 기본 유저 객체만드는 역할
    - spring-boot-starter-security
        - 스프링 시큐리티 5.* 의존성 추가 
    - 모든 요청에 인증이 필요함.
    - 사용자 설정
        - 기본 Username: user
        - 기본 Password: 애플리케이션을 실행할 때 마다 랜덤 값 생성 (콘솔에 출력 됨.)
        ![springboot](/images/springboot/springboot14-4.png)
        - spring.security.user.name
        - spring.security.user.password
        
- test 방법
    - 의존성 추가
    ```
    <dependency>
        <groupId>org.springframework.security</groupId>
        <artifactId>spring-security-test</artifactId>
        <scope>test</scope>
    </dependency>
    ```
    - @WithMockUser 사용
    ![springboot](/images/springboot/springboot14-10.png)
    
    - 설정한 유저로 테스트
    ![springboot](/images/springboot/springboot14-11.png)
- **대부분의 서비스들은 UserDetailsService를 등록하고 사용하며, WebSecurityConfigurerAdapter를 이용하여 보다 쉽게 설정을 하기 때문에 스프링 부트에서 지원하는 시큐리티는 사실상 사용 할 일이 없다.** 

#### 커스터 마이징
- 웹 시큐리티 설정
    - WebSecurityConfigurerAdapter를 상속하는 Configuration을 빈으로 등록한다.
        이미지
        - 이 순간 스프링부트에서 자동 설정해주는 SecurityAutoConfiguration은 동작하지 않는다.
    - http configure을 오버라이딩 한다.
    ```
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
            .antMatchers("/", "/hello").permitAll()
            .anyRequest().authenticated()
            .and().formLogin()
            .and().httpBasic();
        }
    ```
        - 루트 및 "/hello" 요청에 대하여 접근을 허락한다.
        - 그 외 모든 요청에 대하여 인증을 한다.
            - formLogin 인증
            - httpBasic 인증
    - 결과
        - 루트
            ![springboot](/images/springboot/springboot14-13.png)
        - hello
            ![springboot](/images/springboot/springboot14-14.png)
        - my
            ![springboot](/images/springboot/springboot14-15.png)

- [UserDetailsServie 구현](https://docs.spring.io/spring-security/site/docs/current/reference/htmlsingle/#jc-authentication-userdetailsservice)
    - User Entity 및 Repository를 생성한다.
    - User관련 서비스를 생성한다.
    ![springboot](/images/springboot/springboot14-17.png)
    
    - implements UserDetailsService
        - @Override loadByUsername
        ![springboot](/images/springboot/springboot14-18.png)
            - username을 가지고 user객체를 불러와서 인증
            - return 될 UserDetails라는 인터페이스 구현체는 제 각각 구현되어있는 유저정보이다.
            스프링 시큐리티는 User라는 이름으로 UserDetails의 구현체를 제공한다.
            - User의 파라미터로 name, password를 넣고 세번째 인자로 authorities()메서드를 생성해서 넣어준다.
                ![springboot](/images/springboot/springboot14-16.png)
        - 더이상 임의의 User를 만들어주지 않는다.
        - PasswordEncoder를 설정하지 않아서 에러가 발생할 것으로 예상되어진다.

- PasswordEncoder 설정 및 사용
    - 인코딩을 안하고 로그인을 하면.. 오류 발생
    ![springboot](/images/springboot/springboot14-19.png)
    - 패스워드를 직접 DB에 넣으면 안된다. **인코딩 필수**
    - Security 설정했던 클래스에서 
    패스워드 인코더를 아무것도 인코딩하지 않는 NoOpPasswordEncoder를 리턴하는 PasswordEncoder를 빈으로 등록하면 로그인이 가능하다.
    **그러나, 절대로 이런 방법을 사용하면 안된다. 패스워드 인코더를 사용해야한다.**
    ![springboot](/images/springboot/springboot14-20.png)
    
    - 스프링 시큐리티 권장 패스워드 인코더 사용
        - 패스워드 인코더 빈 등록
        ```
        PasswordEncoder passwordEncoder = 
            PasswordEncoderFactories.createDelegatingPasswordEncoder()
        ```
        ![springboot](/images/springboot/springboot14-21.png)
        
        - 패스워드 인코딩
        ![springboot](/images/springboot/springboot14-22.png)
        
        - 인코딩 결과
        ![springboot](/images/springboot/springboot14-23.png)
            - bcrypt로 인코딩된 것이 보여진다.
        
        
    
    
             
    