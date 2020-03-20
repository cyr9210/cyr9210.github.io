---
title: 스프링 부트 원리03 - 자동 설정
date: 2019-04-01 16:59:38
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
# 스프릥 부트 개념과 활용03(inflearn) - 백기선 
## Spring boot

<!-- more -->

### 자동 설정 이해
- @SpringBootApplication는 아래와 같이 구성되어있다.
    - @Configuration
    - @ComponentScan
    - @EnableAutoConfiguration
- @EnableAutoConfiguration (@SpringBootApplication 안에 숨어 있음) 
- 빈은사실두단계로나눠서읽힘
    - 1단계: @ComponentScan
    - 2단계: @EnableAutoConfiguration 
- @ComponentScan
    - @Component라는 어노테이션을 가진 클래스를 빈으로 등
    - @Configuration @Repository @Service @Controller @RestController- 
- @EnableAutoConfiguration 
    - spring.factories : 스프링부트 기본 설정들이 정의되어있다.
        - org.springframework.boot.autoconfigure.EnableAutoConfigu ration
            - 모두 @Configuration이 붙은 설정파일이다.
        - @Configuration
        - @ConditionalOnXxxYyyZzz(조건에 따라 빈으로 등록된다.)
<br><br>

### [자동 설정 만들기](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#boot-features-developing-auto-configuration) Starter와 Autoconfigure
- 자동설정 프로젝트 네이밍 패턴
    - Xxx-Spring-Boot-Autoconfigure : 자동 설정
    - Xxx-Spring-Boot-Starter : 필요한 의존성 정의
    - 그냥 하나로 만들고 싶을 때는? **Xxx-Spring-Boot-Starter**
    
#### 구현방법
- 의존성 추가
```
<dependencies>
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-autoconfigure</artifactId>
   </dependency>
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-autoconfigure-processor</artifactId>
       <optional>true</optional>
   </dependency>
</dependencies>

<dependencyManagement>
   <dependencies>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-dependencies</artifactId>
           <version>2.0.3.RELEASE</version>
           <type>pom</type>
           <scope>import</scope>
       </dependency>
   </dependencies>
</dependencyManagement>
```

- @Configuration 파일 작성
    ![springboot](/images/springboot/springboot02-10.png)
    - Holoman이라는 객체가 있다고 가정한다.(name, howlong 필드값 가짐)
    
- src/main/resource/META-INF에 spring.factories 파일 만들기
- spring.factories 안에 자동 설정 파일 추가
![springboot](/images/springboot/springboot02-11.png)
```
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
    Configuration 클래스 파일 위치 정의
```

- mvn install
    ![springboot](/images/springboot/springboot02-12.png)
    - 다른 메이븐 프로젝트에서도 사용할 수 있도록 로컬 메이븐 프로젝트에 설치한다.
    
    - 사용 시에는 아래의 내용을 사용한다.
    ![springboot](/images/springboot/springboot02-13.png)
    
- 다른 메이븐 프로젝트에서 설치한 의존성을 추가하고 사용한다.
![springboot](/images/springboot/springboot02-15.png)![springboot](/images/springboot/springboot02-14.png)

#### 문제점
![springboot](/images/springboot/springboot02-16.png)
- ComponentScan이 먼저 빈으로 등록하고 autoConfiguration으로 빈으로 등록한다.
따라서, 따로 빈으로 Holoman을 등록 시, 오류가 발생하거나, 덮어써진다.

#### 자동 설정 만들기 @ConfigurationProperties
- 위의 문제점을 해결할 수 있다.
- 덮어쓰기 방지하기
    ![springboot](/images/springboot/springboot02-17.png)
    - @ConditionalOnMissingBean
    - 해당 타입의 빈이 없으면 빈으로 등록한다.

- 빈 재정의 수고 덜기
    - 빈으로 매번 등록(재정의)하는 수고를 덜어준다.
    - 프로퍼티의 정의한 값으로 빈을 등록한다. (설정 프로젝트에서..)
    - @ConfigurationProperties(“properties에서 쓸 이름”)
    ![springboot](/images/springboot/springboot02-18.png)
    
    - 위의 네모박스를 클릭하면 자동완성을 위한 의존성 추가에 대한 정보가 나온다.
    ![springboot](/images/springboot/springboot02-19.png)
        - 의존성을 추가한다.
        ![springboot](/images/springboot/springboot02-21.png)
        
    - @EnableConfigurationProperties(@ConfigurationProperties를 사용한 클래)
    ![springboot](/images/springboot/springboot02-20.png)
    
    - 프로퍼티 키값 자동 완성
    ![springboot](/images/springboot/springboot02-22.png)
    <br>