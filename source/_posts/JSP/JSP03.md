---
title: Cookie, Session,익스프레션, JSTL, 필터
date: 2019-01-03 18:52:29
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

# KOSTA DAY41
## JSP
![JSP](/images/JSP_logo.png)

### Cookie
쿠키 기술은 전달한 데이터를 웹 브라우저로 보냈다가 웹 서버 쪽으로 되돌려 받는 방법을 사용한다.
![JSP](/images/jsp/JSP03-01.png)
- 첫 번째 웹 컴포넌트가 웹 브라우저로 HTML 문서를 보낼 때 전달한 데이터를 함께 보내며, 웹 브라우저는 데이터를 저장해 두었다가 두 번째 웹 컴포넌트를 호출할 때 URL과 함께 웹 서버로 보낸다.

#### 새로운 쿠키 데이터를 저장하는 방법
- Cookie 클래스의 객체를 만든다.
- addCookie 메서드를 호출해야 한다.
- Cookie 클래스는 javax.servlet.http 패키지에 속하며, 클래스의 객체를 만들 때는 쿠키의 이름과 값을 파라미터로 넘겨줘야 한다.
- 파라미터는 모두 String 타입이 되어야 하므로 쿠키의 값이 수치일 경우 문자 데이터로 만들어줘서 넘겨줘야 한다.

![JSP](/images/jsp/JSP03-02.png)
- addCookie 메서드를 호출할 때 웹 브라우저로 쿠키를 보내는 기능을 하며, JSP 페이지에서는 response 내장 객체애 대해, 서블릿 클래스에서는 doGet, doPost 메서드의 두 번째 파라미터에 대해 호출 한 후 Cookie 객체를 파라미터로 넘겨줘야 한다.

![JSP](/images/jsp/JSP03-03.png)
- 메서드를 통해 웹 브라우저로 전송된 쿠키를 실제로 저장하는 일은 웹 브라우저가 하도록 되어 있다.
- 웹 브라우저가 쿠키를 저장할 때 웹 서버의 주소도 함께 저장해 놓는다.

#### 예제
로그인 성공 시, 쿠키가 저장되는 과정을 보자.

![JSP](/images/jsp/JSP03-04.png)
- m_name(“홍길동”)을 쿠키에 담기위해 utf-8로 encode한다.
- new 연산자를 사용하여 쿠키를 생성하고 name값을 담는다.

저장된 쿠키를 활용하는 작업에 대해 보자.

![JSP](/images/jsp/JSP03-05.png)
- 저장된 쿠키를 쿠키배열(cookies)에 담는다.
- cookies에 담긴 쿠키의 값이 null일 경우 → loginform으로 다시 이동
null이 아닌경우 → cookies에 담긴 쿠키의 값을 꺼내어 decode한 후, name에 담는다.
- null이 아닌경우에도 cookies에 담긴 값이 하나이거나, JSESSIONID인 경우에는 loginform으로 다시이동

로그아웃 시, 쿠키를 삭제하는 작업을 보자.


![JSP](/images/jsp/JSP03-06.png)
- cookies에 담긴 쿠키들의 setMaxAge를 0으로 세팅(쿠키 유지 시간)
- 세팅된 쿠키값으로 쿠키값을 덮어쓴다.
<br><br>

### Session
![JSP](/images/jsp/JSP03-07.png)
세션 기술은 웹 브라우저를 거치지 않고 웹 서버에 있는 데이터 영역을 통해 데이터를 전달하는 방법이다.

#### 세션과 쿠키의 차이점
- 쿠키 : 사용자의 정보를 클라이언트쪽에 저장
- 세션 : 사용자의 정보를 서버쪽에 저장

#### 세션기술의 사용방법
- getSession메서드가 리턴한 HttpSession 객체에 대해 setAttribute라는 메서드를 호출하면 세션 데이터 영역에 데이터를 저장할 수 있다.

