---
title: JSP액션, useBean/setProperty, 한글처리, JDBC, DAO/DTO, mybatis
date: 2019-01-02 18:25:16
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
# KOSTA DAY39
## JSP
![JSP](/images/JSP_logo.png)
<!-- more -->
### JSP액션
자바 코드에 의해 객체를 생성하고 사용하는 것이 아니라 태그를 이용하여 객체의 생성하고 사용하는 것 → JSP 태그 or JSP 액션

- scope * 객체가 사용될 수 있는 범위(Life cycle) 
    - page : pageContext에 저장
    - request : HttpServletRequest에 저장
    - session : HttpSession에 저장
    - application : ServletContext에 저장
<br><br>

### useBean/setProperty
![JSP](/images/jsp/JSP02-01.png)
- useBean : 자바빈 객체를 생성한다.
- setProperty : 자바빈에 속성값을 부여한다.
- name 속성에 기술된 이름이 jsp:useBean을 이용해서 먼저 정의해야한다.
- input 태그의 name 과 객체내 필드의 name과 동일 할 경우에만 사용 가능하다.J

#### 예제
![JSP](/images/jsp/JSP02-02.png)
- useBean 액션태그를 사용하여 register JavaBean객체를 생성
- setProperty 태그를 사용하여 form으로부터 받은 내용들을 담는다.
(form의 input_name과 객체의 name이 같아야한다.)
- RegisterDao클래스의 insertRegister 메소드를 호출하여 해당 객체의 내용들을 출력한다.
<br><br>

###  한글처리
![JSP](/images/jsp/JSP02-03.png)
- get방식 → server.xml 의 Connector를 찾아서 해당 내용을 추가한다.
<Connector URIEncoding=”UTF-8"/>
서버에 publish to the server 버튼을 실행한다.

![JSP](/images/jsp/JSP02-04.png)
- post방식 → servlet에 해당 내용을 추가한다.
request.setCharacterEncoding(“utf-8”);

![JSP](/images/jsp/JSP02-05.png)
<br>

###  JDBC
- JDBC란, Java가 DB(data base)와 통신할 수 있게 해주는 API 이다.

#### setting
![JSP](/images/jsp/JSP02-06.png)
- eclipse에서 Oracle의 API를 이용하기위해서 Oracle 라이브러리를 eclipse 복사한다.
(해당 라이브러리는 oracle 홈페이지에서 다운로드 가능)

#### JDBC실행 순서
![JSP](/images/jsp/JSP02-07.png)
- driver 로딩 → connection연결 → Statement생성 → query문 작성 → query문 처리

##### 예제
![JSP](/images/jsp/JSP02-08.png)
- driver, url주소 설정
- 연결할 DB의 id, password 입력
- executeUpdate() 문 사용하여 query문 처리
executeUpdate → 생성,수정,삭제 / executeQuery → 읽기(select문)
- 역순으로 close();

#### PreparedStatement
![JSP](/images/jsp/JSP02-09.png)
- 앞서 statement를 사용한 방식과 유사하나 쿼리문을 ‘?’를 활용하여 그 후 PreparedStatement를 생성한다.

##### 예제
![JSP](/images/jsp/JSP02-10.png)
- 추 후 선언할 내용들에대해 ‘?’를 활용하여 sql문을 작성한다.
- prepareStatement를 생성하고, 그후 setString메소드를 통해 ‘?’에 들어갈 값을 설정한다.
- 역순으로 close();

내가 해당예제를 풀며 생각한 주의사항은 ?를 입력할 시 ‘ ’를 생략하고 넣어야한다는 것이다. 
나는 pstmt를 활용하여 sql문 작성 시, ‘?’ 로 작성하여 해결하기 위해 오랜시간을 소요했다.
또한 connection, Statement(prepareStatement), resultset을 반드시 close()해야 한다는것을 주의하자.(역순으로)
<br><br>

### DAO/DTO
![JSP](/images/jsp/JSP02-11.png)
- DAO (Data Access Object) : DB에 접근(통신)하는 객체
- DTO (Data Transfer Object) : DB의 data를 Java의 Data로 변환시키기 위한 객체 (VO라고도 부른다.)

#### 예제
![JSP](/images/jsp/JSP02-12.png)
- DTO → Board클래스 생성
- DAO → 객체 생성시 오라클 드라이버 로딩
insertBoard 메소드 해당 data를 추가하는 내용을 JDBC 활용하여 작성
- 제목/글쓴이/내용 작성한 내용을 form_action3.jsp에 넘겨준다.
- form_action3는 넘겨받은 내용을 useBean 액션태그를 사용해 bean객체를 생성하여 담는다.
- 담긴 객체를 파라미터 값으로 하여 insertBoard 메소드 호출

상기 예제를 보면 알 수 있듯이, 추 후에 사용자는 노란색 박스부분 처럼insertBoard메소드를 호출하는것으로 게시물들을 추가 할 수 있다
<br><br>

### mybatis
개발자가 지정한 SQL, 저장프로시저 그리고 몇가지 고급 매핑을 지원하는 퍼시스턴스 프레임워크

