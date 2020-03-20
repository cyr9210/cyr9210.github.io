---
title: Spring, Maven, DI, 객체주입, AOP (KOSTA)
date: 2019-01-24 04:29:52
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
#  KOSTA DAY55
## Springframework
<!-- more -->
### Spring
- 오픈소스 프레임 워크
- 엔터프라이즈 어플리케이션 개발의 복잡성을 줄여주기 위한 목적
- EJB 사용으로 수행되었던 모든 기능을 일반 POJO(Plain Old Java Object) 를 사용해서 가능하게 함.

#### 장점
- 경량 컨테이너
- DI ( Dependency Injection) 지원

> **DI**
프로그래밍에서 구성요소간의 의존 관계가 소스코드 내부가 아닌 외부의 설정파일 등을 통해 정의되게 하는 디자인 패턴 중의 하나이다.

- AOP(AspectOrientedProgramming)지원

> **AOP**
관점 지향 프로그래밍으로 “기능을 핵심 비즈니스 기능과 공통 기능으로 ‘구분’하고, 공통 기능을 개발자의 코드 밖에서 필요한 시점에 적용하는 프로그래밍 방법

- POJO(PlainOldJavaObject)지원

> **POJO**
기존에 만들어진 자바 객체

- JDBC를 위한 다양한 API 지원
- Transaction 처리를 위한 일관된 방법제공
- 다양한 API와의 연동 지원
<br><br>

### Maven
- 자바용 프로젝트 관리 툴
- 협업 작업시 , 라이브러리 버전이 다르거나, 유무 때문에 에러발생을 줄일 수 있다.

#### 사용법
![springk](/images/springk/springk01-01.png)
- poem.xml 생성

![springk](/images/springk/springk01-02.png)
![springk](/images/springk/springk01-03.png)
- Maven Repository 사이트에서 필요한 라이브러리를 검색하여 값을 poem.xml에 넣어주어야 한다.
- 메이븐 문제 발생시, 빨간색 느낌표 발생
해결방안
툴을 종료 → 유저폴더에서 숨김파일 확인(Mac : cmd + shift + .) → m2 → Repository 삭제 후 툴 재실행
<br><br>

### DI(Dependency Injection)
- 의존성주입
- Spring에서 DI를 지원
    - Spring Container가 DI조립기를 제공
    - - 스프링 설정파일을 통하여 객체간의 의존관계를 설정한다.
    - - Spring Container가 제공하는 api를 이용해 객체를 사용한다.
- 설정 파일은 xml or java 두가지 종류로 설정이 가능하다.

#### Spring Container
![springk](/images/springk/springk01-04.png)
- 객체를 관리하는 컨테이너
- 위의 interface들을 구현한다.

#### Resource 구현 클래스
- Resource interface : 다양한 종류의 자원을 동일한 방식으로 통일하여 표현할 수 있게 한다.
- XmlBeanFactory는 객체 생성시 설정 파일의 위치를 알려줘야한다.
(Resource를 이용)
<br><br>

### 객체 및 DI 설정
크게 3가지 방법으로 나눌 수 있다.

- xml 사용하여 객체들을 각각 주입 및 DI 설정
- xml사용하여 패키지 단위로 객체들을 주입 후, annotation 사용하여 DI설정
- java 클래스를 사용하여 메소드와 annotation을 사용하여 객체주입 및 DI 설정

주입된 객체들은 모두 Spring Container에 담기며, 호출을 위해서는 SpringContainer에서 호출한다.

#### xml 사용하여 객체들을 각각 주입 및 DI 설정
![springk](/images/springk/springk01-05.png)
- service객체를 implements하는 객체를 생성한다.
- Dao를 생성자를 통해 호출
- 해당 클래스 안에서는 new를 통한 Dao객체 생성을 하지않는다.
- xml상에서 연결시킬 예정

![springk](/images/springk/springk01-06.png)
- Spring Bean Configuration File 생성(xml파일)

![springk](/images/springk/springk01-07.png)
![springk](/images/springk/springk01-08.png)
- 각각의 객체들을 bean으로 주입
    - id :주입받을곳에서호출할이름설정(‘/’ 값으로못가짐)
    - class : 주입할 객체의 클래스
- oracleDao라는 객체를 SpringContainer에 담도록하고 bean태그에 명시되있는 클래스에 dao를 사용할 수 있도록 주입한다. (DI설정)
constructor 또는 property를 사용하여 설정한다.
- value or ref 같은 속성을 이용한다. (객체 = ref , 값 = value)

![springk](/images/springk/springk01-09.png)
- Resource 객체를 통해 xml 파일을 받아온 후, BeanFactory를 통해 Bean객체를 받아 Spring Container생성.
- **getBean(“\*”) 의 * 은 DI설정시 id 값과 동일하도록 설정해야 한다.**

