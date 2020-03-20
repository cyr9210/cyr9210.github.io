---
title: 스프링 핵심기술05 - 스프링 AOP
date: 2019-03-26 16:23:40
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
# 스프링 프레임워크 핵심기술05(inflearn) - 백기선 
## Springframework
<!-- more -->
### AOP 개념
- Aspect-oriendted Programming (AOP)은 OOP를 보완하는 수단으로, 흩어진 Aspect를 모듈화 할 수 있는 프로그래밍 기법.

![springcore](/images/springc/springcore05-01.png)

#### 주요개념
- Aspect : 분류된 모듈
- Target : 대상 
ex\) class A, B, C
- Advice : 해야할 일
- Pointcut : 어디에 적용해야 하는지 정보를 가지고 있다.
- Join Point : 메소드 실행 시점 (**끼어들 수 있는 시점**)

#### [AOP 구현체](https://en.wikipedia.org/wiki/Aspect-oriented_programming)
- ↑ implementation으로 검색 시, 언어별 구현체들을 확인할 수 있다.
- 자바
    - AspectJ
    - 스프링 AOP
    
#### AOP 적용방법
- 컴파일
    - java파일을 class파일로 만들때 조작된 바이트 파일을 생성
- 로드타임
    - AspectJ에서 적용
    - 다양한 join point 제공
    - 로트 타임 위빙 : 로딩할 때, 끼워넣는다.
- 런타임
    - SpringAOP에서 적용
    - 빈을 만들 때, 즉 런타임시에 프록시 빈을 만들어서 구현 (뒤에서 다시 설명 할 예정)
    - 별도의 설정이 필요없고, 문법이 쉽다.
<br><br>

### 프록시 기반 AOP
#### 스프링 AOP 특징
- 프록시 기반의 AOP 구현체
- 스프링 빈에만 AOP를 적용할 수 있다.
- 모든 AOP 기능을 제공하는 것이 목적이 아니라, 스프링 IoC와 연동하여 엔터프라이즈 애플리케이션에서 가장 흔한 문제에 대한 해결책을 제공하는 것이 목적.

#### 프록시 패턴
![springcore](/images/springc/springcore05-02.png)
- 기존 코드를 건드리지 않고 
    - 접근제어
    - 부가기능

#### 예제
- 프록시 미적용
![springcore](/images/springc/springcore05-04.png)

- 프록시 적용
![springcore](/images/springc/springcore05-05.png)

#### 문제점
- 매번 프록시 클래스를 작성해야 하는가?
- 여러 클래스 여러 메소드에 적용하려면?
- 객체들 관계도 복잡하고...

**이러한 문제점 때문에 스프링 AOP가 등장하였다.**
- 스프링 IoC 컨테이너가 제공하는 기반 시설과 Dynamic 프록시를 사용하여 여러 복잡한 문제 해결.
- 동적 프록시: 동적으로 프록시 객체 생성하는 방법
    - 자바가 제공하는 방법은 인터페이스 기반 프록시 생성.
    - CGlib은 클래스 기반 프록시도 지원.
- 스프링 IoC: 기존 빈을 대체하는 동적 프록시 빈을 만들어 등록 시켜준다.
    - 클라이언트 코드 변경 없음.
    - [AbstractAutoProxyCreator](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/aop/framework/autoproxy/AbstractAutoProxyCreator.html) implements [BeanPostProcessor](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/beans/factory/config/BeanPostProcessor.html)
<br>

###  @AOP
애노테이션 기반의 스프링 AOP

- 의존성 추가
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```
- Aspect 정의
    ![springcore](/images/springc/springcore05-10.png)   
    - @Aspect
    - 빈으로 등록해야 하니까 (컴포넌트 스캔을 사용한다면) @Component도 추가.

#### 포인트컷 정의
- @Pointcut(표현식)
- 주요 표현식
    - execution
    ![springcore](/images/springc/springcore05-06.png)
    
    - @annotation
    ![springcore](/images/springc/springcore05-08.png)
    
    - bean
    ![springcore](/images/springc/springcore05-09.png)
- 포인트컷 조합
    - &&, ||, !
<br><br>
