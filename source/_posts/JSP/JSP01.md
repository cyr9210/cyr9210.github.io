---
title: 웹 어플리케이션 서버,Servlet, JSP
date: 2018-12-27 17:58:32
tags: JSP
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
# KOSTA DAY38
## JSP
![JSP](/images/JSP_logo.png)
<!-- more -->
### 웹 어플리케이션 서버
#### setting
![JSP](/images/jsp/JSP01-01.png)
- 상기내용을 체크시 web.xml이 생성된다.

#### 웹 어플리케이션의 동작원리
- 클라이언트와 서버의 요청과 응답 관계속에서 이루어진다.
- 클라이언트는 서비스를 요청하는 사용자가 되고
- 서버가 제공해 주는 서비스를 클라이언트가 응답 받으면 웹 브라우저에 결과화면으로 출력된다.

#### 웹 어플리케이션 동작 순서
![JSP](/images/jsp/JSP01-02.png)
1. 사용자가 웹 브라우저의 주소 란에 특정 사이트의 주소를 입력하게 되면 웹 브라우저가 해당 웹 서버에 홈페이지를 요청하는 것이 된다.
1. 해당 웹 서버에는 웹 브라우저의 요청이 있을 경우 이를 받아들여 요청한 페이지를 응답할 책임을 지고 있다. 
요청한 페이지는 웹 서버에서 바로 공급되지 못하고 웹 어플리케이션 서버(WAS : Web Application Server)에서 다양한 로직이나 데이터베이스와의 연동을 통해서 완성되어야만 공급된다.
1. 웹 어플리케이션 서버(WAS)는 웹 서버가 클라이언트에게 제공할 페이지를 완성하기 위해서 이에 필요한 로직이나 데이터베이스와의 연동과 데이터 처리를 담당한다.
1. 로직이나 데이터베이스 작업 처리 결과를 웹 서버에게 보낸다.
1. 웹 서버는 이 결과를 다시 클라이언트 측 웹 브라우저에 응답하게 된다.

#### 웹 어플리케이션의 구성요소
- 웹브라우저 : 사용자(클라이언트)가 요청한 웹서버의 결과를 화면에 출력해 볼 수 있도록하는 어플리케이션
- 웹서버 : 요청을 받아 이에 알맞는 결과를 웹브라우저에 응답해주는 역할을한다. 요청에 필요한 페이지의 로직이나 데이터베이스와의 연동을 위해서 어플리케이션 서버에 이들의 처리를 요청한다.
- **웹 어플리케이션 서버 : 요청한 페이지의 로직이나 데이터베이스와의 연동을 처리하는 부분**
- 데이터 베이스 : 데이터를 정보를 저장하는 곳 예를 들어 회원의 정보나 게시판에 올린 글에 대한 정보 등을 저장한다.

#### 자바의 web 기술
- J2SE(Java 2 Standard Edition)
- J2EE(Java 2 Enterprise Edition)
- J2ME(Java 2 Micro Edition)
이중에서도 서블릿과 JSP는 J2EE를 구성하는 기술 중 하나이다. 
최근에는 서블릿 및 JSP 를 사용하지 않지만, 현재 국내에서는 많이 사용한다.

#### J2EE
J2EE는 클라이언트/서버 환경이나 웹 환경의 서버 단에서 수행되는 프로그램을 자바로 구현하고자 할 때 사용하는 자바 기술이다.
![JSP](/images/jsp/JSP01-03.png)
- JSP는 html코드 안에 java 언어를 포함한다.
- servlet는 java 코드안에 html코드를 포함한다.
- JSP와 servlet은 사실상 같다.
WAS는 JSP에 작성된 코드를 자동으로 servlet코드로 변환한다.
- 출력은 JSP, 요청을 받는것은 servlet이 적합하다.
<br><br>

### Servlet
- 서블릿이란 서블릿 클래스로부터 만들어진 객체이다.
- 서블릿 클래스는 javax.servlet.http.HttpServlet 클래스를 상속하도록 만들어야 한다.
- 클래스는 public 으로 만들어야 한다.
- javax.servlet.http.HttpServletRequest 와 javax.servlet.http.HttpServletResponse 타입의 파라미터를 받아야 하고 메서드 밖으로 javax.servlet.ServletException 과 java.io.IoException 을 던질 수 있도록 선언해야 한다.

#### 예제
![JSP](/images/jsp/JSP01-04.png)
- servlet은 java언어로 이루어져있기 때문에 출력을 위해서는 위의 예제 내용과 같이 html언어로 print 해야한다.
- ‘@webServlet(“/HelloServlet”) 에 hello라는 url주소를 추가하기 위해서 → ‘@WebServlet({“/HelloServlet”, “/hello”}) 로 변경하였다.