![JSP](/images/jsp/JSP03-08.png)
- HttpSession 객체에 대해 getAttribute라는 메서드를 호출하면 세션 데이터 영역의 데이터를 가져올 수 있다.
- 메서드는 데이터 이름을 파라미터로 받고, 그에 해당하는 데이터 값을 리턴한다.
- 주의 할 점은 메서드의 리턴 타입은 java.lang.Object 이므로 데이터 값을 본래의 타입으로 사용하려면 다음과 같이 캐스트 연산을 해야 한다.

![JSP](/images/jsp/JSP03-09.png)
세션을 이용해서 할 일을 모두 마치고 나면 invalidate라는 메서드를 호출해서 세션을 끝낼 수 있으며, 메서드는 HttpSession 객체에 대해 호출해야 한다.

![JSP](/images/jsp/JSP03-38.png)
- 서블릿 클래스에서 세션을 시작하기 위해서는 doGet, doPost 메서드의 HttpServletRequest 파라미터에 대해 getSession이라는 메서드를 호출해야 한다.
- 메서드는 세션 정보를 포함하는 javax.servlet.http.HttpSession 타입의 객체를 리턴한다.

![JSP](/images/jsp/JSP03-10.png)
- JSP 페이지에서는 JSP 페이지가 서블릿 클래스로 변환될 때 메서드를 호출하는 코드가 자동으로 추가 되기 때문에 getSession 메서드를 호출 할 필요가 없다.

#### 예제
로그인 성공 시, 정보를 세션에 저장하는 작업을 보자.


![JSP](/images/jsp/JSP03-11.png)
- getParameter로 받은 데이터를 name이라는 이름을 가진 session에 저장
- 상기예시는 jsp 페이지 임으로 getSession작업이 필요하지 없다.

session에 저장된 데이터 활용방법에 대해 보자.

![JSP](/images/jsp/JSP03-12.png)
- 저장한 session에서 값을 꺼내어 name변수에 담는다.

로그아웃 시, session끝내기 작업에 대하여 보자.(저장된 data삭제)

![JSP](/images/jsp/JSP03-13.png)
- invalidate함수를 호출하여 저장된 데이터들을 삭제한다.
<br><br>

### 익스프레션 언어
익스프레션 언어는 JSP 규격서가 처음 만들어질 때부터 있던 문법이 아니라 새롭게 추가된 문법이며, 이 문법을 이용하면 데이터를 출력하는 코드를 함축적이고 간결하게 구사할 수 있어 코드의 가독성과 유지보수 용이성에 큰 도움이 된다.

- 익스프레션 언어(expression language)란 같은 식을 중심으로 코드를 기술하는 언어이다.
- 연산자와 피연산자의 조합을 ${와}로 둘러싸서 표현한다.
- 익스프레션 언어의 유일한 목적은 식을 계산해서 그 결과를 출력하는 것이기 때문에 하나의 문법으로 효현할 수 있으며 이 문법을 EL식이라고 부른다.
- ‘식’위치에는 데이터 이름 하나로만 구성된 식이 들어갈 수도 있고, 연산자를 포함하는 식이 들어 갈 수도 있으며, 자바의 정적 메서드를 호출하는 식이 들어갈 수도 있다.

![JSP](/images/jsp/JSP03-14.png)
- EL 식에 있는 데이터 이름을 해석하는 순서는 사용 범위가 좁은 애트리뷰트부터 점점 더 사용 범위가 넓은 애트리뷰트 순으로 진행 된다.

![JSP](/images/jsp/JSP03-15.png)
- 순서에 상관없이 특정한 종류의 애트리뷰트를 짚어서 출력하고 싶을 때는 다음과 같이 표시하면 된다.(거의 사용하지 않는다.)

![JSP](/images/jsp/JSP03-16.png)

EL식 사용방법에 대해 예제로 알아보자.


![JSP](/images/jsp/JSP03-17.png)
![JSP](/images/jsp/JSP03-18.png)

- forward액션태그에 저장된 param값을 호출
- form으로 넘긴 data를 가져오는 방법 또한 동일하다.
- request.setAttribute로 넘긴 data를 호출
- request.setAttribute로 넘긴 객체의 data를 호출
- request.setAttribute로 넘긴 Hashmap의 data를 호출
<br><br>

