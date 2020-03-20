---
title: 스프링 MVC 동작 원리02 - 스프링 연동, DispatcherServlet
date: 2019-04-25 16:11:55
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

웹 서블릿 어플리케이션에 스프링을 사용한다는 의미는 크게 2가지로 나눠서 이야기 할 수 있다.
- 스프링 IoC 컨테이너를 사용하겠다.
- 스프링 MVC를 사용하겠다.

### 스프링 IoC 컨테이너 연동
- 의존성 추가
    - spring-webmvc
    
- ContextLoaderListener 등록
    - ApplicationContext를 서블릿 어플리케이션 생명주기에 맞춰서 바인딩해준다.
        - ApplicationContext를 웹어플리케이션에 등록된 서블릿들이 사용할 수 있도록 서블릿 컨텍스트에 등록해준다.
        - 서블릿이 종료될때 제거해준다.
        - 즉, ApplicationContext를 연동해준다.(ApplicationContext는 만들어야한다.)
    - 서블릿에서 IoC 컨테이너를 ServletContext를 통해 꺼내 사용할 수 있다.
    ```
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
    ```
    
- ApplicationContext 생성
    - 기본적으로 xml을 사용하지만 Java설정파일 사용하겠다. (최근 추세)
        - AppConfig 생성
        ![springmvc](/images/springwebmvc/springwebmvc02-1.png)
        - context class 설정 변경
        ```
        <context-param>
            <param-name>contextClass</param-name>
            <param-value>org.springframework.web.context.support.AnnotationConfigWebApplicationContext</param-value>
        </context-param>
        
        <context-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>bong.spring.AppConfig</param-value> /* Appconfig파일위치 */
        </context-param>
        ```

    - ContextLoaderListener - initWebApplicationContext을 보면 WebApplicationContext.ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE라는 이름으로 ApplicationContext를 setattribute하는것을 볼 수 있다.
        ![springmvc](/images/springwebmvc/springwebmvc02-2.png)

- 등록한 빈 사용하기
    - servletcontext에서 확인한 이름으로 getattrbute해서 확인할 수 있다.
    - 빈으로 등록한 서비스를 getBean()으로 사용할 수 있다.
    ```
    private Object getName() {
        ApplicationContext applicationContext = (ApplicationContext) getServletContext().getAttribute(WebApplicationContext.ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE);
        HelloService helloService = applicationContext.getBean(HelloService.class);
        return helloService.getname();
    }
    ```
    
- 지금까지 만들었던 내용을 RootApplicationContext라고 할 수 있다.
    - 다른 서블릿에서도 공용으로 사용될 공용 리소스
    - web과 상관없는...
<img src="/images/springwebmvc/springwebmvc02-3.png" width="40%">
<br>

### 스프링 MVC 연동
- DispatcherServlet
    - 스프링 MVC의 핵심.
    - Front Controller 역할을 한다.

- DispatcherServlet 등록
    ```
    <servlet>
        <servlet-name>app</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
          <param-name>contextClass</param-name>
          <param-value>org.springframework.web.context.support.AnnotationConfigWebApplicationContext</param-value>
        </init-param>
        <init-param>
          <param-name>contextConfigLocation</param-name>
          <param-value>bong.spring.WebConfig</param-value>
        </init-param>
      </servlet>
    
      <servlet-mapping>
        <servlet-name>app</servlet-name>
        <url-pattern>/app/*</url-pattern>
      </servlet-mapping>    
    ```

- @Controller로 매핑 할 수 있도록한다.
- RootApplicationContext와 같이 쓸 수 있다.
    - Root : Service, Repository등.. 공통 사용빈
    - Dispatcher : Controller등 web 관련 .. 
    - 그러나 다음과 같은 설정이 필요하다.
    ![springmvc](/images/springwebmvc/springwebmvc02-4.png)

- 최근에는 DispatcherServlet 하나만 등록해서 모두 사용하는 방법을 주로 사용한다.

- SpringBoot의 경우 구조가 다르다.
    - ServletContext 안에 스프링이 들어갔다면
    - 스프링부트는 어플리케이션(Java) 안에 톰캣(Servlet컨테이너)가 들어간다.
<br>
 
### DispatcherServlet 
#### 동작원리
- DispatcherServlet 초기화
    - 다음의 특별한 타입의 빈들을 찾거나, 기본 전략에 해당하는 빈들을 등록한다.
    - HandlerMapping : 핸들러를 찾아주는 인터페이스
    - HandlerAdapter : 핸들러를 실행하는 인터페이스
    - HandlerExceptionResolver : 예외처리
    - ViewResolver : 뷰를 찾아낸다.
    - ...

- DispatcherServlet 동작 순서
    1. 요청을 분석한다. (로케일, 테마, 멀티파트 등)
    2. (핸들러 맵핑에게 위임하여) 요청을 처리할 핸들러를 찾는다.
    3. (등록되어 있는 핸들러 어댑터 중에) 해당 핸들러를 실행할 수 있는 “핸들러 어댑터”를 찾는다.
    4. 찾아낸 “핸들러 어댑터”를 사용해서 핸들러의 응답을 처리한다.
        - 핸들러의 리턴값을 보고 어떻게 처리할지 판단한다.
        - 뷰 이름에 해당하는 뷰를 찾아서 모델 데이터를 랜더링한다.
        - @ResponseEntity가 있다면 Converter를 사용해서 응답 본문을 만들고.
    5. (부가적으로) 예외가 발생했다면, 예외 처리 핸들러에 요청 처리를 위임한다.
    6. 최종적으로 응답을 보낸다.

- DispatcherServlet의 사용전략은 서블릿 init 할 때 이루어짐으로 최초 한번만 실행된다.
    - ViewResolver등록이라던지.. 

- HandlerMapping
    - RequestMappingHandlerMapping
- HandlerAdapter
    - RequestMappingHandlerAdapter
    
#### SimpleController
- HandlerMapping
    - BeanNameUrlHandlerMapping
- HandlerAdapter
    - SimpleControllerHandlerAdapter

```
@org.springframework.stereotype.Controller("/simple")
public class SimpleController implements Controller {
    @Override
    public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
    return new ModelAndView("/WEB-INF/simple.jsp");
    }
}
```

#### 커스텀 ViewResolver
- ViewResolver
    - InternalResourceViewResolver

- InternalResourceViewResolver
    - Prefix
    - Suffix
    
- 예제
![springmvc](/images/springwebmvc/springwebmvc02-5.png)
    - 이제 뷰이름만 주어도 된다. 주소 및 타입 설정을 생략할 수 있다.
<br>
