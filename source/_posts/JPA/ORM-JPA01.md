---
title: ORM01 - JPA 소개
date: 2019-10-04 16:02:45
tags: JPA
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

# 자바 ORM 표준 JPA 프로그래밍 - 기본편

## JPA 소개
<!-- more -->
### SQL 중심적인 개발의 문제점

- 무한반복, 지루한 코드
  - CRUD
    - INSERT INTO...
    - UPDATE...
    - SELECT...
    - DELETE...
  - 필드 추가 시, 각 쿼리마다 수정이 필요하다.
    - 휴먼 에러가 발생하기 쉽다.
  - 관계형 DB사용함에 있어서는 SQL에 의존적인 개발을 피하기 어렵다.
- 패러다임 불일치
  - 객체와 관계형 데이터 베이스의 차이
    1. 상속
    2. 연관관계
    3. 데이터 타입
    4. 데이터 식별방법
  - 처음 실행하는 SQL에 따라 탐색 범위가 결정
  - 엔티티 신뢰 문제
    - Query를 확인하기 전까지는 엔티티를 신뢰할 수 없다.
  - 계층 분할이 어렵다.
  - 객체 비교의 결과가 다르다.
- **객체지향적으로 설계를 하면 할수록 매핑 작업만 늘어난다.**
- **객체를 자바 컬렉션에 저장하듯이 DB에 저장할 수는 없을까? -> JPA**

<br><br>

### JPA 소개
- Java Persistence API
- 자바 진영의 ORM 기술 표준

#### ORM
- Object Relational Mapping 객체 관계 매핑
- 객체는 객체대로 설계
- 관계형 데이터베이스는 관계형 데이터베이스대로 설계
- ORM 프레임워크가 주간에서 매핑
- 대중적인 언어는 대부분 ORM 기술이 존재

#### JPA는 애플리케이션과 JDBC 사이에서 동작

![ORM01-1](/images/jpa/ORM-JPA/ORM01-1.png)
- 개발자 직접 JDBC API를 사용하는것이 아니고 JPA에 명령을 하면 JPA가 JDBC API를 사용해서 SQL을 호출하고 결과를 받아서 동작한다.

#### JPA 동작 - 저장

![ORM01-2](/images/jpa/ORM-JPA/ORM01-2.png)
- 적절한 query를 생성한다. (개발자가 만드는것이 아니고 만들어준다.)

#### JPA 동작 - 조회

![ORM01-3](/images/jpa/ORM-JPA/ORM01-3.png)

#### JPA 역사

![ORM01-4](/images/jpa/ORM-JPA/ORM01-4.png)
- 과거에는 EJB(엔티티 빈)이 있었다. 자바표준으로 사용되었다.
- 그러나, 아마추어적인 기술... (속도 느림, 성능 떨어짐 등..)
- '개빈 킹'이라는 개발자가 ORM 프레임워크 하이버네이트를 개발
  여러 개발자들의 동참하여 오픈소스화가 되었다.
- 자바 진영에서 '개빈킹'을 스카웃 하이버네이트를 기반으로 자바 표준 JPA를 만들었다.
  (거의 유사하다. 다듬고.. 용어정리.. )

#### JPA는 표준 명세

- JPA는 인터페이스의 모음
- 3가지 구현체가 있다.
  ![ORM01-5](/images/jpa/ORM-JPA/ORM01-5.png)
  - 하이버네이트
    - 우리가 사용하는 JPA의 80~90퍼 구현체
  - EclipseLink
  - DataNucleus

#### JPA를 왜 사용해야 하는가?

- SQL 중심적인 개발에서 객체 중심으로 개발
- 생산성
  - 저장 : jpa.persist(member);
  - 조회 : Member member = jpa.find(memberId);
  - 수정 : member.setName("update_name");
  - 삭제 : jpa.remove(member);

- 유지보수
  - 기존 : 필드 변경 시, 모든 SQL 수정
  - JPA 사용 : 필드만 추가하면 된다.

- **패러다임 불일치 해결**
  - JPA와 상속
    ![ORM01-6](/images/jpa/ORM-JPA/ORM01-6.png)

    ![ORM01-7](/images/jpa/ORM-JPA/ORM01-7.png)

  - JPA와 연관관계
    ![ORM01-8](/images/jpa/ORM-JPA/ORM01-8.png)

  - JPA와 객체 그래프 탐색
    ![ORM01-9](/images/jpa/ORM-JPA/ORM01-9.png)

  - JPA와 비교하기
    ![ORM01-10](/images/jpa/ORM-JPA/ORM01-10.png)

- JPA의 성능 최적화 기능
  - 1차 캐시와 동일성(identity) 보장
    - 같은 트랜잭션 안에서는 같은 엔티티를 반환 - 약간의 조회 성능 향상
    - 여러번 조회해도 같은 SQL을 여러번 실행하지 않는다.
    
  - 트랜잭션을 지원하는 쓰기 지연(transactional write-behind)
    ![ORM01-11](/images/jpa/ORM-JPA/ORM01-11.png)
    1. 트랜잭션을 커밋할 때까지 INSERT SQL을 모음
    2. JDBC BATCH SQL 기능을 사용해서 한번에 SQL 전송
       - JDBC BATCH : query를 모아서 한번에 날려준다.
       - 이 과정이 코드가 굉장히 지저분해진다. **JPA는 옵션설정하나로 이 작업을 해준다.**

  - 지연 로딩(Lazy Loading)
    - 지연로딩 : 객체가 실제 사용될 때 로딩
    - 즉시 로딩 : JOIN SQL로 한번에 연관된 객체까지 미리 조회
    - 두 로딩을 로직에 맞게 옵션을 설정하여 효과적으로 SQL을 호출한다.

- 데이터 접근 추상화와 벤더 독립성
- 표준

<br><br>