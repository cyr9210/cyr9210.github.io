---
title: 스프링 부트 원리04 - 내장 웹 서버
date: 2019-04-02 16:59:51
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
# 스프릥 부트 개념과 활용04(inflearn) - 백기선 
## Spring boot

<!-- more -->

### 내장 웹 서버
- @SpringBootApplication 쓰지 않고 톰캣 띄우기
    ![springboot](/images/springboot/springboot04-1.png)
    - **부트는 서버가 아니다.**
    - 톰캣객체생성
    - 포트설정
    - 톰캣에 컨텍스트 추가
    - 서블릿 만들기
    - 톰캣에 서블릿 추가
    - 컨텍스트에 서블릿 맵핑
    - 톰캣실행및대기

- 이 모든 과정을 보다 상세히 또 유연하게 설정하고 실행해주는게 바로 스프링 부트의 자동 설정.
    - ServletWebServerFactoryAutoConfiguration (서블릿 웹 서버 생성)
        - TomcatServletWebServerFactoryCustomizer (서버 커스터마이징)
- DispatcherServletAutoConfiguration
    - 서블릿 만들고 등록한다.
    - DispatcherServlet이 따로 있는 이유
        - 서블릿 컨테이너는 변경될 수 있느나, 서블릿은 변경되지 않기때문에 

#### [컨테이너와 서버 포트](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-embedded-web-servers.html)
- 톰캣이 기본 컨테이너로 설정되어있다.
- 다른 서블릿 컨테이너로 변경
    ![springboot](/images/springboot/springboot04-2.png)
    - 톰캣 의존성을 제외한다.
    - 원하는 서블릿 컨네이너 설정
- 웹 서버 사용하지 않기
    - 코드로 타입변경
    ![springboot](/images/springboot/springboot04-3.png)
    
    - application.properties로 타입변경
    ![springboot](/images/springboot/springboot04-4.png)
- 포트
    - 변경
        - application.properties에 포트번호 정의
        ```
        server.port=7070
        ```
    - 랜덤포트
        - 사용가능한 포트를 랜덤으로 사용한다.
        ```
        server.port=0
        ```
    - ApplicationListner<ServletWebServerInitializedEvent\>
        - 웹서버가 초기화가되면(생성되면) 이벤트 리스터가 콜백된다.
        - 해당 방법으로 포트 확인이 가능하다.
        ![springboot](/images/springboot/springboot04-5.png)

#### 응답 압축해서 보내기
- application.properties에 아래왕 같이 입력 시, 응답을 압축해서 보낸다.
```
server.compression.enabled=true
```

- 기본적으로 압축해도 좋은 타입만 압축해서 보내준다.
    - text/html
    - text/xml
    - text/plain
    - text/css
    - text/javascript
    - application/javascript
    - application/json
    - application/xml

#### HTTPS와 HTTP2
- HTTPS 설정하기
    - 키스토어 만들기
        - 줄바꿈없이 아래내용 입력한다.(터미널)
        ```
        keytool -genkey 
            -alias spring 
            -storetype PKCS12 
            -keyalg RSA 
            -keysize 2048 
            -keystore keystore.p12 
            -validity 4000
        ```
        - genkey : key를 생성한다.
        - alias : 별칭
        - storetype : 서블릿 컨테이너마다 지원 티압이 다르다.
            - 주요키타입
                - PKCS12
                - JKS (JavaKeyStore)
    - application.properties 설정
    ```
    server.ssl.key-store: keystore.p12
    /* keysotre의 경로를 classpath에 넣은경우, classpath:를 앞에 붙여준다.*/
    server.ssl.key-store-password: 비밀번호
    server.ssl.keyStoreType: PKCS12
    server.ssl.keyAlias: spring
    ```
    - HTTP로 요청 시, 실패
    ![springboot](/images/springboot/springboot04-7.png)
    
    - HTTPS로 요청 시,
    ![springboot](/images/springboot/springboot04-8.png)
        - 브라우저가 해당 인증서의 펍키를 모르는 상태일때, 위와같은 화면이 나타난다.
        - 공인된 인증서를 사용 시, 대부분의 브라우저가 펍키를 알기때문에 정상적인 화면이 나타난다.
    
    - 커넥터가 하나이기때문에 HTTP를 받을 커넥터가 없다.

- HTTP 커넥터는 코딩으로 설정하기
   ![springboot](/images/springboot/springboot04-10.png)
   ```
   @Bean
       public ServletWebServerFactory servletContainer(){
           TomcatServletWebServerFactory tomcat = new TomcatServletWebServerFactory();
           tomcat.addAdditionalTomcatConnectors(createStandardConnector());
           return tomcat;
       }
   
       private Connector createStandardConnector() {
           Connector connector = new Connector("org.apache.coyote.http11.Http11NioProtocol");
           connector.setPort(8080);
           return connector;
       }
   ```
   - https, http 포트번호를 달리 설정한 후 테스트 결과, 둘다 정상으로 나온다.
   이미지

- HTTP2 활성화
    - application.properties에 다음설정을 추가한다.
    ```
    server.http2.enabled=true
    ```
    - 그러나 컨테이너 마다, 전부 다르다.
        - undertow
            - HTTPS만 설정되어있으면 추가 설정이 필요없다.
            ![springboot](/images/springboot/springboot04-11.png)![springboot](/images/springboot/springboot04-6.png)
                    
        - Tomcat
            - 8.5버전 이하는 복잡한 설정이 필요 (비추천)
            - 톰캐9 이상 과 JDK9 이상 시, 설정 필요없다.
            - 차리리 버전을 업그레이드 하는것을 추천한다.
            ![springboot](/images/springboot/springboot04-12.png)![springboot](/images/springboot/springboot04-13.png)![springboot](/images/springboot/springboot04-14.png)
---
참고 강의 : [생활코딩 - HTTPS와 SSL 인증서](https://opentutorials.org/course/228/4894)
<br><br>
