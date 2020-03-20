---
title: 스프링 MVC 설정07 - 핸들러 인터셉터, 리소스 핸들러, HTTP 메세지 컨버터, 기타 설정
date: 2019-06-24 20:48:44
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

# 스프링 웹 MVC(inflearn) - 백기선 
## Springframework

<!-- more -->

### 핸들러 인터셉터
- [reference](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/HandlerInterceptor.html) 

#### HandlerInterceptor
- 핸들러 맵핑에 설정할 수 있는 인터셉터
- 핸들러를 실행하기 전, 후(아직 랜더링 전) 그리고 완료(랜더링까지 끝난 이후) 시점에 부가 작업을 하고 싶은 경우에 사용할 수 있다.
- 여러 핸들러에서 반복적으로 사용하는 코드를 줄이고 싶을 때 사용할 수 있다.
    - 로깅, 인증 체크, Locale 변경 등...

#### boolean preHandle(request, response, **handler**)
- 핸들러 실행하기 전에 호출 됨
- 핸들러에 대한 정보를 사용할 수 있기 때문에 서블릿 필터에 비해 보다 세밀한 로직을 구현할 수 있다.
- 리턴값으로 계속 다음 인터셉터 또는 핸들러로 요청,응답을 전달할지(true) 응답 처리가 이곳에서 끝났는지(false) 알린다.

#### void postHandle(request, response, **modelAndView**)
- 핸들러 실행이 끝나고 아직 뷰를 랜더링 하기 이전에 호출 됨
- “뷰"에 전달할 추가적이거나 여러 핸들러에 공통적인 모델 정보를 담는데 사용할 수도 있다.
- 이 메소드는 인터셉터 역순으로 호출된다.
    - 예) preHandle 1 -> preHandle 2 -> 요청처리 -> postHandler2 -> postHandler1
- 비동기적인 요청 처리 시에는 호출되지 않는다.
    - asyncHandlerInterceptor에서 다룬다. 

#### void afterCompletion(request, response, handler, ex)
- 요청 처리가 완전히 끝난 뒤(뷰 랜더링 끝난 뒤)에 호출 됨
- preHandler에서 true를 리턴한 경우에만 호출 됨
- 이 메소드는 인터셉터 역순으로 호출된다.
- 비동기적인 요청 처리 시에는 호출되지 않는다.

#### 서블릿 필터와 비교
- 서블릿 보다 구체적인 처리가 가능하다.(핸들러, 뷰가 제공된다.)
- 서블릿은 보다 일반적인 용도의 기능을 구현하는데 사용하는게 좋다.
    - 예) Cross-site scripting (XSS) attack 방지 → 뷰 or 핸들러의 정보는 필요없다..
        - naver에서 만든 LUCY필터가 있다.

#### 구현
![springwebmvc](/images/springwebmvc/springwebmvc07-2.png)
- implements HandlerInterceptor

#### 핸들러 인터셉터 등록하기
![springwebmvc](/images/springwebmvc/springwebmvc07-1.png)
- .order로 순서를 정할수 있다.
    - add 순으로 등록된다.
- .addPathPattern을 사용하여 원하는 핸들러에만 적용 가능

### testcode
![springwebmvc](/images/springwebmvc/springwebmvc07-3.png)
- AnotherInterceptor는 PathPattern에 걸리지 않아서 제외
<br><br>

