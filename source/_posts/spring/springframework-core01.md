---
title: 스프링 핵심기술01 - IoC 컨테이너와 빈
date: 2019-03-20 16:36:43
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
# 스프링 프레임워크 핵심기술01(inflearn) - 백기선 
## Springframework
<!-- more -->
백기선님의 Spring 시리즈 두번째 강좌 '[스프링프레임워크 핵심기술](https://www.inflearn.com/course/spring-framework_core/)' 수강을 시작하였습니다.
해당강좌는 스프링프레임워크 입문에 이어서 좀 더 심화적인 과정이며, 초급에서 중급수준으로 넘어가는 사람들에게 추천하느 강좌라고 소개하고 있습니다.
kosta에서 xml방식을 주로 사용하고 설명했었으나, 그 방법은 오래전에 사용되던 방법이라고 합니다..   
이번에는 Java Class방식을 다루도록 하겠습니다.
***

### IoC 컨테이너와 빈

#### IoC 컨테이너
- Inversion of Control
- 의존 관계 주입(Dependency Injection)이라고도 한다.
- 어떤 객체가 사용하는 의존객체를 직접 만들어 사용하는게 아니라 주입받아 사용하는 방법
- 스프링 초기에느 xml로 설정하는것이 대세였지만, annotation기반의  의존성 주입을 지원
- 최상위 인터페이스 : BeanFactory
    
> **POJO객체**
POJO(Plain Old Java Object) 특정 자바모델이나, 프레임워크 등을 따르지 않는 자바객체

#### 빈(Bean)
- IoC 컨테이너가 관리하는 객체
- 장점
    - 의존성 관리
    - 스코프
        - 싱글톤 : 하나만 사용(항상 같은 객체)
        - 프로토타입 : 매번 다른 객체
    - 라이프사이클 인터페이스
        - ex) 빈이 생성될 때, 무언가를 행함

#### Application Context
BeanFactory를 상속받는다. 즉, BeanFactory의 기능을 하면서 추가적인 기능을 가진다.
- BeanFactory 기능
- 메세지 소스 처리기능 (18n)
- 리소스 로딩 기능
- 이벤트 발행기능
<br><br>

### Application Context와 다양한 빈 설정방법

#### 스프링 IoC 컨테이너의 역할
- 빈 인스턴스 생성
- 의존 관계 설정
- 빈제공

#### ApplicationContext
- ClassPathXmlApplicationContext(XML)
- **AnnotationConfigApplicationContext(Java)**
최근에는 아래와 같이 Java형식을 사용한다.
![springcore](/images/springc/springcore01-01.png)


#### ComponentScan
Annotation이 붙은 객체를 빈 등록
- xml 설정 → context:componen-scan
- Java → @ComponentScan
![springcore](/images/springc/springcore01-03.png)![springcore](/images/springc/springcore01-04.png)
<br>

### Autowired
- 필요한 의존 객체의 타입에 해당하는 빈을 찾아 주입한다.
- 사용위치
    - 생성자
    - setter
    - 필드

#### 생성자
![springcore](/images/springc/springcore01-05.png)
- 해당 타입의 빈이 한개인 경우
    - 정상
- 해당 타입의 빈이 없는 경우
    - 에러발생
- 해당 타입의 빈이 여러 개인 경우
    - @Primary 사용하면 여러 개인경우 해당 빈을 주입
![springcore](/images/springc/springcore01-08.png)

#### setter, 필드
![springcore](/images/springc/springcore01-06.png)
- 해당 타입의 빈이 한개인 경우
    - 정상
- 해당 타입의 빈이 없는경우
    - required = false 설정 시, 없을경우 의존성을 주입하지 않는다.
![springcore](/images/springc/springcore01-07.png)

- 해당 타입의 빈이 여러 개인 경우
    - @Primary 사용하면 여러 개인경우 해당 빈을 주입
    - @Qualifier 사용하여 빈이름 지정
    ![springcore](/images/springc/springcore01-09.png)
    **Qualifier을 사용으로 해결 할 수 있지만, @Primary를 쓰는것을 추천한다.**

#### 해당 타입의 빈을 모두 받고 싶을때
- List<>로 받는다.
![springcore](/images/springc/springcore01-10.png)

#### 동작 원리
 - 라이프사이클에 의해 동작
 - BeanPostProcessor
    - 새로 만든 빈 인스턴스를 수정할 수 있는 라이프 사이클 인터페이스
 - AutowiredAnnotationBeanPostProcessor extends BeanPostProcessor
    - 스프링이 제공하는 @Autowired와 @Value 애노테이션 그리고 JSR-330의 @Inject 애노테이션을 지원하는 애노테이션 처리기.
<br><br>

