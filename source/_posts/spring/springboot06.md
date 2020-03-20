---
title: 스프링 부트 활용06 - SpringApplication
date: 2019-04-03 12:50:08
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
# 스프릥 부트 개념과 활용06(inflearn) - 백기선 
## Spring boot

<!-- more -->

### [SpringApplication](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-spring-application.html#boot-features-spring-application)

#### 로그
- 기본 로그 레벨 INFO
![springboot](/images/springboot/springboot06-1.png)
    - debug 레벨로 변경
![springboot](/images/springboot/springboot06-2.png)
        - VM options : -Ddebug
        - Program arguments : - -debug
    - 변경 후
        - debug레벨로 로그가 찍힌다.
        - 어떠한 자동설정이 적용됬는지
        - 어떠한 자동설정이 왜 적용되지 않았는지
![springboot](/images/springboot/springboot06-4.png)

#### FailureAnalyzer
- 어플리케이션 에러가 발생 시, 보다 가시적으로 보이도록 해준다.
- 직접 등록도 가능하다.
- 거의 만질일이 없다.

#### 배너
- resources에 배너 설정파일을 만든다.(banner.txt | gif | jpg | png)
![springboot](/images/springboot/springboot06-5.png)
- banner파일은 resources 바로 아래 위치해야한다.
    - classpath 또는 spring.banner.location을 사용해서 다른 위치의 배너 파일을 사용할 수 있다.
- ${spring-boot.version} 등의 변수를 사용할 수 있다.
    - 일부 변수들은 MANIFEST파일이 있어야 사용할 수 있다.
- Banner 클래스 구현하고 SpringApplication.setBanner()로 설정 가능.
    ![springboot](/images/springboot/springboot06-7.png)
    - txt(승) vs 코딩 
- 배너끄는방법
    ![springboot](/images/springboot/springboot06-6.png)

#### Builder 패턴        
- SpringApplicationBuilder로 빌더 패턴 사용 가능
![springboot](/images/springboot/springboot06-8.png)

#### ApplicationEvent 등
- ApplicationContext를 만들기 전에 사용하는 리스너는 @Bean으로 등록할 수 없다.
    - ex) ApplicationStartingEvent
    Application이 시작할 때. 즉, ApplicationContext가 만들어지기 전
    - 이런 경우 직접 해주어야한다. (addListener 등)
    ![springboot](/images/springboot/springboot06-9.png)
    - Bean으로 등록이되는 리스너는 Bean등록만 해주면 된다.
        - ApplicationStartedListenr로 변경    
        ![springboot](/images/springboot/springboot06-10.png)
        
#### WebApplicationType 설정
- 기본적으로 서블릿으로 동작 (Spring WebMVC가 들어있다면)
    - 서블릿이 있으면 무조건 서블릿 동작
    - 서블릿이 없고 WebFlux가 있다면 REACTIVE 
    - none 웹 어플리케이션 동작안함
      
#### 애플리케이션 arguments 사용하기
- Application Arguments를 빈으로 등록해 주니까 가져다 쓸 수 있다.

#### 애플리케이션 실행한 뒤 뭔가 실행하고 싶을 때
- ApplicationRunner (추천) 또는 CommandLineRunner
- Program arguments는 사용가능하나 JVM options는 무시한다.
![springboot](/images/springboot/springboot06-11.png)
- 순서 지정 가능 @Order (숫자 낮은게 우선)
![springboot](/images/springboot/springboot06-12.png)
<br>