---
title: 스프링 MVC 동작 원리03 - 스프링 MVC 구성 요소, 동작원리 정리
date: 2019-04-30 00:59:04
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

### 스프링 MVC 구성 요소
![springmvc](/images/springwebmvc/springwebmvc03-1.png)
- DispatcherSerlvet의 기본 전략
    - DispatcherServlet.properties를 보면 정의되어있다.
    
- MultipartResolver
    - 파일 업로드 요청 처리에 필요한 인터페이스
    - HttpServletRequest를 MultipartHttpServletRequest로 변환해주어 요청이 담고 있는 File을 꺼낼 수 있는 API 제공.

- LocaleResolver
    - 클라이언트의 위치(Locale) 정보를 파악하는 인터페이스
    - 기본 전략은 요청의 accept-language를 보고 판단.

- ThemeResolver
    - 애플리케이션에 설정된 테마를 파악하고 변경할 수 있는 인터페이스
    - 참고: https://memorynotfound.com/spring-mvc-theme-switcher-example/

- HandlerMapping
    - 요청을 처리할 핸들러를 찾는 인터페이스

- HandlerAdapter
    - HandlerMapping이 찾아낸 “핸들러”를 처리하는 인터페이스
    - 스프링 MVC 확장력의 핵심

- HandlerExceptionResolver
    - 요청 처리 중에 발생한 에러 처리하는 인터페이스

- RequestToViewNameTranslator
    - 핸들러에서 뷰 이름을 명시적으로 리턴하지 않은 경우, 요청을 기반으로 뷰 이름을 판단하는 인터페이스

- ViewResolver
    - 뷰 이름(string)에 해당하는 뷰를 찾아내는 인터페이스

- FlashMapManager
    - FlashMap 인스턴스를 가져오고 저장하는 인터페이스
    - FlashMap은 주로 리다이렉션을 사용할 때 요청 매개변수를 사용하지 않고 데이터를 전달하고 정리할 때 사용한다.
    - redirect:/events 이런식으로 이전 정보들을 가지고 가되, url에 정의하지 않고 리다이렉트할 때 사용됨.
    
### 스프링 MVC 동작원리 정리
- 결국엔 (굉장히 복잡한) 서블릿 = DispatcherServlet
- DispatcherServlet 초기화
    1. 특정 타입에 해당하는 빈을 찾는다.
    2. 없으면 기본 전략을 사용한다. (DispatcherServlet.properties)
- 스프링 부트 사용하지 않는 스프링 MVC
    - 서블릿 컨네이너(ex, 톰캣)에 등록한 웹 애플리케이션(WAR)에 DispatcherServlet을 등록한다.
        - web.xml에 서블릿 등록
        - 또는 WebApplicationInitializer에 자바 코드로 서블릿 등록 (스프링 3.1+, 서블릿 3.0+)
            ```
            public class WebApplication implements WebApplicationInitializer {
                @Override
                public void onStartup(ServletContext servletContext) throws ServletException {
                    AnnotationConfigWebApplicationContext context = new AnnotationConfigWebApplicationContext();
                    context.register(WebConfig.class);
                    context.refresh();
            
                    DispatcherServlet dispatcherServlet = new DispatcherServlet(context);
                    ServletRegistration.Dynamic app = servletContext.addServlet("app", dispatcherServlet);
                    app.addMapping("/app/*");
                }
            }
            ```
    - 세부 구성 요소는 빈 설정하기 나름.

- 스프링 부트를 사용하는 스프링 MVC
    - 자바 애플리케이션에 내장 톰캣을 만들고 그 안에 DispatcherServlet을 등록한다.
        - 스프링 부트 자동 설정이 자동으로 해줌.
    - 스프링 부트의 주관에 따라 여러 인터페이스 구현체를 빈으로 등록한다.
<br><br>