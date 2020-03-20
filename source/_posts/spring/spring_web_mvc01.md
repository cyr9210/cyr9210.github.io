---
title: 스프링 MVC 동작 원리01 - 스프링 MVC 소개, 서블릿
date: 2019-04-23 20:21:40
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

백기선님의 스프링 웹 MVC강의를 수강하고 공부내용을 정리하고 있습니다.
해당 강좌는 스프링 MVC에 대하여 핵심원리, 설정, 활용으로 나누어 강의해주고 있습니다.
모든 예제는 [github](https://github.com/cyr9210/SpringWebMVc-study)에 올리고 있습니다.

### 스프링 MVC 소개
- 스프링 MVC
    - M: 모델
        - 도메인 객체 또는 DTO로 화면에 전달할 또는 화면에서 전달 받은 데이터를 담고 있는 객체.
        - 평범한 자바 객체 POJO
        - ex)
            ![springmvc](/images/springwebmvc/springwebmvc01-2.png)
    - V: 뷰
        - 데이터를 보여주는 역할. 다양한 형태로 보여줄 수 있다. HTML, JSON, XML, ...
        - HTML. JSP, 타임리프, ...
        - 스프링부트는 기본적으로 동적인 뷰를 resources - templates디렉토리에서 찾는다.
        - ex)
            ![springmvc](/images/springwebmvc/springwebmvc01-3.png)
    - C: 컨트롤러
        - 사용자 입력을 받아 모델 객체의 데이터를 변경하거나, 모델 객체를 뷰에 전달하는 역할.
        - 입력값 검증
        - 입력 받은 데이터로 모델 객체 변경
        - 변경된 모델 객체를 뷰에 전달
        - @Controller 사용한 클래스
        - ex)
            ![springmvc](/images/springwebmvc/springwebmvc01-1.png)

>**tip**
- Java9는 적은수의 요소로 편리하게 Collections의 인스턴스를 생성 할 수 있는 새로운 정적 팩토리를 지원한다.
- 다만 정적 팩토리 메소드로 작성된 콜렉션은 불변이므로, 요소 또는 null을 add/put 하려고 하면 java.lang.UnsupportedOperationException 또는 java.lang.NullPointerException 을 발생 시킨다.
 ```
 List<String> immutableList = List.of("one","two","three");
 Set<String> immutableSet = Set.of("one","two","three");
 Map<Integer,String> immutableMap = Map.of(1,"one",2,"two",3,"three")
 ```

- MVC 패턴의 장점
    - 동시 다발적(Simultaneous) 개발 - 백엔드 개발자와 프론트엔드 개발자가 독립적으로 개발을 진행할 수 있다.
    - 높은 결합도 - 논리적으로 관련있는 기능을 하나의 컨트롤러로 묶거나, 특정 모델과 관련있는 뷰를 그룹화 할 수 있다.
    - 낮은 의존도 - 뷰, 모델, 컨트롤러는 각각 독립적이다.
    - 개발 용이성 - 책임이 구분되어 있어 코드 수정하는 것이 편하다.
    - 한 모델에 대한 여러 형태의 뷰를 가질 수 있다.
- MVC 패턴의 단점
    - 코드 네비게이션 복잡함
    - 코드 일관성 유지에 노력이 필요함
    - 높은 학습 곡선
<br><br>

### 서블릿
- 서블릿 (Servlet)
    - 자바 엔터프라이즈 에디션은 웹 애플리케이션 개발용 스팩과 API 제공.
    - 요청 당 쓰레드 (만들거나, 풀에서 가져다가) 사용
    - 그 중에 가장 중요한 클래스중 하나가 HttpServlet.
    
- 서블릿 등장 이전에 사용하던 기술인 CGI (Common Gateway Interface) 
    - 요청 당 프로세스를 만들어 사용

- 서블릿의 장점 (CGI에 비해)
    - 빠르다.
    - 플랫폼 독립적
    - 보안
    - 이식성
    
- 서블릿 엔진 또는 서블릿 컨테이너 (톰캣, 제티, 언더토, ...)
    - 세션 관리
    - 네트워크 서비스
    - MIME 기반 메시지 인코딩 디코딩
    - 서블릿 생명주기 관리
    - ...
    
- 우리가 만든 서블릿 어플리케이션은 직접 사용할 수 없으며, 반드시 서블릿 컨테이너를 이용하여 사용할 수 있다.