### @Component와 ComponentScan
#### 컨포넌트 스캔 주요 기능
- 스캔 위치 설정(basePackage, basePackageClasses를 통해 위치 설정)
![springcore](/images/springc/springcore01-11.png)
Myservice는 @Service를 사용했지만 스캔 범위에서 벗어나기 때문에 빈으로 등록되지 않는다.

- 필터: 어떤 애노테이션을 스캔 할지 또는 하지 않을지 

#### @Component
다음 Annotation들은 @Component를 참조한다.
- @Repository
- @Service
- @Controller
- @Configuration

#### 동작원리
- 실제 스캐닝은 ConfigurationClassPostProcessor라는 BeanFactoryPostProcessor에 의해 처리 됨.
- **즉, 다른 빈(직접등록하는)들이 등록되기전에 빈으로 등록된다.**
    → 초기 시간이 오래걸릴 수 있다.(싱글톤타입의 경우)

#### 펑션을 사용한 빈 등록
- ComponentsSan을 대체하는 것이 아닌, 직접 빈을 등록하는 경우를 아래와 같이 사용하는것이 좋다. 

```
 public static void main(String[] args) {
        SpringApplication app = new SpringApplication((SpringcoreApplication.class));
        
        app.addInitializers((ApplicationContextInitializer<GenericApplicationContext>) ctx -> 
        ctx.registerBean(MyService.class));
        
        app.run(args);
 }  
```

![springcore](/images/springc/springcore01-12.png)
- ComponentScan 범위 밖이지만 위와같은 방법으로 빈을 등록해줬기 때문에 빈으로 등록이된 것을 볼 수 있다.
<br><br>

### 빈의 스코프
#### 스코프
- 싱글톤
    - 프로퍼티가 공유
        - 여러곳에서 사용 시, thread safe 하지 않다.
    - ApplicationContext 초기 구동시 인스턴스 생성
    
- 프로토 타입
    ![springcore](/images/springc/springcore01-14.png)
    - @Scope를 사용하여 설정
    - Request
    - Session
    - WebSocket
    - ...
    
- 프로토타입 빈이 싱글톤 빈을 참조하면 아무 문제 없다.
- 싱글톤 빈이 프로토타입 빈을 참조하면
    - 프로토타입 빈이 업데이트되지 않는다. (싱글톤처럼 계속 같은 값)
    - 업데이트 
        - scoped-proxy
        ![springcore](/images/springc/springcore01-13.png)
        - Object-Provider

#### 예제
- 싱글톤 및 프로토타입 
![springcore](/images/springc/springcore01-15.png)

- 싱글톤 빈이 프로토타입을 참조할 때
![springcore](/images/springc/springcore01-16.png)
    - 프로토 타입은 전부 다른값을 가르켜야하는데 같은 값을 가르키는 문제가 발생(없데이트가 안됨)

- 프록시 시용
    ![springcore](/images/springc/springcore01-17.png)
    - 프록시를 사용하여 상기 문제를 해결하였다.
    - ScopedProxyMode의 Default는 프록시를 사용하지 않는다.   
    우리예제에서는 class로 설정되어 있어 TARGET_CLASS 로 변경해주었다. 
    
- Object provider 사용
    ![springcore](/images/springc/springcore01-18.png)
    - 코드자체를 건드리기때문에 추천하지는 않는다. 
<br><br>

### Environment
- ApplicationContext는 EnvironmentCapable을 상속받는다.
- Environment가 가지는 기능
    - Profile
    - Property
- getEnvironment()를 통해 확인할 수 있다.
```
ApplicationContext ctx;

Environment environment = ctx.getEnvironment();
```

#### Profile
- 빈들의 그룹
- 특정 상황에서의 환경을 활성화하고 싶을 때, 사용한다.
    - ex)테스트 환경에서는 A라는 빈을 사용하고, 배포 환경에서는 B라는 빈을 쓰고 싶다.
    - 이 빈은 모니터링 용도니까 테스트할 때는 필요가 없고 배포할 때만 등록이 되면 좋겠다.

#### Profile 정의하기
- 클래스에 정의
    - @Configuration @Profile("[profile_name\]")
    ![springcore](/images/springc/springcore01-21.png)

    - @Component @Profile("[profile_name\]")
    ![springcore](/images/springc/springcore01-19.png)

- 메소드에 정의
    - Bean @Profile("[profile_name\]")
    ![springcore](/images/springc/springcore01-20.png)

#### Profile 설정하기
- -Dspring.prifiles.active="[profile_name\]"
![springcore](/images/springc/springcore01-23.png)

- ActivePrifiles
![springcore](/images/springc/springcore01-22.png)

#### Profile 표현식
- ! (not)
- & (and)
- | (or)
<br><br>

### Property
- 다양한 방법으로 정의할 수 있는 설정값
- Environment의 역할은 프로퍼티 소스 설정 및 프로퍼티 값 가져외기

