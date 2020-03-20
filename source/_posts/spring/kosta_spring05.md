---
title: Spring MVC TEST (KOSTA)
date: 2019-01-30 06:45:43
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
#  KOSTA DAY59
## Springframework
<!-- more -->
### Mapper Test
CRUD를 위한 Mapping이 잘되었는지 확인하기위한 Test방법에 대해 알아보자.

- 테스트를 위한 DB의 테이블과 data를 생성한다.
(table명 = tbl_board, seq_board)

![springk](/images/springk/springk05-01.png)

![springk](/images/springk/springk05-02.png)

![springk](/images/springk/springk05-03.png)

![springk](/images/springk/springk05-04.png)

![springk](/images/springk/springk05-05.png)
- BoardVO클래스 생성
- BoardMapper 인터페이스 생성 및 메소드 생성
- BoardMapper.xml 생성 및 select 쿼리문 작성
- BoardMapperTests 테스트 클래스 생성 후 Junit Test처리
전체 data 로그로 출력

#### Create Test
- 인서트문의 경우 두가지 처리방법이 있다.
    - 인서트 후에, pk정보가 필요없는 경우 
    - 처리방법/pk정보가 필요한 경우

##### 먼저 pk정보가 필요없는 insert
![springk](/images/springk/springk05-06.png)

![springk](/images/springk/springk05-07.png)

![springk](/images/springk/springk05-08.png)
- Query문 작성
- Test 실행
- bno값이 null로 결과가 나온다.

##### pk정보가 필요한 경우 insert
![springk](/images/springk/springk05-09.png)

![springk](/images/springk/springk05-10.png)
- insert문 안 selectKey태그를 작성하여, pk값을 구한다.(bno)
- 위와 같은 방식으로 test를 실행했을때, 결과값에 bno값이 출력된다.

#### Read Test
pk정보를 이용하여 테이터 출력을 해보자.

![springk](/images/springk/springk05-11.png)

![springk](/images/springk/springk05-12.png)
- Long값을 파라미터값으로 쓸 경우, L을 뒤에 붙여준다.
<br><br>

### Service Test
service 객체를 만들어서 Test를 실행해보자.

![springk](/images/springk/springk05-13.png)

![springk](/images/springk/springk05-14.png)

![springk](/images/springk/springk05-15.png)

![springk](/images/springk/springk05-16.png)
- service 패키지를 생성
root-context.xml에 service 패키지를 scan할 수 있도록 설정한다.
- service 인터페이스 생성
- servie 인터페이스를 implements 하는 InsertBoardService 생성
- InsertBoardService에 오버라이딩 메소드를 작성한다.
내용 → Insert(selectKey사용)
- 테스트 실행
<br><br>

### Controller Test
Controller 객체를 만들어서 Test 해보자.

Controller는 기본적으로 서버로 부터 받는 주소값을 가지고 결과를 요청하는 공간이기 때문에 서버를 실행하지 않고 Test하기 위해서는 Test 클래스 안에서 설정이 필요하다.

![springk](/images/springk/springk05-17.png)

![springk](/images/springk/springk05-18.png)

![springk](/images/springk/springk05-19.png)
- BoardController를 생성한다.
- ControllerTest 생성 및 설정
    - WebAppConfiguration을 사용하여 WebAppicationContext를 이용할 수 있도록 한다.
    - ContextConfiguration으로 설정 파일을 로드한다.
    - WebAppicationContext를 Setter로 DI한다.
    - MockMvc를 선언한다. 이유는 WAS 구동을 하지 않고도 테스트를 할 수 있기 때문에
    - Before 어노테이션은 테스트 메소드 실행 전 실행하게 해준다.
- 테스트 코드를 상기 예제와같이 작성 후 실행하면 톰캣 서버 실행을 하지 않아도 결과가 log로 출력된다.
- post방식/ 파라미터값이 있는 경우 예제를 작성해보았다.
<br><br>
