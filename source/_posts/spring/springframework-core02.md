---
title: 스프링 핵심기술02 - Resource / Validation
date: 2019-03-21 02:37:57
tags: Springframework
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
![springf](/images/springframwork-logo.png)
# 스프링 프레임워크 핵심기술02(inflearn) - 백기선 
## Springframework
<!-- more -->
### [Resource](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/io/Resource.html) 추상화
- org.springframework.core.io.Resource
- 스프링 내부에서 많이 사용하는 인터페이스.
- java.net.URL을 추상화 한 것.
    - 추상화 한 이유
        - 클래스패스 기준으로 리소스 읽어오는 기능 부재
        - ServletContext를 기준으로 상대 경로로 읽어오는 기능 부재
        - 새로운 핸들러를 등록하여 특별한 URL 접미사를 만들어 사용할 수는 있지만 구현이 복잡하고 편의성 메소드가 부족하다.
- 상속 받은 인터페이스
- 주요 메소드
    - getInputStream()
    - exitst()
    - isOpen()
    - getDescription(): 전체 경로 포함한 파일 이름 또는 실제 URL

#### 구현체
- UrlResource: java.net.URL 참고, 기본으로 지원하는 프로토콜 http, https, ftp, file, jar.
- ClassPathResource: 지원하는 접두어 classpath:
- FileSystemResource
- ServletContextResource: 웹 애플리케이션 루트에서 상대 경로로 리소스 찾는다.
    - 대부분 ServletContextResource를 사용할 것이다.   

#### 리소스 읽어오기
- Resource의 타입은 locaion 문자열과 ApplicationContext의 타입에 따라 결정 된다.
    - ClassPathXmlApplicationContext -> ClassPathResource
    - FileSystemXmlApplicationContext -> FileSystemResource
    - WebApplicationContext -> ServletContextResource
- ApplicationContext의 타입에 상관없이 리소스 타입을 강제하려면 java.net.URL 접두어(+ classpath:)중 하나를 사용할 수 있다.
    - classpath:me/whiteship/config.xml -> ClassPathResource
    - file:///some/resource/path/config.xml -> FileSystemResource
    - **좀 더 명시적으로 사용하기 위해 이 방법을 추천한다.**

#### 예제
![springcore2](/images/springc/springcore02-01.png)
- WebApplicationContext인것으로 보아 기본적으로 ServletContextResource 이어야한다.
- classpath: 라는 prefix를 사용했기 때문에 ClassPathResource가 나온다.

여기서 만약 classpath라는 prefix를 지운다면...
![springcore2](/images/springc/springcore02-02.png)
- ServletContextResource 가 나온다.
- 스프링부트가 띄어주는 기본적인 내장 톰캣은 ContextPath가 지정되어있지 않다.   
때문에 resource파일이 존재하지 않는다고 결과가 나온다.
- **스프링부트 기반으로 어플리케이션을 작성할 시, 특히 JSP를 사용하지 않는경우 classpath 접두어 사용을 추천한다.**
<br><br>

### [Validation](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/validation/Validator.html) 추상화
- 애플리케이션에서 사용하는 객체 검증용 인터페이스.
- 어떤한 계층과도 관계가 없다. → 모든 계층(웹, 서비스, 데이터)에서 사용해도 좋다.
- DataBinder에 들어가 바인딩 할 때 같이 사용되기도 한다.

#### 인터페이스
![springcore2](/images/springc/springcore02-05.png)

![springcore2](/images/springc/springcore02-03.png)

![springcore2](/images/springc/springcore02-04.png)
- boolean supports(Class clazz): 어떤 타입의 객체를 검증할 때 사용할 것인지 결정한다.(검증 대상)
- void validate(Object obj, Errors e): 실제 검증 로직을 이 안에서 구현한다.(검증 내용)
    - 구현할 때 ValidationUtils 사용하면 편리하다.
    - ValidationUtils를 사용하지 않고, rejectValue를 통해서 직접 만들어 사용할 수 있다.   
    특정필드값에 대한 검증이 아니라면 reject사용
    ![springcore2](/images/springc/springcore02-06.png)    

#### 스프링 부트 2.0.5 이상 버전을 사용할 때
![springcore2](/images/springc/springcore02-07.png)
- JSR-380([Bean Validation 2.0.1](https://docs.jboss.org/hibernate/beanvalidation/spec/2.0/api/)) 구현체로 hibernate-validator 사용.()
- [LocalValidatorFactoryBean](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/validation/beanvalidation/LocalValidatorFactoryBean.html) 빈으로 자동 등록
<br><br>