#### xml사용하여 패키지 단위로 객체들을 주입 후, annotation 사용하여 DI설정

![springk](/images/springk/springk01-10.png)
- xml 작성 시, Namespaces에서 context를 활성화 시킨다.

![springk](/images/springk/springk01-11.png)
- context: annotation-config 설정한다.(어노테이션이 달린 객체를 담는다.)
- context:component-scan base-package=”*” 
해당 패키지 영역을 지정한다.

- DI를 설정해주는 것은 앞서 1번 방식과 같으나, 직접 constructor 나 property를 통해 지정해주지 않는다.

![springk](/images/springk/springk01-12.png)
- 담고싶은 Bean객체에 Repository 어노테이션을 작성한다.

![springk](/images/springk/springk01-13.png)
- 주입할 부분에 Inject 또는 Autowired 어노테이션을 작성
@ Inject → 선언부 상단
@Autowired → set메소드 상단

- Inject 어노테이션 활용 시, 라이브러리 추가 필요

![springk](/images/springk/springk01-14.png)
- ApplicationContext를 통해 정보를 받아서 Spring Container를 생성
- **getBean(“\*”) 의 * 은 DI설정시 id 값과 동일하도록 설정해야 한다.**

#### java 클래스를 사용하여 메소드와 annotation을 사용하여 객체주입 및 DI 설정
![springk](/images/springk/springk01-15.png)
- 클래스에 Configuration 어노테이션을 작성한다.
- xml파일이 아닌 자바 파일로 생성 해당 객체들에 Bean어노테이션을 작성한다.
- 메소드이름이 xml방식의 id값 역할을 한다.

![springk](/images/springk/springk01-13.png)
- 주입할 부분에 Inject 또는 Autowired 어노테이션을 작성
@ Inject → 선언부 상단
@Autowired → set메소드 상단

- Inject 어노테이션 활용 시, 라이브러리 추가 필요

![springk](/images/springk/springk01-16.png)
- ApplicationContext객체를 통해 Spring Container 생성
- **getBean(“\*”)을 통해 객체를 호출한다. (\*값은 메소드 이름)**
<br><br>

### AOP
![springk](/images/springk/springk01-17.png)
- 핵심 관심사항(core concern)과 공통 관심사항 (cross-cutting concern)
- 기존 OOP 에서는 공통관심사항을 여러 모듈에서 적용하는데 중복된 코드를 양산과 같은 한계가 존재 → 이를 극복하기 위해 AOP 가 등장
- Aspect Oriented Programming은 문제를 해결 하기 위한 핵심 관심사항과 전체에 적용되는 공통 관심사항을 기준으로 프로그래밍함으로써 공통 모듈을 손쉽게 적용할 수 있게 해준다.
- 반복되는 공통관심사항 코드들을 줄일 수 잇다.


#### Spring AOP 용어
- Aspect : 여러 객체에서 공통으로 적용되는 공통 관심사항
(ex:트랜 잭션, 로깅, 보안)
- JoinPoint : Aspect가 적용 될 수 있는 지점(ex:메소드, 필드)
- Pointcut : 공통관심사항이 적용될 Joinpoint 
→ pointcut에 의해 joinpoint가 결정된다.
- Advice : 어느 시점(ex: 메소드 수행 전/후, 예외발생 후 등)에 어 떤 공통 관심기능(Aspect)을 적용할지 정의한 것.
- Weaving : 어떤 Advice를 어떤 Pointcut(핵심사항)에 적용시킬 것 인지에 대한 설정 (Advisor)

>example) 
남자들 오늘 술먹자.
Aspect = 술먹자
joinpoint = 각각 남자 
pointcut = 남자들
advice = 오늘

#### AOP 사용법
- 라이브러리 추가
Maven → Spring aspect 추가

![springk](/images/springk/springk01-18.png)
- 공통관심사항에 대한 메소드 작성후, try catch를 통해 Object 객체를 통해 핵심관심사항 로직을 호출한다.
- 호출 뒤 실행문에 대하여는 finally 부분에 넣어준다.

![springk](/images/springk/springk01-19.png)
- Bean태그를 통해 공통관심사항 객체 지정
- <aop:pointcut expression=”execution(pointcut지정)” id =”포인트컷이름지정">
- <aop : aspect id=”이름지정” ref=”공통관심사항 해당객체 이름">
- <aop:around method=”공통관심사항객체실행메소드이름" pointcut-ref=”포인트컷이름">

![springk](/images/springk/springk01-20.png)
- 두가지 이상의 xml 사용시 스트링 배열에 담은후, ApplicationContext객체를 통해 Spring Container 생성
- 해당메소드 실행시, 공통관심사항객체의 메소드가 실행된다.

결과
![springk](/images/springk/springk01-21.png)
<br>