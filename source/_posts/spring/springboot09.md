---
title: 스프링 부트 활용09 - 로깅
date: 2019-04-10 12:21:30
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
# 스프릥 부트 개념과 활용09(inflearn) - 백기선 
## Spring boot

<!-- more -->

### 스프링 부트 기본 로거 설정

#### 로깅 퍼사드 VS 로거
- 로깅 퍼사드
    - 로거를 바꿔낄 수 있게 해준다.
    - Commons Logging​, SLF4j
    
- 로거
    - 실제로 로그를 찍는 역할
    - JUL, Log4J2, ​Logback
    
#### 스프링 5에 로거 관련 변경 사항
- [reference 문서](https://docs.spring.io/spring/docs/5.0.0.RC3/spring-framework-reference/overview.html#overview-logging)
- Spring-JCL(자카르타 커먼스 로깅)
    - Commons Logging -> SLF4j or Log4j2
    - pom.xml에 exclusion 안해도 됨.(Spring boot 2 부터..)
    - Log4j2가 있으면 Log4j2를 사용한다.
    SLF4j만 있으면 SLF4j를 쓴다.
- 스프링 부트는 Commons Logging을 쓴다.
Commons Logging을 사용해도 SLF4j로 가고 Logback로 간다.
**결국 Logback이 로그를 찍는다.**
    
#### 스프링 부트 로깅
- 기본포맷
- \--debug (일부 핵심 라이브러리만 디버깅 모드로)
- \--trace (전부 다 디버깅 모드로)
- 컬러 출력: spring.output.ansi.enabled
- 파일 출력: logging.file 또는 logging.path
    - file : 파일
    - path : 디렉토리
- 로그 레벨 조정: logging.level.패지키 = 로그 레벨

### 커스터 마이징
- [reference 문서](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-logging.html)

#### 커스텀 로그 설정 파일 사용하기
- Logback: logback-spring.xml
    ```
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
    	<include resource="org/springframework/boot/logging/logback/base.xml"/>
    	<logger name="패키지_path" level="DEBUG"/>
    </configuration>
    ```
    ![springboot](/images/springboot/springboot09-1.png)

- Log4J2: log4j2-spring.xml
- JUL (비추): logging.properties
- Logback extension
    - logback-spring.xml을 사용해야한다.(logback.spring.xml 사용 시, 로딩이 일찍되어 사용불가)
    - 프로파일 <springProfile name=”프로파일”>
    - Environment 프로퍼티 <springProperty>
    
- 로거를 Log4j2로 변경하기
    - [reference문서](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-logging.html#howto-configure-log4j-for-logging)
    - exclusion logging(spring-boot-starter)
    ```
    <exclusions>
    		<exclusion>
    			<groupId>org.springframework.boot</groupId>
    			<artifactId>spring-boot-starter-logging</artifactId>
    		</exclusion>
    </exclusions>
    ```
    - 의존성 추가
    ```
    <dependency>
    	<groupId>org.springframework.boot</groupId>
    	<artifactId>spring-boot-starter-log4j2</artifactId>
    </dependency>
    ```
    - **앞서 말했던 스프링부트2버전대부터 pom.xml에 exclusion없이 사용할 수 있다고 하였는데, 그 경우에는 로깅퍼사드를 변경하는 경우이며, 이 경우에는 로거를 변경하는 경우이다.**
    ![springboot](/images/springboot/springboot09-3.png)
    
    
    
    