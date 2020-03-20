---
title: 개발환경 세팅, Junit 테스트, OracleDB 연동, mybaits연동, log4jdbc, MVC Controller, 파일업/다운로드 (KOSTA)
date: 2019-01-29 06:06:08
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
#  KOSTA DAY58
## Springframework
<!-- more -->
### 개발환경 설정(Spring MVC)
해당 포스팅은 Eclipse를 통해 이루어진다. 추후에 IntelliJ 로 변경할 예정이다.

#### 프로젝트생성
![springk](/images/springk/springk04-02.png)

![springk](/images/springk/springk04-04.png)

![springk](/images/springk/springk04-06.png)
- Spring Legacy Project를 생성한다.
- Spirng MVC Project 선택
- 사용 될 Controller 피키지명 입력( 자동생성)


![springk](/images/springk/springk04-08.png)

![springk](/images/springk/springk04-10.png)
- pom.xml에 Java 및 Spring, Maven의 버전을 변경한다.


![springk](/images/springk/springk04-12.png)

![springk](/images/springk/springk04-13.png)
- maven update

#### Lombok 설치 및 라이브러리 추가
> **Lombok이란?**
Java에서 자주 사용하는 생성자, setter/getter, toString()을 자동으로 생성해 주는 라이브러리이다.

- [Lombok홈페이지에서](https://projectlombok.org/download) 다운로드 한다.

![springk](/images/springk/springk04-14.png)
- Lombok을 설치한다.
<br><br>

### JUnit 테스트
> **xUnit이란?**
자바만 단위 테스팅 프레임 워크인 JUnit만 있는게 아니다. 다른 언어도 단위 테스트를 위한 프레임워크가 존재하며 보통 이름을 xUnit이라 칭한다.
ex) JUnit → java, CUnit → C ,CppUnit →c++

#### JUnit assert 관련 메소드
![springk](/images/springk/springk04-15.png)
JUnit을 활용하여 설정이 잘 되었는지 확인해보자.

- pom.xml에 라이브러리 추가(junit 4.12버전)

![springk](/images/springk/springk04-16.png)
- root-context.xml 파일은 Spring과 관련된 설정을 작성하는 곳
- ‘어노테이션을 통해 sample 패키지를 스캔하여 객체를 만들겠다.’를 의미한다.

![springk](/images/springk/springk04-17.png)

![springk](/images/springk/springk04-18.png)

- 테스트 객체 추가
- @Component 를 사용하여 Bean객체로 등록한다.
- @Data를 통해 Lombok을 사용 (@Setter 의존성 주입)

![springk](/images/springk/springk04-19.png)
- src/test/java에 테스트파일 생성
- @RunWith : JUnit 프레임워크의 테스트 실행 방법을 확장할 때 사용하는 어노테이션이다. SpringJUnit4ClassRunner라는 JUnit용 테스트 컨텍스트 프레임워크 확장 클래스를 지정해주면 JUnit이 테스트를 진행하는 중에 테스트가 사용할 애플리케이션 컨텍스트를 만들고 관리하는 작업을 진행해준다.
→JUnit에 내장된 Runner대신 그 클래스를 실행
- @ContextConfiguration : 자동으로 만들어줄 애플리케이션 컨텍스트의 설정파일위치를 지정

![springk](/images/springk/springk04-20.png)

![springk](/images/springk/springk04-21.png)

<br><br>

### Oracle DB 연동
- Build Path를 통해 Ojdbc 라이브러리를 추가한다.
(Maven으로 추가 시, 에러발생함으로 로컬환경에서 추가하도록 하자.)
- pom.xml에 spring-jdbc 라이브러리 추가

![springk](/images/springk/springk04-22.png)

- src/test/java에 테스트 파일을 생성한다.
- junit test 실행 결과 정상으로 나타난다.
<br><br>

### mybatis연동
spring에서는 mybatis를 쉽게 연동하기위해 라이브러리를 지원한다.

