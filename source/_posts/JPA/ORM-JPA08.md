---
title: ORM08 - 프록시와 연관관계 관리(프록시, fetchType, cascade, orphanRemoval, 고아객체)
date: 2019-12-08 15:35:38
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

## 프록시와 연관관계 관리

### 프록시

#### 기초
<!-- more -->
- `em.find()` vs `em.getReference()`

- `em.find()` : 데이터베이스를 통해서 **실제 엔티티** 객체 조회

  ![ORM08-1](/images/jpa/ORM-JPA/ORM08-1.png)

  - query를 날린다.

- `em.getReference()` : 데이터베이스 조회를 미루는 **가짜(프록시) 엔티티 객체** 조회

  ![ORM08-2](/images/jpa/ORM-JPA/ORM08-2.png)

  - query를 날리지 않는다.

  - 직접 사용할 때 query를 날린다.

    ![ORM08-3](/images/jpa/ORM-JPA/ORM08-3.png)

  - hibernate가 만든 **가짜 클래스를 호출**한다.(**Proxy객체**)

    ![ORM08-4](/images/jpa/ORM-JPA/ORM08-4.png)

#### 프록시의 특징

- **실제 클래스르 상속** 받아서 만든다.

- 실제 클래스와 겉모양은 같다.

  - 껍데기는 같고 ID값만 가지고 있다.

- 사용하는 입장에서는 진짜 객체인지 프록시 객체인지 구분하지 않고 사용하면 된다.(이론상)

- **프록시 객체는 실제 객체의 참조(target)을 보관한다**.

  - **처음에는 target = null인 상태이다.(DB에서 조회한적이 없기 때문에...)**

- 프록시 객체를 호출하면, 프록시 객체는 실제 객체의 메소드 호출

  ![ORM08-5](/images/jpa/ORM-JPA/ORM08-5.png)

#### 프록시 객체의 초기화

```java
Member member = em.getReference(Member.class, 1L);
member.getUsername();
```

![ORM08-6](/images/jpa/ORM-JPA/ORM08-6.png)

1. 실제 사용 요청(`member.getName()`)

2. 영속성 컨텍스트로 초기화 요청

3. DB 조회

4. 실제 엔티티 생성하고 target에 연결해준다.

5. `target.getName()` 호출

#### 프록시의 특징

- 프록시 객체는 **처음 사용할 때 한번만 초기화**

- 프록시 객체를 초기화 할 때, **프록시 객체가 실제 엔티티로 바뀌는것은 아니다.**

  - 초기화되면 프록시 객체를 통해 실제 엔티티 접근 가능

- 프록시 객체는 원본 엔티티를 상속받음

  ![ORM08-7](/images/jpa/ORM-JPA/ORM08-7.png)

  - 타입 체크 시 주의가 필요( == 대신 **instanseof 사용**)

- **영속성 컨텍스트에 찾는 엔티티가 있으면 `em.getReference()`를 호출해도 실제 엔티티 반환**

  ![ORM08-8](/images/jpa/ORM-JPA/ORM08-8.png)

  - **JPA는 같은 트랜잭션안에서 같은 데이터에 대하여 인스턴스 비교(==)를 보장한다.**
    (type이 다르면 ==비교는 무조건 false)

- 프록시로 한번 조회가 되면 `em.find()`로 조회해도 프록시 클래스로 조회가 된다.

- 영속성 컨텍스트의 도움을 받을 수 없는 준영속 상태일 때, 프록시를 초기화하면 문제발생

  ![ORM08-9](/images/jpa/ORM-JPA/ORM08-9.png)

  - org.hibernate.LazyInitializationException 예외 발생

#### 프록시 확인

- 프록시 인스턴스의 초기화 여부 확인

  ```java
  PersistenceUnitUtil.isLoaded(Object entity);
  ```

  ![ORM08-10](/images/jpa/ORM-JPA/ORM08-10.png)

- 프록시 클래스 확인 방법

  ```java
  entity.getClass().getName();
  ```

- 프록시 강제 초기화

  ```java
  org.hibernate.Hibernate.Initialize(entity);
  ```

  ![ORM08-11](/images/jpa/ORM-JPA/ORM08-11.png)

  - JPA표준에는 강제 초기화가 없다.
  - member.getName(), size() 등으로 강제호출하여 초기화시킨다.

<br><br>

### 즉시로딩과 지연로딩

