---
title: 스프링 부트 시작하기01
date: 2019-03-28 13:29:41
tags: SpringBoot
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

![springboot](/images/springboot_logo.png)
# 스프릥 부트 개념과 활용01(inflearn) - 백기선 
## Spring boot

<!-- more -->

백기선 님의 강의 중 세 번째로 수강하게 된 '스프링 부트 개념과 활용'!!
사실 이 강의를 듣고 스프링 부트를 사용하기 위해서 앞선 두 강의를 수강했다고 할 수 있습니다.
스프링 부트에 대해서 기초부터 탄탄히 다져 나갑시다.
해당 강좌는 스프링 부트의 원리, 활용(예제), 운영에 대해서 알려주고 있습니다.😀

***

### 스프링 부트
- 제품수준의 스프링 기반 어플리케이션을 만들 때, 빠르고 쉽게 만들 수 있도록 도와준다.
- 기본적인 설정을 지원한다.(opinionated view)
    - 스프링 플랫폼
    - third-party 즉, 제 3의 라이브러리 설정 지원 (ex)톰캣
- 자바 8버전 이상 사용이 요구된다.

#### 목적
- 모든 스프링 개발에 있어 더 빠르고 폭넓은 사용성을 제공한다.
- 기본 설정을 convention으로 지원한다.
**또한 그 설정을 필요에 따라서 의도에 맞도록 쉽고 빠르게 변경할 수 있다.**
- 비지니스 로직을 구현하느 기능뿐만 아니라, non-functional한 기능도 제공해준다.
- xml설정을 하지 않는다.
- 코드 제너레이션도 하지 않는다.
<br><br>

### 스프링 부트 시작하기

#### 프로젝트 생성
- 프로젝트를 생성한다. (메이븐/인텔리제이 사용)
    ![springboot](/images/springboot/springboot01-1.png)
      
    ![springboot](/images/springboot/springboot01-2.png)
        
    ![springboot](/images/springboot/springboot01-3.png)
    - GroupId는 보통 패키지명
    - 생성위치 설정
    

- spring boot 레퍼런스 문서를 보고 메이븐 파일을 설정한다.
![springboot](/images/springboot/springboot01-4.png)
    - maven간에 프로젝트 계층구조를 만들수 있다.
    부모 프로젝트로 spring-boot-starter-parent로 설정해준다.
    - spring-boot-starter-web을 의존성 추가한다.
    - 빌드 플러그인에 spring-boot-maven을 추가한다.

- 패키지 및 클래스 파일 만든 후, 메인 실행 메소드 생성
![springboot](/images/springboot/springboot01-4.png)
    
- 실행 시, 스프링 부트 내장 톰캣이 실행된다. (기본 포트 : 8080)
![springboot](/images/springboot/springboot01-6.png)

#### 여러가지 스프링 부트 생성방법
- 인텔리제이 Spring Initializr
![springboot](/images/springboot/springboot01-7.png)

- [start.spring.io](https://start.spring.io/) 사용
![springboot](/images/springboot/springboot01-8.png)

#### 스프링 부트 구조
- 이븐 기본 프로젝트 구조와 동일
    - 소스 코드 (src\main\java)
    - 소스 리소스 (src\main\resource)
    - 테스트 코드 (src\test\java)
    - 테스트 리소스 (src\test\resource)
    - resource의 파일을 참조할 시, 'classpath:'로 참조할 수 있다.
    ('classpath:text.txt'일 경우, resource 디렉토리 바로 아래 text.txt가 있다.)

- 메인 애플리케이션 위치
    <image src="/images/springboot/springboot01-9.png" width="40%"/>
    - 기본패키지
    - @SpringBootApplication이 @ComponentScan을 포함하여 해당 패키지 및 하위 패키지들을 스캔한다.
    - 만약 java 하위에 패키지를 생성하지 않고 만들게 되면 모든 패키지를 스캔하기 때문에 좋지 않다.
    - [@Component스캔](https://cyr9210.github.io/2019/03/20/Spring/springframework-core01/) 참고
<br><br>