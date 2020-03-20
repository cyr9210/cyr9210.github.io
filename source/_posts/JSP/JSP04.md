---
title: 설계모델, 모델1설계방식, 모델2설계방식(MVC패턴)
date: 2019-01-04 19:45:33
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
# KOSTA DAY42
## JSP
![JSP](/images/JSP_logo.png)
<!-- more -->
### 설계모델
- 애플리케이션을 설계할 때 가장 먼저 해야 할 일은 프로그램이 해야 할 일을 적당한 크기로 나누어서 모듈화하는 것이다.
- JSP 규격서의 초기 버전인 JavaServer Pages– Specification 0.92에서 소개하고 있는 설계 모델인 모델 1과 모델 2에서도 웹 애플리케이션이 해야 할 일을 데이터 입력, 데이터 처리, 데이터 출력으로 구분하여 모듈화하는 방법을 제시하고 있다.
- 모델 1은 비교적 간단한 웹 애플리케이션에 적합한 설계 모델이며, 이 모델에서는 애플리케이션을 두 종류의 모듈로 나누어서 각각 JSP 페이지와 자바빈 클래스로 구현하도록 제안하고 있다.

### 모델1 설계방식
#### 모델1 개요
![JSP](/images/jsp/JSP04-01.png)
- JSP만 이용하여 개발하는 경우
- JSP + Java Bean을 이용하여 개발하는 경우
- Model2의 Controller 개념이 모호

#### 장점
- 개발속도가 빠름
- 개발자의 기술적인 숙련도가 낮아도 배우기 쉬워 빠르게 적용가능

#### 단점
- JSP페이지에서 프레젠테이션 로직과 비지니스 로직이 혼재되어 복잡
- 로직의 혼재로 인해 개발자와 디자이너의 작업 분리가 어려움
- JSP 코드의 복잡도로 인해 유지보수가 어려워짐

웹 애플리케이션이 복잡해지고 사용자 요구가 증가함에 따 라 새로운 개발방식을 요구

### 모델2 설계방식
#### 모델2 개요
![JSP](/images/jsp/JSP04-02.png)
- GUI 개발모델인 MVC를 웹 애플리케이션에 적용하여 구현한 방식
- Application의 역할을 Model — View — Controller로 분리

#### 장점
- 비즈니스 로직과 프리젠테이션의 분리로 인해 어플리케이션이 명료해지며 유지보수와 확장이 용이함
- 디자이너와 개발자의 작업을 분리해 줌

#### 단점
- 개발초기에아키텍처디자인을위한시간의소요로개발기간이늘어남 
(개발자가 설계할경우) ( 요즘에는 프레임워크 사용한다. -> 틀이 짜여있음)
- MVC 구조에 대한 개발자들의 이해가 필요함

#### Model
- Business Logic을 담당한다. → Java Bean으로 구현
- Business Service(Manager) →Business Logic의 workflow를 관리
- DAO (Data Access Object) → Database와 연동하는 Business Logic을 처리.

#### View
- Client에게 응답을 처리한다. → JSP로 구현

#### Controller
- 클라이언트의 요청을 받아 Model과 View사이에서 일의 흐름을 조정한다.
- Servlet
- Client의 요청을 받아 Client가 보낸 Data를 읽고 검사한다.
- Model에게 Business Logic을 요청한다.
- Model의 처리 결과에 맞는 View에게 응답을 요청한다.

#### 코드진행 과정
1. 요청
1. Controller(Servlet)
    - url판별
    - action생성 → Model(dao) → mybatis → DB
    - Model 호출(data를 입력)
    - View data전달 타입 결정
    - View path 결정
1. View
    - 화면에 data 출력

#### 주의사항
- sendRedirect 를 사용하는것은 mvc패턴 사용의 의미가 없다.
모든요청은 Controller가 받아야한다.
- 어노테이션 or Servlet(Controller)생성시 설정
ex) *.do → 페이지 내 모든 .do 호출 시

![JSP](/images/jsp/JSP04-03.png)

#### 예제
![JSP](/images/jsp/JSP04-04.png)
- 페이지 로딩 시, → list.do로 이동

![JSP](/images/jsp/JSP04-05.png)

![JSP](/images/jsp/JSP04-06.png)

![JSP](/images/jsp/JSP04-07.png)
- request.getRequestURI 를 통해 전체 URI호출
→ /MVC/list.do
- getContextPath를 통해 프로젝트 네임
→ /MVC
- requestURI.subString(ContextPath.length +1) → list.do
ContextPath.length +1 = 5
- command가 list.do와 일치한다면, action클래스를 implemets하는 ListAction을 생성한 후, 오버라이딩한 execute함수를 호출한다.
- execute함수는 ActionForward를 리턴하는데 ActionForward객체는 전송타입을 결정할 boolean값과 이동될 경로를 결정하는 path값을 가진다.
- ListAction객체는 list라는 객체를 “list”에 담고 dispatcher방식으로 list.jsp로 이동한다.
<br><br>