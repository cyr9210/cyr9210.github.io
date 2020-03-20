---
title: 스프링 부트 활용10 - Test
date: 2019-04-10 16:55:50
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
# 스프릥 부트 개념과 활용10(inflearn) - 백기선 
## Spring boot

<!-- more -->

### Test
- spring-boot-starter-test 의존성이 필요하다.
![springboot](/images/springboot/springboot10-2.png)

#### @SpringBootTest
- @RunWith(SpringRunner.class)랑 같이 사용해야 한다.
- 빈 설정 파일 필요없음. 빈은 알아서 찾는다.(@SpringBootApplication)

#### webEnvironment
- MOCK: mock servlet environment
    - 기본값
    - ServletContainer가 사용하지 않는다. 
    **내장 톰캣 구동 안 함.**
    - mocking한 servlet이 뜬다.
    - dispatcher servlet에 요청을 보내는것과 유사한 실험이 가능하다.
    - mockmvc라는 클라이언트가 필요하다.
    ![springboot](/images/springboot/springboot10-1.png)
    - stauts(), content() 메소드는 MockMvcResultMatcher에 있는것을 가져온다.
    ![springboot](/images/springboot/springboot10-3.png)
- RANDON_PORT, DEFINED_PORT
    - ServletContainer를 사용한다. 
    **내장 톰캣 구동함**
    - 테스트 TestRestTemplet 또는 **테스트용 web client**를 사용해야한다.
    
    - TestRestTemplet
    ![springboot](/images/springboot/springboot10-4.png)
        
    - 원본이 아닌 mocking을 하여 사용할 수 있다.
    ![springboot](/images/springboot/springboot10-5.png)
        
    - WebTestClient
        ![springboot](/images/springboot/springboot10-6.png)
        - Java5에 추가 된 RestClient
        - Async 하다.
        **테스트 코드에서도 웹클라이언트와 유사한 api를 사용할 수 있다.**
        
        - webflux 의존성 추가가 필요하다.
        ```
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-webflux</artifactId>
        </dependency>
        ```
        - **api가 보다 이해하기 편하다. 추천**
- NONE
    - 서블릿환경제공안함.
    
#### 슬라이스 테스트
- @SpringBootTest를 사용 시, @SpringBootApplication을 찾아서 모든 빈을 등록하고 @MockBean이 있으면 해당 빈만 교체한다.
- 모든 빈이 등록되는걸 원하지 않을 때 사용한다.
- 레이어 별로 잘라서 테스트하고 싶을 때 사용한다.
- @SpringBootTest를 대체하여 사용한다.

- @JsonTest
    - Json형태로 결과가 나올 때, 해당 Json과 관련 된 테스트에 사용
    - JacksonTester<도메인\>을 주입 받아 사용한다.
    - [reference문서](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-testing.html#boot-features-testing-spring-boot-applications-testing-autoconfigured-json-tests)
- @WebMvcTest
    ![springboot](/images/springboot/springboot10-7.png)
    - controller 관련 테스트할 때, 주로 사용
    - web과 관련된 것들만 빈으로 등록된다.
    - 필요한것들은 MockBean으로 등록하여 사용한다.
- @WebFluxTest
- @DataJpaTest
<br><br>

### 테스트 유틸
- **OutputCapture**
    - 로그를 포함 콘솔에 찍히는 모든것을 캡쳐한다.
    ![springboot](/images/springboot/springboot10-8.png)
- TestPropertyValues
- TestRestTemplate
- ConfigFileApplicationContextInitializer
<br><br>