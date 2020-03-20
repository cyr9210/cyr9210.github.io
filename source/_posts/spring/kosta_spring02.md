---
title: Spring MVC, valid이용 검증, mybatis 연동 (KOSTA)
date: 2019-01-25 05:22:10
tags: Springframework_kosta
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
#  KOSTA DAY56
## Springframework

### Spring MVC
#### MVC(Model-View-Controller)패턴
- 어플리케이션의 확장을 위해 Model, View, Controller 세가지 영역으로 분리
- 컴포넌트의 변경이 다른 영역 컴포넌트에 영향을 미치지 않음
- 컴포넌트 간의 결합성이 낮아 프로그램 수정이 용이

#### 장점
- 화면과 비즈니스 로직을 분리해서 작업 가능
- 영역별 개발로 인하여 확장성이 뛰어남
- 표준화된 코드를 사용하므로 공동작업이 용이하고 유지보수성이 좋음

#### 단점
- 개발과정이 복잡해 초기 개발속도가 늦음
- 초보자가 이해하고 개발하기에 다소 어려움

#### Spring MVC
![springk](/images/springk/springk02-01.png)
- MVC패턴 기반 웹 개발 프레임워크
- Dispatcher Servlet : 컨트롤러 역할을 한다. (.do 등으로 오는 모든 요청을 받아서 연결시켜줌)
- Controller : 기존의 action 클래스의 역할
- Handler Mapping : Dispatcher Servlet이 Controller로 매핑하는 방식을 설정 (설정하지 않으면 Default annotation mapping →annotation으로 메소드를 호출)
- ModelAndView : view 이름 , data를 담을수 있다. (최근 5버젼 사용자들은 잘안쓴다고 한다.)
- viewResolver : view 타입을 설정해준다.
- view : 받은 data를 출력

#### 요청 처리 순서
1. DispatcherServlet이 요청을 수신
    - 단일 front controller servlet
    - 요청을 수신하여 처리를 다른 컴포넌트에 위임
    - 어느 컨트롤러에 요청을 전송할지 결정
1. DispatcherServlet은 Handler Mapping에 어느컨트롤러를 사용할 것인지 문의
    - URL과 매핑
1. DispatcherServlet은 요청을 컨트롤러에게 전송하고 컨트롤러는 요청을 처리한 후 결과 리턴
    - 비즈니스 로직 수행 후 결과 정보(Model)가 생성되어 JSP와 같은 뷰에서 사용됨
1. ModelAndView 오브젝트에 수행결과가 포함되어 DispatcherServlet에 리턴
1. ModelAndView는 실제 JSP정보를 갖고 있지 않으며, ViewResolver가 논리적 이름을 실제 JSP이름으로 변환
1. View는 결과정보를 사용하여 화면을 표현함

![springk](/images/springk/springk02-02.png)
- 좋은 디자인은 컨트롤러가 많은 일을 하지 않고 서비스에 처리를 위임

#### 라이브러리 설정

![springk](/images/springk/springk02-03.png)
Spring MVC 를 위해 Maven을 사용하여 라이르러리를 적용하였다.

1. Spring MVC
mvc패턴을 사용하기 위해 라이브러리 필요
2. validator
@valid, @InitBinder 어노테이션 사용하기 위해 필요
3. Spring jdbc
DB연동을 위해 필요
4. mybatis
mybatis-config.xml사용하기 위해 필요
5. mybatis-spring
mybatis-spring 연동을 위해 필요

#### DispatcherServlet 설정
- 다른 Servlet처럼 web.xml에 등록
- 기본적으로 “<servlet-name>-servlet.xml” 파일에서 어플리케이션 컨텍스트 정보를 로드
- <url-pattern>은 DispatcherServlet이 처리하는 URL 매핑 패턴을 정의

![springk](/images/springk/springk02-04.png)
- springframework의 springapp라는 이름의 DispatcherServlet를 등록한다.
- DispatcherServlet 영역 지정 ‘/’를 지정한것은 모든 요청을 영역으로 지정한것

![springk](/images/springk/springk02-05.png)
- context: annotation-config
context: component-scan base-package=”kosta”
→ kosta 패키지 안의 모든 @ 를 찾아 객체화
- mvc: annotation-dirven
mvc:default-servlet-handler
→ 매핑되는 컨트롤러가 없어도 default serlvet handler가 빈객체로 등록되어, default servlet handler에 요청을 전달한다.
- viewResolver 
    -Controller가 넘긴 view이름을 통해 알맞은 view 를 찾는 역할
    - prefix 경로 / suffix 타입

#### 한글처리
![springk](/images/springk/springk02-06.png)
- web.wml에서 한글처리를 위한 filter 처리 가능

![springk](/images/springk/springk02-07.png)
![springk](/images/springk/springk02-08.png)
- @Controller , @Service 등으로 객체화
- Controller 내에 @RequestMapping 으로 매핑
- @Autowired, @Inject 를 사용하여 DI 처리
- 같은 매핑 조건이더라도, RequestMethod 타입에 의해 전해진다.
- data를 가져가는 방법
    - ModelAndView (setViewName, addObject)
    - Model (addAttribute)
    - command 객체

> **command 객체**
form에서 입력된 값을 받기 위한 객체
form 의 name = command객체의 필드변수명과 같을 때, 쓸수 있다.

![springk](/images/springk/springk02-09.png)
- @ModelAttribute 어노테이션 설정으로 모델명을 설정할 수 있다.

![springk](/images/springk/springk02-10.png)
<br>

### valid 검증
valid 검증을 위해서 라이브러리가 필요하다.

![springk](/images/springk/springk02-11.png)
![springk](/images/springk/springk02-12.png)
- Validator 를 implements 하는 객체를 생성
- @validate 시, 발생 될 validate 메소드를 오버라이드
- 해당 객체 및 메소드를 사용하기 위해 @InitBinder 어노테이션 등록
InitBinder 메소드 작성
- @Valid 어노테이션으로 원하는 검증위치에 사용
검증내용을 직접 작성하지않고, 빈값, 사이즈, 문자타입 등을 어노테이션으로 등록 후, InitBinder어노테이션 없이 @Valid 사용이 가능하다.


![springk](/images/springk/springk02-13.png)
- NotEmpty : 빈값일 때, error
- Size : min값, max값 설정
- Pattern : 정규표현식등을 사용하여, 패턴설정
<br><br>

### mybatis 연동
#### xml 설정
![springk](/images/springk/springk02-14.png)
- 원하는 DB의 name, password, url, 을 입력하고, name, type을 정하여 server.xml에 기입한다.

![springk](/images/springk/springk02-15.png)
- 작성한 Resource를 참조하여, Dispatcher Servlet.xml 을 작성한다.
- dataSource → jndiObjectFactoryBean
- sqlSessionFactory
dataSource → 생성한 dataSource 객체
configLocation → mybatis-config.xml 설정파일
- sqlSessionTemplate
sqlSessionFactory → sqlSessionFactory객체

#### DB연동
![springk](/images/springk/springk02-16.png)
![springk](/images/springk/springk02-17.png)
- Dao객체 생성 → 등록한 sqlSessionTemplate 선언 및 등록
- Dao객체 등록
<br><br>