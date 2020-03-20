---
title: 스프링 MVC 설정05 - 스프링 부트에서 JSP 사용하기
date: 2019-05-31 15:36:50
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

### 스프링 부트에서 JSP 사용하기
- 제약 사항
    - JAR 프로젝트로 만들 수 없음, WAR 프로젝트로 만들어야 함
        ![springmvc](/images/springwebmvc/springwebmvc05-1.png)
    - Java -JAR로 실행할 수는 있지만 “실행가능한 JAR 파일”은 지원하지 않음
    - 언더토우(JBoss에서 만든 서블릿 컨테이너)는 JSP를 지원하지 않음
    - Whitelabel 에러 페이지를 error.jsp로 오버라이딩 할 수 없음

- 의존성 추가
    ```
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>jstl</artifactId>
    </dependency>
    
    <dependency>
        <groupId>org.apache.tomcat.embed</groupId>
        <artifactId>tomcat-embed-jasper</artifactId>
        <scope>provided</scope>
    </dependency>
    ```
    
- WAR로 프로젝트를 생성하면 ServletInitializer클래스가 추가된다.
    ![springmvc](/images/springwebmvc/springwebmvc05-2.png)
    - 독립적인 WAR파일로 실행할때는 Application 클래스를 사용하는 것이고,
    - 톰캣 및 서블릿엔진에 배포할때는 ServletInitializer를 사용하는 것이다.
    
- 태그선언
    ```
    <%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
    ```
    
- view suffix 및 prefix 설정
    - application.properties
        ```
        spring.mvc.view.prefix=/WEB-INF/jsp
        spring.mvc.view.suffix=.jsp
        ```

- mvn package
    - war파일로 생성된다.
    - java -jar 명령어로 실행이 가능하다.
    - 독립적으로 실행가능한 어플리케이션

>**mvnw package**
스프링 부트 프로젝트를 만들면 mvnw라는 커맨드가 생성되고,
로컬에 메이븐이 설치 안되어있어도 패키징 할 수 있다. 

### WAR 파일 배포하기
#### java -jar를 사용해서 실행하기
- SpringApplication.run 사용하기 (java -jar 실행한것과 같다.)
    ![springmvc](/images/springwebmvc/springwebmvc05-3.png)
    
- 서블릿 컨테이너에 배포하기
    - SpringBootServletInitializer (WebApplicationInitializer) 사용하기
    ![springmvc](/images/springwebmvc/springwebmvc05-4.png)
    - 톰캣 설치
        - [톰캣 홈페이지](https://tomcat.apache.org/download-90.cgi)에서 톰캣을 다운받는다.(현재 최신버전 9버전)
        - 압출을 풀고 해당위치에서 권한을 준다.
            - bin 폴더를 찾아들어가서 아래 명령어를 사용하여 \.sh파일을 실행가능한 파일로 만들어준다.
            ```
            chmod +x *.sh
            ```
    - 톰캣 설정
        - 실행 설정
        ![springmvc](/images/springwebmvc/springwebmvc05-5.png)
        - 톰캣 추가
        ![springmvc](/images/springwebmvc/springwebmvc05-6.png)
        - 톰캣 폴더 경로 지정 및 아티팩트 지정 (war는 압출파일 , war exploded는 압축을 푼 것)
        ![springmvc](/images/springwebmvc/springwebmvc05-7.png)