- pom.xml에 해당 라이브러리들을 추가한다.
HikariCP/spring-tx / mybatis / mybatis-spring
![springk](/images/springk/springk04-23.png)

- root-context.xml 설정
- hikariCP라이브러리를 사용하여 상기와 같이 Connection Pool을 한다.
(상기예제의 diriverClassName, jdbcUrl 은 추후 log4jdbc설정을 위해 변경된 값이다. 주석처리한 부분이 원래 값)
- dataSource 객체 생성 (ref = hikariConfig)
- sqlSessionFactory 객체 생성(ref = dataSource)
- mybatis-sping:scan을 사용하여 mapper패키지의 xml파일 및 인터페이스 파일을 검색하고 사용한다.

먼저 DB와 연동되는지 확인해보자.

![springk](/images/springk/springk04-24.png)
- @Setter 를 사용하여 DataSource 객체 및 SqlsessionFactory객체 생성
- DB를 연결하고 잘 연결되었는지 확인하기 위해 session과 con 을 출력한다.

>try 사용 시, 괄호안의 코드를 작성하면 try가 끝날때 내용을 자동으로 close 해준다.

DB와 연동이 잘된다면 mybatis설정이 잘되었는지 확인해보자.

![springk](/images/springk/springk04-25.png)

![springk](/images/springk/springk04-26.png)
- mybatis로 연동될 인터페이스 생성
- src/main/resources 에 인터페이스의 경로와 같도록 폴더를 생성하고 그안에 xml파일을 생성한다.
- 이전에 root-cotntex.xml에서 mybatis-spring : scan 경로와 같도록 해야한다.(자동으로 scan하기 위해서)

![springk](/images/springk/springk04-27.png)
- 테스트 파일 작성 및 실행
<br><br>

### log4jdbc-log4j2 설정
sql 관련 log를 보다 가시성이 높게 표현되도록 변경해주는 라이브러리이다.
- pom.xml에 해당라이브러리를 추가한다.

![springk](/images/springk/springk04-28.png)
- src/main/resources에 log4jdbc2.properties를 추가한다.

![springk](/images/springk/springk04-29.png)
- root-context.xml 의 Connection Pool 세팅의 value값을 변경한다.
(HikariCP 라이브러리 사용)

다시 테스트파일을 실행해보면 다음과 같은 결과가 나온다.

![springk](/images/springk/springk04-30.png)
<br>

### MVC Controller
Spring-MVC 패턴에서의 Controller의 역할을 알아보자.

- HttpServletRequest, HttpServletResponse를 거의 사용할 필요 없이 구현 가능
- 다양한 타입의 파라미터 처리, 리턴 타입이 가능
- GET, POST 방식에 대한 어노테이션 처리 가능
- 상속/인터페이스 방식 대신 어노테이션 만으로도 설정 가능

컨트롤러를 사용하여 톰캣서버를 실행해보자.

- pom.xml의 버전변경 (Spring legacy 로 생성된 lib들의 버전이 낮음)
spring → 5.0.7 / javax.servlet-api → 3.1.0 / 
maven-compiler-plugin → 2.5.1

![springk](/images/springk/springk04-31.png)
- 웹프로젝트 가능한 절대경로를 이용하는 구조를 사용하는것이 바람직함으로, Tomcat의 Module메뉴를 이용하여 ‘/’ 경로로 프로젝트가 실행될 수 있도록 처리한다.

![springk](/images/springk/springk04-32.png)

![springk](/images/springk/springk04-33.png)

![springk](/images/springk/springk04-34.png)