#### 우선순위
- StandardServletEnvironment의 우선순위
    - ServletConfig 매개변수
    - ServletContext 매개변수
    - JNDI (java:comp/env/)
    - JVM 시스템 프로퍼티 (-Dkey=”value”)
    ![springcore](/images/springc/springcore01-26.png)

    - JVM 시스템 환경 변수 (운영 체제 환경 변수)

- @PropertySource
    - Environment를 통해 프로퍼티 추가하는 방법
    ![springcore](/images/springc/springcore01-24.png)

- JVM 시스템 프로퍼티 vs @PropertySource
    ![springcore](/images/springc/springcore01-25.png)
<br><br>

### MessageSource
국제화(i18n) 기능을 제공하는 인터페이스
- ApplicationContext는 MessageSource인터페이스를 상속한다.

- 스프링 부트를 사용한다면 별다른 설정 필요없이 messages.properties 사용할 수 있음
    - messages.properties
    - messages_ko_KR.properties

#### 릴로딩 기능이 있는 메세지 소스 사용하기
- 직접 빈등록을 해준다.
```
@Bean
public MessageSource messageSource(){
    ReloadableResourceBundleMessageSource messageSource 
        = new ReloadableResourceBundleMessageSource();
    
    messageSource.setBasename("classpath:/messages");
    messageSource.setDefaultEncoding("UTF-8");
    messageSource.setCacheSeconds(3); /*최대3초까지만 캐싱*/
    
    return messageSource;
}
```

- 테스트 코드 작성
```
@Component
public class AppRun implements ApplicationRunner {
    @Autowired
    MessageSource messageSource;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        while (true) {
            Locale.setDefault(Locale.CANADA);
            System.out.println(messageSource.getMessage
                ("greeting", new String[]{"Bong!"}, Locale.KOREA));
            System.out.println(messageSource.getMessage
                ("greeting", new String[]{"Bong!"}, Locale.getDefault()));
            Thread.sleep(1000); /*1초마다 찍어*/
        }
    }
}
```

- 실행결과
![springcore](/images/springc/springcore01-27.png)
<br>

### ApplicationEventPublisher
- 이벤트 프로그래밍에 필요한 인터페이스 제공
- 옵저버 패턴 구현체

> **옵저버 패턴**
객체의 상태 변화를 관찰하는 관찰자들, 즉 옵저버들의 목록을 객체에 등록하여 상태 변화가 있을 때마다 메서드 등을 통해 객체가 직접 목록의 각 옵저버에게 통지하도록 하는 디자인 패턴

#### 이벤트 만들기
- ApplicationEvent 상속
![springcore](/images/springc/springcore01-28.png)
- 스프링 4.2 부터는 이 클래스를 상속받지 않아도 이벤트로 사용할 수 있다.
![springcore](/images/springc/springcore01-31.png)
    - 스프링 프레임워크의 클래스를 상속받지 않아 깔끔한 POJO객체가 되었다.
    - 스프링 프레임워크의 철학 → **비침투성**

#### 이벤트 발생시키기
![springcore](/images/springc/springcore01-29.png)
- ApplicationEventPublisher.publishEvent();

#### 이벤트 처리하는 방법
- ApplicationListener<이벤트> implements한 클래스 만들어서 빈으로 등록하기.
![springcore](/images/springc/springcore01-30.png)
- 스프링 4.2 부터는 @EventListener를 사용해서 빈의 메소드에 사용할 수 있다.
![springcore](/images/springc/springcore01-32.png)
- 이벤트 핸들러가 여러개일 경우 모두 실행
![springcore](/images/springc/springcore01-33.png)
    - 기본적으로는 순차적 (순서는 잘모르겠으나, 순처작으로 실행된다.)
- 순서를 정하고 싶다면 @Order와 함께 사용.
![springcore](/images/springc/springcore01-34.png)

- 비동기적으로 실행하고 싶다면 @Async와 함께 사용.(멀티쓰레드)
![springcore](/images/springc/springcore01-35.png)

#### 스프링이 제공하는 기본 이벤트
![springcore](/images/springc/springcore01-36.png)
- ContextRefreshedEvent: ApplicationContext를 초기화 했더나 리프래시 했을 때 발생.
- ContextClosedEvent: ApplicationContext를 close()하여 싱글톤 빈 소멸되는 시점에 발생.
- ContextStartedEvent: ApplicationContext를 start()하여 라이프사이클 빈들이 시작신호를 받은 시점에 발생.
- ContextStoppedEvent: ApplicationContext를 stop()하여 라이프사이클 빈들이 정지신호를 받은 시점에 발생.
- RequestHandledEvent: HTTP 요청을 처리했을 때 발생.
<br><br>

### ResourceLoader
- 리소스를 읽어오는 기능을 제공하는 인터페이스
- ApplicationContext는 ResourceLoader를 상속받는다.

![springcore](/images/springc/springcore01-37.png)
<br>