### 리소스 핸들러
- 이미지, 자바스크립트, css, html 파일 같은 리소스를 처리하는 핸들러
- [reference 문서](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/config/annotation/WebMvcConfigurer.html#addResourceHandlers-org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry-)

#### Default Servlet
- 서블릿 컨테이너가 기본으로 제공하는 서블릿으로 정적인 리소를 처리할 때, 사용한다.
    - 예)..[톰캣문서](https://tomcat.apache.org/tomcat-9.0-doc/default-servlet.html)

#### 스프링 MVC 리소스 핸들러 맵핑 등록
- 가장 낮은 우선 순위로 등록.
    - 다른 핸들러 맵핑이 “/” 이하 요청을 처리하도록 허용하고
    - 최종적으로 리소스 핸들러가 처리하도록.
- 스프링 부트를 사용하는 경우 

#### 리소스 핸들러 설정
![springwebmvc](/images/springwebmvc/springwebmvc07-4.png)
- **file시스템으로 맵핑 시, classpath: 대신 file:로 넣어준다.**
- 어떤 요청 패턴을 지원할 것인가
- 어디서 리소스를 찾을 것인가
- 캐싱
- ResourceResolver: 요청에 해당하는 리소스를 찾는 전략
    - 캐싱, 인코딩(gzip, brotli), WebJar, ...
- ResourceTransformer: 응답으로 보낼 리소스를 수정하는 전략
    - 캐싱, CSS 링크, HTML5 AppCache, ...
- ResourceResolver, ResourceTransformer 관련 내용은 [reference](https://www.slideshare.net/rstoya05/resource-handling-spring-framework-41)를 참고
    
- 스프링 부트
    - 기본 정적 리소스 핸들러와 캐싱 제공
<br><br>

### HTTP 메세지 컨버터
- 요청 본문에서 메시지를 읽어들이거나(@RequestBody), 응답 본문에 메시지를 작성할 때(@ResponseBody) 사용한다.
- RestController는 모든 메소드에 @ResponseBody가 생략되어있는 것.
- @RequestBody
    - 요청 본문의 문자열을 받는다.
    - 요청 본문의 문자열을 변환
        - xml, JSON등을 객체로 변환

#### 기본 HTTP 메시지 컨버터
- 바이트 배열 컨버터
- 문자열 컨버터
- Resource 컨버터
- Form 컨버터 (폼 데이터 to/from MultiValueMap<String, String>)
    - Map → 폼 데이터 or 폼 데이터 → Map 

#### 의존성이 있을 때 등록이되는 메세지 컨버터
- xml
    - (JAXB2 컨버터)
- Json    
    - (Jackson2 컨버터)
    - (Jackson 컨버터)
    - (Gson 컨버터)
- feed
    - (Atom 컨버터)
    - (RSS 컨버터)
- ...

#### 설정 방법
- 기본으로 등록해주는 컨버터에 새로운 컨버터 추가하기: extendMessageConverters
    ![springwebmvc](/images/springwebmvc/springwebmvc07-5.png)
    - [reference 문서](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/config/annotation/WebMvcConfigurer.html#extendMessageConverters-java.util.List-)
- 기본으로 등록해주는 컨버터는 다 무시하고 새로 컨버터 설정하기: configureMessageConverters
    ![springwebmvc](/images/springwebmvc/springwebmvc07-6.png)
    - [reference 문서](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/config/annotation/WebMvcConfigurer.html#configureMessageConverters-java.util.List-)
- 의존성 추가로 컨버터 등록하기 (제일 많이 사용하게 될 것이며 추천한다.)
    - 메이븐 또는 그래들 설정에 의존성을 추가하면 그에 따른 컨버터가 자동으로 등록된다.
    - WebMvcConfigurationSupport
    - (이 기능 자체는 스프링 프레임워크의 기능임, 스프링 부트 아님.)
[참고](https://www.baeldung.com/spring-httpmessageconverter-rest)

#### JSON용 HTTP 메세지 컨버터 
- 스프링부트를 사용하지 않는 경우 
    - 사용하고 싶은 JSON 라이브러리를 의존성으로 추가
        - GSON
        - JacksonJSON
        - JacksonJSON 2

- 스프링 부트를 사용하는 겨우
    - 기본적으로 JacksonJSON2가 의존성에 들어있다.
    - 즉, JSON용 HTTP 메세지 컨버터가 등록되어있다.

- ObjectMapper는 스프링부트에서 의존성을 자동으로 주입해준다.(spring-boot-starter-web 추가 시)
- TestCode
    ![springwebmvc](/images/springwebmvc/springwebmvc07-7.png)
- JsonPath문법
    - https://github.com/json-path/JsonPath
    - http://jsonpath.com/
    
#### XML용 HTTP 메세지 컨버터
- OXM(Object-XML Mapper) 라이브러리 중에 스프링이 지원하는 의존성 추가
    - JacksonXML
    - JAXB
    
- Mashaller : 객체 → xml
- UnMashaller : xml → 객체
    
- 스프링 부트가 기본으로 관련 의존성을 추가해주지 않는다.
    
- 의존성 추가
    ```
    <dependency>
        <groupId>javax.xml.bind</groupId>
        <artifactId>jaxb-api</artifactId>
    </dependency>
    <dependency>
        <groupId>org.glassfish.jaxb</groupId>
        <artifactId>jaxb-runtime</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-oxm</artifactId>
        <version>${spring-framework.version}</version>
    </dependency>
    ```
    
- Marshaller 등록
    - xml로 변환할 클래스의 패키지이름을 설정해준다.
        ![springwebmvc](/images/springwebmvc/springwebmvc07-8.png)
    - 도메인 클래스에 @XmlRootElement 애노테이션 추가
        ![springwebmvc](/images/springwebmvc/springwebmvc07-9.png)
        
- TestCode
    ![springwebmvc](/images/springwebmvc/springwebmvc07-10.png)
- Xpath 문법
    - https://www.w3schools.com/xml/xpath_syntax.asp
    - https://www.freeformatter.com/xpath-tester.html
<br><br>

### 그밖에 WebMvcConfigurer 설정
- [reference 문서](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/config/annotation/WebMvcConfigurer.html)

- CORS 설정
    - Cross Origin 요청 처리 설정
    - 같은 도메인에서 온 요청이 아니더라도 처리를 허용하고 싶다면 설정한다.
- 리턴 값 핸들러 설정
    - 스프링 MVC가 제공하는 기본 리턴 값 핸들러 이외에 리턴 핸들러를 추가하고 싶을 때 설정한다.
- 아큐먼트 리졸버 설정
    - 스프링 MVC가 제공하는 기본 아규먼트 리졸버 이외에 커스텀한 아규먼트 리졸버를 추가하고 싶을 때 설정한다.
- 뷰 컨트롤러
    - 단순하게 요청 URL을 특정 뷰로 연결하고 싶을 때 사용할 수 있다.
- 비동기 설정
    - 비동기 요청 처리에 사용할 타임아웃이나 TaskExecutor를 설정할 수 있다.
- 뷰 리졸버 설정
    - 핸들러에서 리턴하는 뷰 이름에 해당하는 문자열을 View 인스턴스로 바꿔줄 뷰 리졸버를 설정한다.
- Content Negotiation 설정
    - 요청 본문 또는 응답 본문을 어떤 (MIME) 타입으로 보내야 하는지 결정하는 전략을설정한다.
<br><br>