### JSTL
표준 태그 라이브러리(JSP Standard Tag Library)의 약어이다.

#### JSTL을 가지고 할 수 있는일
- 간단한 프로그램 로직의 구사(자바의 변수 선언, if 문, for 문 등에 해당하는 로직)
- 다른 JSP 페이지 호출(<\c:redirect>, <\c:import>)
- 날짜, 시간, 숫자의 포맷

#### JSTL이란?
![JSP](/images/jsp/JSP03-19.png)
- JSP 페이지에서 접두어를 사용하기 위해서는 taglib 지시자를 이용해서 라이브러리의 URI 식별자와 접두어를 연결할 수 있다.
- Taglib* 지시자는 다른 지시자와 마찬가지로 <%@으로 시작해서 %>로 끝난다.
- 지시자에는 uri와 prefix라는 두 개의 애트리뷰트를 써야 하고 두 애트리뷰트에 각각 URI 식별자와 접두어를 값으로 주어야 한다.

#### 속성설정
JSTL은 커스텀라이브러리기 때문에 Taglib를 설정해야한다.
![JSP](/images/jsp/JSP03-20.png)
- 코어 라이브러리
- 포매팅 라이브러리

#### 코어 라이브러리
##### set/out
![JSP](/images/jsp/JSP03-21.png)

##### foreach
![JSP](/images/jsp/JSP03-22.png)

##### redirect
![JSP](/images/jsp/JSP03-23.png)
![JSP](/images/jsp/JSP03-24.png)
- redirect했던 orange라는 문자값을 fruit이라는 변수에 담는다.

##### if
![JSP](/images/jsp/JSP03-25.png)

##### choosewhen
![JSP](/images/jsp/JSP03-26.png)
- otherwise는 Java 언어 if문의 else와 같다고 보면 된다.

#### 포매팅 라이브러리
##### formatdate
![JSP](/images/jsp/JSP03-27.png)

##### formatNumber
![JSP](/images/jsp/JSP03-28.png)
- type = “currency” currencySymbol = “$” →화폐단위로 변환 및 심벌입력
- type = “percent” → 소수를 %값으로 변환
- pattern = “#0.00” → 소수둘째자리에서 반올림
<br><br>

### 필터
글자 그대로 여과기 역할을 하는 프로그램이다.

- 자바 클래스 형태로 구현해야 하며, 이 클래스를 필터 클래스(filter class)라고 한다.

![JSP](/images/jsp/JSP03-29.png)
- 초기화된 필터는 웹 브라우저와 웹 컴포넌트 사이에 위치하게 된다.
- 웹 브라우저가 웹 컴포넌트를 호출했을 때 대신 필터가 호출될 것이고, 필터는 필요한 사전작업을 수행한 다음에 웹 컴포넌트를 호출할 것이다.
- filter클래스는 filter인터페이스를 implemets 해야한다.


![JSP](/images/jsp/JSP03-30.png)
![JSP](/images/jsp/JSP03-31.png)
![JSP](/images/jsp/JSP03-32.png)

#### 예제
![JSP](/images/jsp/JSP03-35.png)
- /bean/* → bean폴더의 모든 페이지에 필터를 적용
필터클래스 생성 → 클래스명 입력후 Next → Filter mappings 의 Add또는 Edit를 통해 필터를 적용할 페이지를 입력한다.
(@(어노테이션)을 통해서도 매핑할 수 있다.)

![JSP](/images/jsp/JSP03-36.png)
- 한글처리를 doFilter에 함으로써 매핑된 페이지들은 모두 한글처리가 적용된다.

![JSP](/images/jsp/JSP03-37.png)
- getSession을 위한 request는 HttpServletRequest/Response객체 임으로 형변환 하였다.
- Servlet 페이지임으로, getSession메소드 호출과정이 없어서 직접 HttpSession객체를 만들고, getSession메소드를 호출하였다.
<br><br>