![springk](/images/springk/springk04-35.png)
- servlet-context.xml 에 해당 패키지안을 찾아내도록 설정한다.
- @Controller 를 사용하여 컨트롤러를 찾아낼 수 있도록한다.
- RequestMapping(“/sample/*”) 주소 /sample/로 들어올시, 해당 컨드롤러에 매핑된다.
- RequestMapping 과 GetMapping 을 사용하여 각각 mapping 하였다.
GetMapping get방식만 data를 받을 수 있다.
RequestMapping 은 get/post를 모두 지원하며 두방식 모두 지원시 배열로 처리할 수 있다.
- void 함수로 return 값이 없으면 매핑 주소값을 return 한다.

#### 파라미터 수집
spring에서는 파라미터가 자동으로 수집되는 기능을 지원한다. 
즉, request.getParameter()를 이용하는 불편함이 없다. 예제를 통해 살펴보자.

![springk](/images/springk/springk04-36.png)

![springk](/images/springk/springk04-37.png)

![springk](/images/springk/springk04-38.png)
- @Data 어노테테이션을 찾아 파라미터값으로 가진다.

#### 파라미터 형변환
파라미터수집을 다른용어로는 binding이라고 한다. 자동으로 변환이 가능한 data들은 변환을 하지만, 변환처리가 필요한 경우도 있다.
예를들어 문자를 Date 타입으로 변환하기 위해서는 다음과같은 과정이 필요하다.

![springk](/images/springk/springk04-39.png)

![springk](/images/springk/springk04-40.png)

![springk](/images/springk/springk04-41.png)
- InitBinder를 사용하여 형변환을 할 수 있다.
- DateTimeFormat 으로 대체 가능하다.(Chef 클래스 주석부분)

#### Json 변환
@ResponseBody를 사용하여 객체를 Json 타입으로 변환하여 출력이 가능하다.

- pom.xml에 jackson 라이브러리 추가

![springk](/images/springk/springk04-42.png)

![springk](/images/springk/springk04-43.png)
- @ResponseBody 를 사용하여 Json객체로 출력하였다.

#### ResponseEntity 타입
- 원하는 헤더 정보 및 데이터를 전달 할 수 있다.
- HttpHeaders 객체를 전달해 원하는 헤더 메세지로 가공이 가능하다.
예제를 통해 살펴보자.

![springk](/images/springk/springk04-44.png)

![springk](/images/springk/springk04-45.png)
컨트롤러에서 리턴값을 ResponseEntity값으로 선언하였다.
(데이터 타입이다. 어노테이션이 아님)

- 쌍따옴표를 사용하기 위해 \ 사용
- 결과값을 보면 알 수 있듯이,
HttpStatus.OK → status 200(정상) 
header → 결과페이지의 헤더값 (Json타입) 라는 상태 코드를 전송한다.
<br><br>

### 파일업/다운로드
#### 파일업로드
파일업로드 방법은 여러가지가 있는데 Servlet3.0이하버전에서 일반적으로 사용했던 가장 일반적인 commons-fileupload 방법에대해 알아보자.

- pom.xml에 commons-fileupload 라이브러리를 추가한다.

![springk](/images/springk/springk04-46.png)

![springk](/images/springk/springk04-47.png)

![springk](/images/springk/springk04-48.png)

![springk](/images/springk/springk04-49.png)


- servlet-context.xml 아래와 같이 설정한다.
maxUploadSize : Request로 전달될 수 있는 최대의 크기
maxUploadSizePerFile : 하나의 파일 최대크기
maxlnMemorySize : 메모리상에서 유지하는 최대의 크기
uploadTempDir : 업로드 파일 경로(‘file:/’로 시작한 이유는 절대경로를 이용하려면 URI형태로 제공해야되기 때문에)
- defaultEncoding는 한글처리를 해준다.
- Controller를 통해서 views/sample/exUpload.jsp 로 간다.
- exUpload.jsp에서 파일을 올릴수 있는 input태그 작성 (form태그의 속성들 주의)
- form 이 post방식으로 컨트롤러로 오기때문에 @PostMapping 사용한다.
- 값이 여러개임으로 ArrayList사용 타입은 MultipartFile이며 변수명은 input태그의 name값으로 한다.
- foreach(file->{});를 사용하여 log 출력
<br><br>