---
title: 스프링 기반 REST API 개발01 - REST API 및 프로젝트 소개
date: 2019-06-25 10:21:40
tags: RestAPI
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
# 스프링 기반 REST API 개발(inflearn)1 - 백기선
## REST API 및 프로젝트 소개
### REST API
<!-- more -->
참고 발표 영상 : [그런 REST API로 괜찮은가](https://www.youtube.com/watch?v=RP_f5dMoHFc)
참고 소스 코드 : https://github.com/keesun/study/tree/master/rest-api-with-spring

#### API
- Application Programming Interface

#### REST 
- REpresentational State Transfer
- 인터넷 상의 시스템 간의 상호 운용성(interoperability)을 제공하는 방법중 하나
- 시스템 제각각의 독립적인 독립적인 진화를 보장하기 위한 방법
- REST API: REST 아키텍처 스타일을 따르는 API

- REST 아키텍처 스타일
    - Client-Server
    - Stateless
    - Cache
    - **Uniform Interface**
    - Layered System
    - Code-On-Demand (optional)
    - **http만 잘 따라도 잘 지켜지지만 Uniform Interface는 잘 지켜지지 않는다.**
    
- Uniform Interface (영상영상 11분 40초)
    - Identification of resources
    - manipulation of resources through represenations
    - **self-descrive messages**
    - **hypermisa as the engine of appliaction state (HATEOAS)**
    - 강조 된 두 제약조건을 잘 지키지 못한다.

##### 두 문제를 좀 더 자세히 살펴보자. (발표 영상 37분 50초)
- Self-descriptive message
    - 메시지 스스로 메시지에 대한 설명이 가능해야 한다.
    - 서버가 변해서 메시지가 변해도 클라이언트는 그 메시지를 보고 해석이 가능하다.
    - 확장 가능한 커뮤니케이션
- HATEOAS
    - 하이퍼미디어(링크)를 통해 애플리케이션 상태 변화가 가능해야 한다.
    - 링크링크 정보를동적으로 바꿀 수 있다.(Versioning 할 필요 없이!)

- Self-descriptive message 해결 방법
    - 방법 1: 미디어 타입을 정의하고 IANA에 등록하고 그 미디어 타입을 리소스 리턴할 때, Content-Type으로 사용한다.
    - 방법방법 2: profile 링크 헤더를 추가한다. (발표발표 영상영상 41분 50초)
        - 브라우저들이 아직 스팩 지원을 잘 안해
        - 대안으로 HAL의 링크 데이터에 profile 링크 추가
- HATEOAS 해결 방법
    - 방법1: 데이터에 링크 제공
        - 링크를 어떻게 정의할 것인가? HAL
    - 방법2: 링크 헤더나 Location을 제공
    
> **HAL(Hypertext Application Language)?**
HAL은 API의 리소스들 사이에 쉽고 일관적인 하이퍼링크를 제공하는 방식이다. 
API 설계시 HAL을 도입하면 API간에 쉽게 검색이 가능하다. 
따라서 해당 API를 사용하는 다른 개발자들에게 좀 더 나은 개발 경험을 제공한다.

<br>

### "Event" REST API
앞으로 만들 프로젝트에 대한 설명

- 이벤트 등록, 조회 및 수정을 하는 REST API
- GET /api/events
    - 이벤트 목록 조회 REST API (로그인 안 한 상태)
    - 응답에 보여줘야 할 데이터
        - 이벤트 목록
        - 링크
            - self
            - profile: 이벤트 목록 조회 API 문서로 링크
            - get-an-event: 이벤트 하나 조회하는 API 링크
            - next: 다음 페이지 (optional)
            - prev: 이전 페이지 (optional)
    - 문서 : 스프링 REST Docs로 만들 예정
    <br>
    - 이벤트 목록 조회 REST API (로그인 한 상태)
        - 응답에 보여줘야 할 데이터
            - 이벤트 목록
            - 링크 
                - self
                - profile: 이벤트 목록 조회 API 문서로 링크
                - get-an-event: 이벤트 하나 조회하는 API 링크
                - create-new-event: 이벤트를 생성할 수있는 API 링크
                - next: 다음 페이지 (optional)
                - prev: 이전 페이지 (optional)
        - 로그인 한 상태???? (stateless라며..)
            - 아니, 사실은 Bearer 헤더에 유효한 AccessToken이 들어있는 경우!

- POST /api/events
    - 이벤트 생성
- GET /api/events/{id}
    - 이벤트 하나 조회
- PUT /api/events/{id}
    - 이벤트 수정
    
> **Stateless**
Stateless 하다는 것은 서버가 어떠한 Client 의 Status 도 저장하지 않는다는 뜻입니다. 
따라서 Client 는 매 요청마다 자신을 인증할 수 있는 Token 이나 Key 등을 Request 에 포함시켜 전송합니다. 만약 클라이언트의 요청이 Stateful 하다면 서버가 클라이언트의 현재 상태를 저장해야하고, 클라이언트의 상태는 해당 서버에 종속이 됩니다. 
만약 대규모 환경에서 동일한 웹서버를 다수 배치해 로드밸런싱을 하는 경우에 각 서버가 클라이언트의 상태, 세션을 공유할 수 있는 Redis 같은 별도의 시스템이 필요합니다. 
그러나 Stateless 하다면 클라이언트의 요청은 어느 서버가 처리하던지 동일하게 처리할 수 있습니다. 
클라이언트의 상태는 클라이언트의 요청 안에 모두 들어있으니까요.
**참고, 출처** : http://blog.weirdx.io/post/3110
<br>

### Events API 사용 예제
- https://www.inflearn.com/course/spring_rest-api/lecture/16411
    - 해당강좌 구매한 사람만 볼 수 있습니다.
<br><br>

### 스프링 부트 프로젝트 만들기
#### 의존성 추가
- web
- JPA
- HATEOAS
- REST Docs
- H2
- PostgreSQL
- Lombok

#### 스프링 부트 핵심 원리
- 의존성 설정(pom.xml)
- 자동설정(@EnableAutoConfiguration)
- 내장 웹 서버(의존성과 자동 설정의 일부)
- 독립적으로 실행 가능한 JAR(pom.xml의 플러그인)
<br><br>

### Event 생성 API 구현 - Event 도메인 구현
- 왜 @EqualsAndHasCode에서 of를 사용하는가
    - entity간의 연관관계가 있을 때, stack over flow 가 발생할 수 있다.
        - @data도 마찬가지...
- 왜 @Builder를 사용할 때 @AllArgsConstructor가 필요한가
    - javaBean 스펙도 필요
    - builder는 public이 아닌 default로 생성된다.
- @Data를 쓰지 않는 이유
- 애노테이션 줄일 수 없나
    - lombok 애노테이션은 메타 애노테이션이 아니기 때문에.. 애노테이션 조합이 안된다.

### Event 생성 API - 비지니스 로직 
- 다음의 입력 값을 받는다.
    - name
    - description
    - beginEnrollmentDateTime
    - closeEnrollmentDateTime
    - beginEventDateTime
    - endEventDateTime
    - location (optional) 이게 없으면 온라인 모임
    - basePrice (optional)
    - maxPrice (optional)
    - limitOfEnrollment
    
- basePrice와 maxPrice 경우의 수와 각각 로직
    ![restapi](/images/restapi/restapi01-1.png)

- 결과값
    - id
    - name
    - ...
    - eventStatus: DRAFT, PUBLISHED, ENROLLMENT_STARTED, ...
    - offline
    - free
    - _links 
        - profile (for the self-descriptive message)
        - self
        - publish
        - ...
<br><br>