#### 지연로딩(LAZY)

**Member를 조회할 때 Team도 조회해야할까?**

![ORM08-12](/images/jpa/ORM-JPA/ORM08-12.png)

![ORM08-13](/images/jpa/ORM-JPA/ORM08-13.png)

- 해당 연관관계에 해당하는 엔티티를 프록시객체로 가져온다.
  - 실제로 사용하는 시점에 초기화(DB조회)

#### 즉시로딩(EAGER)

**Meber와 Team을 자주 함께 사용한다면?**

![ORM08-14](/images/jpa/ORM-JPA/ORM08-14.png)

- 해당 연관관계에 해당하는 엔티티를 실제 엔티티 객체로 초기화하여 가져온다.
- JPA구현체는 가능하면 조인을 사용해서 SQL 한번에 함께 조회

#### 프록시와 즉시로딩 주의

- **가급적 지연 로딩만 사용(실무에서는 특히...)**
- 즉시 로딩을 적용하면 예상하지 못한 SQL이 발생
- **즉시 로딩은 JPQL에서 N+1문제를 발생시킨다.**
  - 일단 주조건 JPQL 쿼리를 SQL로 번역하여 날린다.
  - 그다음에 fetchType등의 조건을 확인
  - 문제를 해결하려면...(일단 지연로딩으로 설정)
    - **fetchJoin**
    - @EntityGraph
    - 배치사이즈 (1+1으로 쿼리가 나온다.)
- **@ManyToOne, @OneToOne은 기본이 즉시로딩(EAGER) -> LAZY로 변경하는것을 추천**
- @OneToMany, @ManyToMany는 기본이 지연로딩

#### 지연로딩 활용

- 이론적으론 자주 함께 사용 -> 즉시로딩
- 가끔 사용 -> 지연로딩
- **실제로는 모든 연관관계에서 지연로딩으로 설정하자.**
  - 즉시 로딩은 상상하지 못한 query가 발생한다.
- JPQL의 fetchJoin 또는 EntityGraph기능을 사용하자.

<br><br>

### 영속성 전이: CASCADE

![ORM08-15](/images/jpa/ORM-JPA/ORM08-15.png)

- 특정 엔티티를 영속상태로 만들 때, **연관된 엔티티도 함께 영속상태로 만들고 싶을 때**
  - 예시> 부모 엔티티를 저장할 때, 자식 엔티티도 함께 저장.
- CascadeType.ALL or PERSIST를 주로 사용
  - 저장만 필요한 경우 PERSIST
  - 아닌경우 ALL(삭제등..)

#### 주의사항

- 영속성 전이는 연관관계를 매핑하는 것과 아무 관련 없다.
- 엔티티를 영속화할 때, 연관된 엔티티도 함께 영속화하는 편리함을 제공할 뿐
- **라이프 사이클이 유사할 때, 사용하자.**
- **단일 소유자 일 때, 사용하자.**

#### CASCADE의 종류

- **ALL : 모두적용**
- **PERSIST : 영속**
- **REMOVE : 삭제**
- MERGE : 병합
- REFRESH : REFRESH
- DETACH : DETACH

<br><br>

### 고아객체

- 부모 엔티티와 연관관계가 끊어진 자식 엔티티

- `orphanRemoval = true` : 고아객체를 자동으로 삭제

  - 단일 소유자(참조가 1개)일 때, 사용하자.
  - 또한 부모가 삭제되면 고아객체가 되어 삭제된다.(CascadeType.REMOVE 처럼 동작한다.)

#### 고아객체 자동삭제 주의

- **단일 소유자 일 때만 사용하자.**
  - 특정 엔티티가 개인소유
  - 참조하는 곳이 하나

#### 영속성 전이 + 고아객체, 생명주기

- 스스로 생명주기를 관리하는 엔티티는 `em.persist()` , `em.remove()` 로 제거

- `CascadeType.ALL + orphanRemoval=true` 

  - 두 옵션 모두를 활성 화 시, 부모 엔티티로를 통해서 자식의 생명주기를 관리할 수 있다.
  
  - `orphanRemoval = true` 미설정 시, collection 객체 삭제에 대하여 작동하지 않는다.
  
- DDD(DomainDrivenDesign) 도메인 주도 설계의 AggregateRoot개념을 구현할 때, 유용하다.

  - Aggregate하나당 Repository하나인 것을 추천한다.

<br><br>