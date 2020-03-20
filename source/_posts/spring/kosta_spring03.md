---
title: \@RequestParam, \@Pathvariable(RESTful), tiles, File up/down, JSON, AOP, Transaction (KOSTA)
date: 2019-01-28 05:41:12
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
#  KOSTA DAY57
## Springframework

<!-- more -->

### RequestParam
- HTTP 요청 파라미터를 매핑
![springk](/images/springk/springk03-01.png)

- get 방식으로 data를 전달한다.
- parameter값에 @RequestParam 사용하여 변수를 받는다.
(RequestParam을 사용 시, 장점은 자동으로 형변환을 해준다는 점이다. 후에 나올 PathVariable도 마찬가지)
<br><br>

### PathVarialbe
![springk](/images/springk/springk03-02.png)
- URI 템플릿 변수에 접근할 때 사용
- RESTful 방식
    http://somehost/users/madvirus
    http://somehost/games/
    http://somehost/forum/board1/10
- @RequestMapping 어노테이션 값으로 {템플릿변수}를 사용한다.
- @PathVariable 어노테이션을 이용해서 {템플릿변수}와 동일한 이름을 갖는 파라메터를 추가한다.
<br><br>

### tiles
웹 페이지의 상단이나 하단, 메뉴와 같은 반복적으로 사용되는 부분들에 대한 정보를 한 곳에 모아 둔 프레임워크

- 레이아웃 : 모든 페이지에 표시될 HTML과 반복 적으로 사용될 부분의 위치를 정의한 JSP 파일.
- Tiles Definition : 레이아웃에서 반복적으로 사용되는 부분들(HTM, JSP)지정하는 설정. (설정:XML)

![springk](/images/springk/springk03-03.png)
- tiles-api 라이브러리 추가가 필요하다.

![springk](/images/springk/springk03-04.png)
- tiles 설정파일을 bean객체로 등록한다.
- 출력을 위한 viewResolver를 등록한다.
- <property name=”order” value=”*”/>을 사용하여 viewResolver의 우선순위를 정한다.

![springk](/images/springk/springk03-05.png)
- 공통된 부분을 base_layout을 지정한다.
- 각 페이지의 본문이 될부분을 지정한다.
(base_layout을 extends한 definition으로 설정 )
<br><br>

### 파일 업/다운로드
#### 업로드
![springk](/images/springk/springk03-06.png)
- commos-io / commons-fileupload 라이브러리가 필요하다.

![springk](/images/springk/springk03-07.png)
![springk](/images/springk/springk03-08.png)
- uploadDir : 업로드 될 폴더 경로
- MultipartFile 객체 생성 후, 업로드 된 파일을 받는다.
- 받은 파일을 transferTo 메소드를 사용하여 업로드 경로에 넣는다.
- 파일이름을 추출하여, DB에 insert 시, 같이 기입한다.

#### 다운로드
![springk](/images/springk/springk03-09.png)
![springk](/images/springk/springk03-10.png)
![springk](/images/springk/springk03-11.png)
- 다운로드를 위한 뷰를 작성 후 bean객체로 등록한다.
- 해당view는 java파일임으로 viewResolver를 등록한다.
- multipartResolver bean객체를 등록한다.

![springk](/images/springk/springk03-12.png)
- 파일이름과 경로를 받아서 새로운 파일 객체를 생성한다.
- 생성된 객체를 담아서 등록한 bean객체로 보낸다.
<br><br>

### JSON
![springk](/images/springk/springk03-13.png)
- Json객체를 컨트롤러로 만들어주기 위해서는 해당 라이브러리가 필요하다.

![springk](/images/springk/springk03-14.png)
- @RestController 어노테이션을 통해서 JSON객체로 변환하는 컨트롤러를 생성한다.
- RequestMapping 후, JSON객체로 변환할 객체 or List를 리턴한다.
매핑된 json 객체를 사용하기 위해서는 view에서 다음과같이 스크립트를 설정한다.

![springk](/images/springk/springk03-15.png)
- 파라미터값으로 url주소와, data(넘겨줄값 없으면 생략), 콜백함수 를 가진다.
- $.ajax 와 유사하게 사용된다.(비동기식)
<br><br>

### Spring MVC AOP
이전에 포스팅하였던 AOP를 MVC패턴에서 적용하는법에 대해 알아보자.

#### 예제
![springk](/images/springk/springk03-16.png)
spring-aspects 라이브러리가 필요하다.

![springk](/images/springk/springk03-17.png)
![springk](/images/springk/springk03-18.png)
- @Aspect를 사용한 공통 관심사항 클래스를 생성한다.
- ProceedingJoinPoint객체를 파라미터로 가지는 @Around 메소드를 생성한다.

>Around : 공통관심사항 가운데 핵심관심사항이 나타나게 하는 어노테이션

- object 배열에 핵심관심사항을 담는다.(여러개일수 있음으로 배열)
- request객체로 형변환하여 담는다.
- “name”이름의 session값을 얻는다.
- 리턴할 뷰의값을 fail상태로 초기화한다.
- 값이 null일경우, 에러발생하여, catch문으로 전달되어 fail상태 그대로 리턴된다.
- 값이 null이 아닐경우 핵심관심사항이 호출되어 success값을 리턴하게 된다.

![springk](/images/springk/springk03-19.png)
- namespaces에 aop를 추가한다.
- 생성한 aspect class를 bean객체로 등록한다.

![springk](/images/springk/springk03-20.png)
<br><br>

### Spring Transaction
- 트랜잭션은 성공적으로 처리되거나 또는 하나라도 실패하면 완전히 실패 처리를 해야 하는 경우에 사용
- 스프링에서 트랜잭션 관리 기능을 지원하고, 간단한 설정으로 트랜 잭션 처리 가능함
- 어노테이션 기반 트랜잭션을 대부분 사용한다.
어노테이션을 활용하여 트랜잭션 처리하는 방법에 대해 알아보자.

#### 예제
주문 데이터 생성과 동시에 재고 데이터가 주문한 수량만큼 감소되며, 재고가 없을 시, 실패되는 트랜잭션처리 예제이다.

![springk](/images/springk/springk03-21.png)
- 어노테이션을 활용한 트랜잭션처리를 위해서는 spring-tx 라이브러리가 필요하다.

![springk](/images/springk/springk03-22.png)
- 트랜잭션 매니저 객체를 생성한다.
- property 값으로 dataSource객체를 갖는다.
- <tx:annotation-driven transaction-mansger=”*”>을 통해 등록한 트랜잭션매니저 객체를 등록한다.

![springk](/images/springk/springk03-23.png)
- @Transactional 태그를 사용하여 메소드를 생성한다.
(propagation = Propagation.REQUIRED → 트랜잭션이 없으면 새로운 트랜잭션을 생성해라.)
- rollbackFor= {Exception.class} → 예외발생시, rollback
- 주문등록 후에 남은 재고량보다 주문량이 많을 경우 예외를 발생시킨다.

![springk](/images/springk/springk03-24.png)
- service에서 예외발생 시, 에러를 출력하고 다시 주문 창으로 연결된다.
- 예외발생이 없을 시, 성공 화면으로 view 이동
<br><br>