- 서블릿 생명주기
![springmvc](/images/springwebmvc/springwebmvc01-4.png)
    - 서블릿 컨테이너가 서블릿 인스턴스의 init() 메소드를 호출하여 초기화 한다.
        - 최초 요청을 받았을 때 한번 초기화 하고 나면 그 다음 요청부터는 이 과정을 생략한다.
    - 서블릿이 초기화 된 다음부터 클라이언트의 요청을 처리할 수 있다. 각 요청은 별도의 쓰레드로 처리하고 이때 서블릿 인스턴스의 service() 메소드를 호출한다.
        - 이 안에서 HTTP 요청을 받고 클라이언트로 보낼 HTTP 응답을 만든다.
        - service()는 보통 HTTP Method에 따라 doGet(), doPost() 등으로 처리를 위임한다.
        - 따라서 보통 doGet() 또는 doPost()를 구현한다.
    - 서블릿 컨테이너 판단에 따라 해당 서블릿을 메모리에서 내려야 할 시점에 destroy()를 호출한다
<br><br>

### 서블릿 어플리케이션 개발
#### 가장 기본적이고 원시적인 서블릿 어플리케이션 개발
- 메이븐 프로젝트 생성
    - archetype 사용(메이븐이 만들어 놓은 프로젝트 틀)
    ![springmvc](/images/springwebmvc/springwebmvc01-6.png)
    - webapp archetype 사용
    <img src="/images/springwebmvc/springwebmvc01-7.png" width="40%">
    
- Java Servlet API 의존성 추가
    ```
    <dependency>
          <groupId>javax.servlet</groupId>
          <artifactId>javax.servlet-api</artifactId>
          <version>4.0.1</version>
          <scope>provided</scope>
    </dependency>
    ```
    - provided 스코프는 코딩시점에서는 사용이 가능하나, runtime시점에는 사용할 수 없다.(어디선가 제공할것이다.
        - 톰캣 컨테이너에서 제공(서블릿 컨테이너)

- 톰캣 설치 및 추가
    - artifacts 는 war-exploded로 추가한다.
        ![springmvc](/images/springwebmvc/springwebmvc01-9.png)
        - war는 압축된것 exploded 풀린상태
    - web.xml에 서블릿 클래스 추가 및 매핑 추가
    ![springmvc](/images/springwebmvc/springwebmvc01-8.png)
<br>

### 서블릿 리스너와 필터
#### 서블릿 리스너
- 웹 애플리케이션에서 발생하는 주요 이벤트를 감지하고 각 이벤트에 특별한 작업이 필요한 경우에 사용할 수 있다.
    - 서블릿 컨텍스트 수준의 이벤트
        - 컨텍스트 라이프사이클 이벤트
        - 컨텍스트 애트리뷰트 변경 이벤트
    - 세션 수준의 이벤트
        - 세션 라이프사이클 이벤트
        - 세션 애트리뷰트 변경 이벤트

- 예제
    - 서블릿 리스너 생성
        ![springmvc](/images/springwebmvc/springwebmvc01-11.png)
        - implements ServletContextListener
    - web.xml에 리스너 등록
        ![springmvc](/images/springwebmvc/springwebmvc01-12.png)
    - 리스너 적용 및 결과
        ![springmvc](/images/springwebmvc/springwebmvc01-13.png)
     
#### 서블릿 필터
- 들어온 요청을 서블릿으로 보내고, 또 서블릿이 작성한 응답을 클라이언트로 보내기 전에 특별한 처리가 필요한 경우에 사용할 수 있다.
- 체인 형태의 구조
    <img src="/images/springwebmvc/springwebmvc01-10.png" width="40%">

- 예제
    - 서블릿 필터 생성
        - implements Filter(javax.servlet)
    - doFilter 설정
    ![springmvc](/images/springwebmvc/springwebmvc01-15.png)
        - 다음 필터 적용 또는 마지막 필터인 경우 서블릿은 연결해준다.
    
    - web.xml에 필터 등록 및 매핑
    ![springmvc](/images/springwebmvc/springwebmvc01-14.png)
        - 매핑은 servlet-name 또는 url-pattern 으로 매핑할 수 있다.
        - 리스너 - 필터 - 서블릿 순서로 init destroy는 역순
<br>   