상기내용과 같이 서블릿에서 출력을 하기위해서는 불편한 부분이 많다.
때문에 jsp를 사용하여 보다 편하게 출력하는 방법에 대해 알아보자.

![JSP](/images/jsp/JSP01-05.png)
- <% %> 사이에 Java 코드 입력하여 사용한다.
- 출력 시, <%= %>사이에 출력할값을 지정한다.

앞서 Servlet은 요청을 받는것에, JSP는 출력에 적합하다고 언급을 한 적이 있다. 그 방법에 대해 알아보자

![JSP](/images/jsp/JSP01-06.png)
- 먼저 값을 입력받을 input태그를 만들기 위해 jsp를 사용하여 만들었다.
- 받은 입력값을 관련 servlet 주소로 전송한다.
- servlet안에서 해당 결과값을 계산하고 결과값을 Dispatcher를 사용하여 결과값을 출력할 JSP를 연결한다.

여기서 만약 Dispatcher가 아닌 Redirect를 사용하였다면 어떻게 될까?

![JSP](/images/jsp/JSP01-09.png)
- 상기 예시와 같이 sendRedirect를 사용 시, 다른 request객체를 가지기 때문에 값이 나오지 않는다.
- Dispatcher 사용 시, 결과값을 출력하는 url은 input에서 요청한 페이지
Redirect 사용 시, 결과값을 출력하는 url 을 가진다.
<br><br>

### JSP
JSP기술에서 웹애플리케이션을 구현할 때, 작성하는 코드

![JSP](/images/jsp/JSP01-12.png)
- HTML문서의 사이에 JSP문법의 코드가 삽입되는 형태
- 웹 컨테이너는 JSP 페이지 전체를 서블릿 클래스의 소스 코드로 변환하고, 다음에 소스 코드를 컴파일 후 결과를 가지고 서블릿 객체를 만들고 서블릿 객체를 초기화해서 서블릿을 만든다.
- 웹 브라우저로부터 URL이 왔을 때 실행되는 것은 서블릿이다.

#### 기초문법
- JSP의 문법에는 세가지 형태가 있다.
    -  <% %>
    -  ${ }
    -  XML태그 형태 (ex : <jsp:forward\><C:if\>)

#### 지시자
![JSP](/images/jsp/JSP01-13.png)
- <% %>는 지시자와 스크립팅 요소로 나누어진다.
    - 페이지 지시자 : <%@ 사전에 설정해야 하는 부분%>
    - 스크립틀릿 : Java코드가 들어가는 부분
    - 익스프레션-표현식(출력) : <%= 출력되는부분 %>

#### 지시자의 종류
![JSP](/images/jsp/JSP01-14.png)
- 모두 <%@ 으로 시작하고 %>로 끝나야 한다.
- <%@ 바로 다음에는 지시자 이름이 와야하고, 지시자 이름 다음에는 여러가지 애트리뷰트가 올 수 있다.
- page 지시자는 JSP페이지 전체에 적용되는 정보를 기술하기 위해 사용된다.

#### Include 지시자
![JSP](/images/jsp/JSP01-15.png)
include의 소스가 포함된다. 페이지를 모듈화 한다.
- Include 지시자는 다른 JSP페이지나 HTML 문서를 불러다가 현재 JSP 페이지의 일부로 만들기 위해 사용된다.
- 불러온대상은 file이라는 애트리뷰트를 이용해서 지정할 수 있으며 애트리뷰트의 값은 지시자가 속하는 JSP페이지르 기준으로 상대적인 URL로 해석된다.

#### 예제
![JSP](/images/jsp/JSP01-16.png)
- header, menu, footer 부분을 모두 include를 사용하여 기본 페이지의 레이아웃을 구성하였다.
- 각 부분의 수정이 필요할경우 해당 HTML파일 또는 jsp 파일을 수정 시 전체가 수정된다.

#### Taglib 지시자
![JSP](/images/jsp/JSP01-17.png)
- 기존의 액션태그가 아닌 사용자가 지정한 태그를 사용한다.
- Taglib 지시자는 JSP문법 중 하나인 액션을 사용할 때 필요하다.
- Taglib 지시자는 액션이 속한 라이브러리를 설치해야만 사용할 수 있다.

#### JSP 내장변수(객체)
![JSP](/images/jsp/JSP01-18.png)
- JSP 페이지의 내장변수는 선언하지 않고도 사용할 수 있는 변수이다.

#### 예제
![JSP](/images/jsp/JSP01-19.png)
- application은 내장객체
- application.getRealPath(path)를 통해 path(/message/notice.txt) 절대적 주소를 fullpath변수에 저장한다.
- 해당 텍스트파일의 내용이 없을때까지 내용을 출력한다.
<br><br>