- JDBC로 처리하는 상당부분의 코드와 파라미터 설정 및 결과 매핑을 대신해준다.
- 데이터베이스 레코드에 원시타입과 Map 인터페이스 그리고 자바 POJO를 설정해서 매핑하기 위해 XML과 애노테이션을 사용할 수 있다.

#### XML에서 SqlSessionFactory 빌드하기
- 모든 마이바티스 애플리케이션은 SqlSessionFactory 인스턴스를 사용한다.
- SqlSessionFactory인스턴스는 SqlSessionFactoryBuilder를 사용하여 만들 수 있다.
- SqlSessionFactoryBuilder는 XML설정 파일에서 SqlSessionFactory인스턴스를 빌드 할 수 있다.
- 설정을 위해 클래스 패스 자원을 사용하는 것을 추천하나, 파일 경로나 file://URL 로부터 만들어진 InputStream인스턴스를 사용할 수도 있다.
- 마이바티스는 클래스패스와 다른위치에서 자원을 로드하는 것으로 좀 더 쉽게 해주는 Resources라는 유틸성 클래스를 가지고 있다.

![JSP](/images/jsp/JSP02-13.png)
상기 내용은 POOLED 타입으로 설정 된 mybatis-config.xml 이다.
해당내용을 JNDI타입으로 변경하면 아래와 같이 가능하다.

![JSP](/images/jsp/JSP02-14.png)
- url, username, password등 DB연결을 위한 정보들을 server/context.xml의 <Resource/>태그 안에 추가한다.
- 추가한 Resource의 name을 mybatis-config.xml에 맞추어 준다.

![JSP](/images/jsp/JSP02-15.png)
작성된 xml은 상기예시와 같이 사용된다.

- xml파일의 내용을 가져올 InputStream 객체 생성 후 Resource내용을 가져온다.
- SqlSessionFactoryBuilder()함수를 호출하여 담은내용을 build한다.

이렇게 만들어진 SqlSessionFactory를 DB에 연결이 필요할때 마다, openSession()하여 사용하고, 사용을 마친후 close()해준다.

> **tip**
try($){내용} → $의 위치에 openSession해줄경우 자동으로 close된다.

#### Mapping
- mapping을 위한 interface객체가 필요하다.
- 생성된 interface 객체의 주소를 참조하는 xml파일을 생성하고 DB에서 실행할 Query문들을 매핑한다.

![JSP](/images/jsp/JSP02-16.png)
![JSP](/images/jsp/JSP02-17.png)
- 인터페이스 객체는 리턴타입 및 파라미터값만 선언한다.
- xml파일에 매핑
<mapper namespace= “인터페이스 객체 주소”>
- \#{}를 사용하여 파라미터값 or 파라미터 객체안의 값들을 사용
- return 타입을 int로 선언한 이유는 DB수정 시, commit을 하기 위해서 이다.

![JSP](/images/jsp/JSP02-18.png)
상기예시와 같이 선언 및 매핑한 함수를 사용한다.

- SqlSession.getMapper(인터페이스객체.class).선언함수;
- int re 로 결과값을 받는다.
if(re == 1) → 정상 구현 
실패시, 초기화값 re = -1
- 정상 구현 됬을 시, commit 아닐경우 rollback

#### typeHandler
DB의 자료형을 적절한 Java 자료형으로 변화하기 위해 사용한다.

#### CRUD타입
내장메소드를 사용한다.

- selectone : 하나의 데이터를 가져온다.
    - selectone(String queryid) → 쿼리문을 통해 데이터를 가져온다.
    - selectone(String queryid, Object parameter) → 쿼리문에 파라미터값을 대입하여 데이터를 가져온다.
- selectlist : 하나이상의 데이터를 가져온다.
- update : 수정
- delete : 삭제
- insert : 삽입

DML(insert, update, delete) 사용 시, 트랜잭션 처리가 반드시 필요하다.

#### SQL코드 재사용
중복되는 query문을 include해서 사용 가능하다.
![JSP](/images/jsp/JSP02-19.png)


#### Result 매핑
DB컬럼과 객체필드의 name이 다를 때, resultMap을 사용하여 매핑할 수 있다.(아래 예시는 DB의 컬럼과 객체필드의 name이 같으므로 올바른 예제는 아니다.)
![JSP](/images/jsp/JSP02-20.png)

#### 동적 Query문 (동적 SQL)
![JSP](/images/jsp/JSP02-21.png)
![JSP](/images/jsp/JSP02-22.png)
- DB에 전달할 파라미터값을 두개이상 넣어야할때는 객체화를 시키거나 hashmap을 만들어서 전달해야한다.

![JSP](/images/jsp/JSP02-23.png)
- area객체가 있다면 item에 area값을 담는다.
- open, close는 쿼리문의 양끝에 붙으며, item이 2개이상의 경우 separator로 구분한다.
- item에 담겨져있는 내용은 원래 literal(문자)형으로 나와야 하는 부분이기 때문에 #이 아닌 $을 사용하여 호출한다.
상기 예시에 작성된 query문은 밑줄 친 부분의 sql query문과 같은 내용이다.

![JSP](/images/jsp/JSP02-24.png)
- getParameterValues(name) : 같은 name의 여러값들을 가져올때 사용한다.
- searchKey 양 끝에 %를 담은 이유는 query문의 일부를 String 으로 저장하기 위해서 이다.
<br><br>