---
title: 스프링 부트 활용15 - 스프링 REST 클라이언트
date: 2019-04-17 16:27:48
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
# 스프릥 부트 개념과 활용15(inflearn) - 백기선 
## Spring boot

<!-- more -->

### 스프링 REST 클라이언트
- 스프링 부트가 REST 클라이언트 관련해서 직접적인 기능을 제공하는것은 아니다.
- REST 클라이언트는 스프링 프레임워크에서 지원한다.
- 스프링부트는 REST 클라이언트를 쉽게 사용할 수 있도록 빈을 등록해준다.
- REST Client, WebClient 빈을 등록하는것이 아니고, 빌더를 등록해준다.

#### RestTemplate 와 WebClient
- RestTemplate
    - Blocking I/O 기반의 **Synchronous API**
    - RestTemplateAutoConfiguration
    - 프로젝트에 spring-web 모듈이 있다면 RestTemplateBuilder를 빈으로 등록해준다.
    - [reference 문서](https://docs.spring.io/spring/docs/current/spring-framework-reference/integration.html#rest-client-access)

- WebClient
    - Non-Blocking I/O 기반의 **Asynchronous API**
    - WebClientAutoConfiguration
    - 프로젝트에 spring-webflux 모듈이 있다면 WebClient.Builder를 빈으로 등록해준다.
    - [reference 문서](https://docs.spring.io/spring/docs/current/spring-framework-reference/web-reactive.html#webflux-client)

- 비교 테스트
    - 사정 동일한 작업
        - RestController 생성 및 매핑
        ![springboot](/images/springboot/springboot15-2.png)
            - hello → 스레드 타임슬립 5초
            - world → 스레드 타임슬립 3초
    - RestTemplate
        - RestTemplate builder 주입받는다.(spring-boot-web 의존성 추가 시, 빈으로 등록된다.)
        - builder를 통해 build 한다.
        - 빌드된 RestTemplate를 통해 실행한다.
        - 결과
        ![springboot](/images/springboot/springboot15-1.png)
            1. 5초 뒤 hello 출력
            2. 3초 뒤 world 출력
            3. stopwatch가 꺼진다.
    - WebClient
        - Webflux를 의존성 추가한다.
        - builder를 통해 build 한다.
        - Mono를 세팅하고 subscribe() 한다.
        - 결과
        ![springboot](/images/springboot/springboot15-3.png)
            1. stopwatch 꺼진다.
            2. world 출력
            3. hello 출력
        - 비동기적인(Asyncronous한) 결과가 발생한다.
        - API들의 유연한 조합을 위해선 WebClient를 사용하는 것을 추천한다.
        - 좀 더 활용적인 예제를 보고 싶다면 .. [백선 youtube](https://www.youtube.com/watch?v=a4Hiz3pqizg&t=1918s)

#### 커스터 마이징
- RestTemplate
    - 기본으로 java.net.HttpURLConnection 사용.
    - 커스터마이징
        - 로컬 커스터마이징
            - 빌더 전에 baseUrl등 여러 설정이 가능하다.
        - 글로벌 커스터마이징
            - 전역적으로 사용하기 위해서..
            - RestTemplateCustomizer
            - 빌더 자체를 빈 재정의
    - aphache httpclient 사용하기
        - 의존성 주입
        ```
        <dependency>
            <groupId>org.apache.httpcomponents</groupId>
            <artifactId>httpclient</artifactId>
        </dependency>
        ```
        - RestTemplate.setRequestFactory();
        ![springboot](/images/springboot/springboot15-6.png)
        
- WebClient
    - 기본으로 Reactor Netty의 HTTP 클라이언트 사용.
    - 커스터마이징
        - 로컬 커스터마이징
            - 빌더 전에 baseUrl등 여러 설정이 가능하다.
            ![springboot](/images/springboot/springboot15-4.png)
        - 글로벌 커스터마이징
            - 전역적으로 사용하기 위해서..
            - WebClientCustomizer
                ![springboot](/images/springboot/springboot15-5.png)
            - 빌더 자체를 빈 재정의
        

    
    
