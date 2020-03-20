---
title: 스프링 MVC 설정06 - 포매터 추가하기, 도메인 클래스 컨버터 자동 등록
date: 2019-06-10 20:17:39
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

### 포매터 추가하기
- [Reference 문서](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/config/annotation/WebMvcConfigurer.html#addFormatters-org.springframework.format.FormatterRegistry-)

#### [Formatter](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/format/Formatter.html)
- Formatter<T>를 implements 하는 클래스를 생성한다.
    ![springwebmvc](/images/springwebmvc/springwebmvc06-1.png)
- Printer, Parser를 구현한다.
    - Printer: 해당 객체를 (Locale 정보를 참고하여) 문자열로 어떻게 출력할 것인가
    - Parser: 어떤 문자열을 (Locale 정보를 참고하여) 객체로 어떻게 변환할 것인가

#### Formatter를 추가하는 방법
1. WebMvcConfigurer의 addFormatters(FormatterRegistry) 메소드 정의
    ![springwebmvc](/images/springwebmvc/springwebmvc06-3.png)
2. 해당 포매터를 빈으로 등록(스프링 부트 사용시에만 가능하다.)
    ![springwebmvc](/images/springwebmvc/springwebmvc06-2.png)
<br>

### 도메인 클래스 컨버터 자동 등록
- 스프링 데이터 JPA는 스프링 MVC용 도메인 클래스 컨버터를 제공합니다.

#### 도메인 클래스 컨버터
- 스프링 데이터 JPA가 제공하는 Repository를 사용해서 ID에 해당하는 엔티티를 읽어옵니다.
    - findById()를 통해서 도메인 클래스로 컨버트해서 가져온다.
- 테스트 코드
    ![springwebmvc](/images/springwebmvc/springwebmvc06-4.png)
<br>


