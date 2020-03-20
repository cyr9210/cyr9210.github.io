---
title: 스프링 MVC 설정04 - 스프링 MVC 빈 설정
date: 2019-05-31 12:38:31
tags: SpringWebMvc
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

# 스프링 웹 MVC(inflearn) - 백기선 
## Springframework

<!-- more -->

### 스프링 MVC 빈 설정
#### @Configuration을 사용한 자바 설정 파일에 직접 @Bean을 사용해서 등록하기
- Dispatcher Servlet의 기본적략을 따른다면 아래와 같다.
    ![springmvc](/images/springwebmvc/springwebmvc04-1.png)
- 추가적인 설정이 있다면 아래와 같이 설정을 추가해준다.
    ![springmvc](/images/springwebmvc/springwebmvc04-2.png)
- 현재는 이러한 방법은 거의 쓰지 않는다. spring mvc에서 제공하는 방법을 사용한다.

#### @EanbleWebMvc
- 애노테이션 기반 스프링 MVC를 사용할 때 편리한 웹 MVC 기본 설정
- @Configuration이 있는 클래스에 @EanbleWebMvc를 설정해준다.
    ![springmvc](/images/springwebmvc/springwebmvc04-5.png)
- @EanbleWebMvc는 DelegatingWebMvcConfiguration을 import해준다.
    ![springmvc](/images/springwebmvc/springwebmvc04-4.png)
    - DispatcherServlet의 기본설정을 빈으로 등록해준다. (등록된 빈이 없다면..)
- 사용 시에는 반드시 servletContext를 설정해주어야한다.
    ![springmvc](/images/springwebmvc/springwebmvc04-3.png)
    - DelegatingWebMvcConfiguration에서 servletContext를 많이 참조하고 있기 때문에..

#### WebMvcConfigurer
- @EnableWebMvc가 제공하는 빈을 커스터마이징할 수 있는 기능을 제공하는 인터페이스

- WebMvcConfigurer를 implements 함으로서 설정이 가능하다.
    - viewresolver를 직접등록하지 않고 다음과 같이 설정 할 수 있다.
        ![springmvc](/images/springwebmvc/springwebmvc04-6.png)
<br><br>

### 스프링 부트의 MVC 설정
- 스프링 부트의 “주관”이 적용된 자동 설정이 동작한다.
    - JSP 보다 Thymeleaf 선호
    - JSON 지원
    - 정적 리소스 지원 (+ 웰컴 페이지, 파비콘 등 지원)
#### 스프링 MVC 커스터마이징
- application.properties
- @Configuration + Implements WebMvcConfigurer: 스프링 부트의 스프링 MVC 자동설정 + 추가 설정
- @Configuration + @EnableWebMvc + Imlements WebMvcConfigurer: 스프링 부트의 스프링 MVC 자동설정 사용하지 않음.
    

 