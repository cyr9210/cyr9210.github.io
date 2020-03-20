---
title: 스프링 부트 운영16 - Actuator
date: 2019-04-18 20:13:46
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
# 스프릥 부트 개념과 활용16(inflearn) - 백기선 
## Spring boot

<!-- more -->

### Actuator
- 스프링 부트는 애플리케이션 운영 환경에서 유용한 기능을 제공한다. 
    -  스프링 부트가 제공하는 엔드포인트와 메트릭스
    - 그 데이터를 활용하는 모니터링 기능

- [reference 문서](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)

- 의존성 추가
    - spring-boot-starter-actuator

- 애플리케이션의 각종 정보를 확인할 수 있는 Endpoints
    - 다양한 Endpoints 제공.
        - auditevents : 인증정보
        - conditions : 자동설정 및 조건
        - env : environment안에 property
        - health : 잘 구동중인지..
        - httptrace : 최근 100개의 http요청과 응답
        - loggers : 로그와 레벨 정보, 수정도 가능하다.
        - metrics : 메모리, cpu등 핵심정보, 알림설정도 가능하다. (모니터링 중 어느 수치를 넘거나..)
        - shutdown : 애플리케이션을 off 할 수 있다.
        - web
            - jolokia : JMX 빈을 http 화면에서 확인할 수 있다.
        
    - JMX 또는 HTTP를 통해 접근 가능 함.
    - shutdown을 제외한 모든 Endpoint는 기본적으로 활성화 상태.
    - 활성화(enable)랑 공개(Exposing)는 다르다. 
    보안상의 이유로 노출 health, info 외 비공개이다. 
    - 활성화 옵션 조정
        - management.endpoints.enabled-by-default=false
        - management.endpoint.info.enabled=true
       
#### JMX와 HTTP
 - JConsole 사용하기
    - terminal에서 jconsole
    ![springboot](/images/springboot/springboot16-1.png)
    - https://docs.oracle.com/javase/tutorial/jmx/mbeans/
    - https://docs.oracle.com/javase/7/docs/technotes/guides/management/jconsole.html
 - VisualVM 사용하기
    ![springboot](/images/springboot/springboot16-2.png)
    - [다운로드 사이트]https://visualvm.github.io/download.html
    - JConsole보다 가시적이고 다양한 플러그인을 지원한다.
    
 - HTTP 사용하기
    - /actuator
    - health와 info를 제외한 대부분의 Endpoint가 기본적으로 비공개 상태
    - 공개 옵션 조정
         - management.endpoints.web.exposure.include=*
         - management.endpoints.web.exposure.exclude=env,beans

#### Spring-Boot-Admin
- 스프링 부트가 제공하는것이 아닌 제 3자가 오픈소스로 제공하는 어플리케이션
- Actuator 정보를 간단한 UI로 제공한다.
- github : https://github.com/codecentric/spring-boot-admin

- Admin 서버 설정
    - 의존성 추가
    ```
    <dependency>
        <groupId>de.codecentric</groupId>
        <artifactId>spring-boot-admin-starter-server</artifactId>
        <version>2.1.4</version>
    </dependency>

    ```
    - @EnableAdminServer
    ![springboot](/images/springboot/springboot16-3.png)

- 클라이언트 설정 (운영될 프로젝트)
    - 의존성 추가
    ```
    <dependency>
        <groupId>de.codecentric</groupId>
        <artifactId>spring-boot-admin-starter-client</artifactId>
        <version>2.1.4 </version>
    </dependency>
    ```
    - spring.boot.admin.client.url=http://localhost:8080
    - management.endpoints.web.exposure.include=*
    - 포트를 같은걸 쓸 수 없기 때문에 포트 변경해준다.
    - 설정했던 localhost:8080 접속 시, 아래와같은 화면이 나오고 아래 버튼 클릭 시, 기능들을 사용할 수 있다.
     ![springboot](/images/springboot/springboot16-4.png)
    - 가시적으로 정보들을 확인할 수 있고, 운영중에 로거 변경등의 기능이 있다.

---
스프링부트 개념과 활용 강좌를 모두 수강하였습니다.😁
제가 원했던 내용보다 자세하고 깊숙하게 알려주셔서 너무너무 만족스러웠습니다.
제가 알게된 내용들로 프로젝트를 진행할 생각을 하니 설레기도 합니다.
다음 정리 블로깅은 아마 스프링 JPA되지 않을까합니다.
스프링 JPA는 ORM의 구현체로 객체와 관계형 데이터베이스의 연결을 해주는 api며 많은 기업에서 사용하고 있는것으로 알고있습니다.
빨리 수강하고 싶네요 😁
<br><br>    