---
title: ORM03 - 영속성 관리(내부 동작 방식)(영속성 컨텍스트(Dirty checking, 스냅샷..), 플러시, 준영속 상태)
date: 2019-10-08 16:53:21
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

## 영속성 관리 - 내부 동작 방식
<!-- more -->
- JPA에서 가장 중요한 2가지
  - 객체와 관계형 데이터베이스 매핑하기(설계)
  - **영속성 컨텍스트**

### 영속성 컨텍스트

- JPA를 이해하는데 가장 중요한 용어

- '엔티티를 영구 저장하는 환경'이라는 뜻

- EntityManger.persist(entity);

  - 사실 이것은 DB에 저장한다는 뜻이아닌, 엔티티를 영속성컨텍스트에 저장한다는 뜻

- 영속성 컨텍스트는 논리적인 개념

- 눈에 보이지 않는다.

- 엔티티 매니저를 통해서 영속성 컨텍스트에 접근

  ![ORM03-1](/images/jpa/ORM-JPA/ORM03-1.png)

#### 엔티티의 생명주기

![ORM03-2](/images/jpa/ORM-JPA/ORM03-2.png)

- 비영속 (new/transient)
  - 영속성 컨텍스트와 전혀 관계가 없는 새로운 상태
- 영속 (managed)
  - 영속성 컨텍스트에 관리되는 상태
- 준영속 (detached)
  - 영속성 컨텍스트에 저장되었다가 분리된 상태
- 삭제 (removed)
  - 삭제된 상태

코드로 살펴보자.

#### 비영속

![ORM03-3](/images/jpa/ORM-JPA/ORM03-3.png)

```java
Member member = new Member();
member.setId("member1");
member.setUsername("회원1");
```

- 객체를 생성한 상태
- JPA와 전혀 상관없는 상태

#### 영속

![ORM03-4](/images/jpa/ORM-JPA/ORM03-4.png)

```java
Member member = new Member();
member.setId("member1");
member.setUsername("회원1");

EntityManager em = emf.createEntityManger();
em.getTransaction().begin();

em.persist(member);
```

- 영속성 컨텍스트안에 Member객체가 들억간다.

- **아직 DB에 저장되는것이 아니다.**

  ![ORM03-5](/images/jpa/ORM-JPA/ORM03-5.png)

  DB에 저장된다면 BEFORE와 AFTER사이에 쿼리가 찍혔을것이다.

##### 준영속

```java
em.detatch(member);
```

- 영속성 컨텍스트에서 분리

#### 삭제

```java
em.remove(member);
```

- 객체를 DB에서 영구삭제

<br><br>

### 영속성 컨텍스트의 이점

- 1차 캐시
- 동일성(identity) 보장
- 트랜잭션을 지원하는 쓰기 지연(transactional write-behind)
- 변경 감지(Dirty Checking)
- 지연로딩(Lazy Loading)

컨텍스트 이점들에 대해  자세히 알아보자.

#### 엔티티 조회, 1차 캐시

- 엔티티를 영속하면 1차캐시에 저장된다.

  ![ORM03-6](/images/jpa/ORM-JPA/ORM03-6.png)

  - @Id 값을 키값 Entity를 value값인 맵형태의 1차 캐시에 저장

- 1차캐시에서 조회

  ![ORM03-7](/images/jpa/ORM-JPA/ORM03-7.png)

  - 1차 캐시에서 먼저 찾는다.

- 데이터베이스에서 조회

  ![ORM03-8](/images/jpa/ORM-JPA/ORM03-8.png)

  - 1차캐시에 없을 경우 DB를 조회한 후, 결과를 1차캐시에 저장 후 반환한다.

- 테스트

  ![ORM03-9](/images/jpa/ORM-JPA/ORM03-9.png)

  - 1차캐시에 저장된 값을 반환했기 때문에  SELECT 쿼리문이 없다.
  - commit 시에 데이터가 insert 된다.

  ![ORM03-10](/images/jpa/ORM-JPA/ORM03-10.png)

  - 조회 쿼리가 하나만 조회된다.
  - 조회할때 영속성컨텍스트의 1차캐시 안에 저장하고 반환하기 때문에 두번째 조회할때는 1차캐시 안의 값을 반환한다.

#### 영속 엔티티의 동일성 보장

![ORM03-11](/images/jpa/ORM-JPA/ORM03-11.png)

- 자바 컬렉션처럼 비교가 가능

#### 엔티티 등록 시, 트랜잭션을 지원하는 쓰기 지연

- 기본적으로 commit, flush 전에는 DB에 반영하지 않고 쓰지 지연 SQL 저장소에 저장한다.

  ![ORM03-12](/images/jpa/ORM-JPA/ORM03-12.png)

- flush, 또는 commit 시에, 일괄 

  ![ORM03-13](/images/jpa/ORM-JPA/ORM03-13.png)

  - 설정에서 `hibernate.jdbc.batch_size` 를 설정해주면 그 사이즈 만큼 모아서 쿼리를 날린다.(버퍼링 같은 기능)

- 테스트 코드 실행

  ![ORM03-14](/images/jpa/ORM-JPA/ORM03-14.png)

#### 변경감지(Dirty Checking)

![ORM03-16](/images/jpa/ORM-JPA/ORM03-16.png)

- JPA는 commit 시 flush() 메소드를 호출한다.

- flush를 할 때, 엔티티와 스냅샷을 비교한다.

  - 스냅샷은 1차 캐시안에 존재하는것으로, 해당 엔티티가 1차캐시로 들어올때의 값을 저장해둔것.

- 비교 후에 값의 변경이 있으면 쓰기 지연 SQL 저장소에 UPDATE 쿼리를 저장해둔다.

- 그 후에 데이터베이스에 반영

- 테스트 코드 실행

  ![ORM03-15](/images/jpa/ORM-JPA/ORM03-15.png)

<br><br>

### 플러시

- 영속성 컨텍스트의 변경내용을 데이터베이스에 반영

#### 플러시 발생

- 변경감지(Dirty Checking)
- 수정된 엔티티 쓰기 지연 SQL 저장소에 등록
- 쓰기 지연 SQL 저장소의 쿼리를 데이터베이스에 전송(등록, 수정, 삭제, 쿼리)

#### 영속성 컨텍스트를 플러시하는 방법

- `em.flush();` 직접 호출
- 트랜잭션 커밋 : 플러시 자동 호출
- JPQL 쿼리 실행 : 플러시 자동 호출
  - 무조건 플러시를 한번 호출
  - 왜냐하면 JPQL은 query를 날리는데 기존에 데이터들이 1차캐시에만 있고 DB에 반영이 안되어있다면 잘못된 결과를 불러올 수 있으므로, 기본모드가 query를 날리기전에 플러시를 한번 해주는것이다.

#### 플러시 모드 옵션

```
em.setFlushMode(FlushModeType.COMMIT);
```

- FlushModeType.AUTO
  - 커밋이나 쿼리를 실행할 때 플러시 (기본값)
- FlushModeType.COMMIT
  - 커밋할 때만 플러시
  - 거의 사용하지 않는다.
  - 사용하는 경우 :  앞에서 insert한 데이터와 상관없는 테이블을 조회하는 쿼리를 날리는 경우 플러시를 하는 의미가 없기 때문에 사용할 수 도 있으나 ... 거의 사용하지 않는다.

#### 주의사항

- 쓰기 지연 SQL저장소에 있는 쿼리들이 데이터베이스에 반영되는것.
- 영속성 컨텍스트를 비우지 않음(**영속성 컨텍스트에 있는 1차캐시는 지워지지는 않는다.**)
- **트랜잭션이라는 작업 단위가 중요**
  - 커밋 직전에만 동기화 하면 된다.
  - JPA는 기본적으로 데이터동기화등 동시성 관련내용은 트랜잭션에 위임한다.

<br><br>

### 준영속 상태

- 영속 -> 준영속
- 영속 상태의 엔티티가 영속성 컨텍스트에서 분리(detached)
- 영속성 컨텍스트가 제공하는 기능을 사용 못함

#### 준영속 상태로 만드는 방법

- `em.detach(entity);`
  - 특정 엔티티만 준영속 상태로 전환
- `em.clear();`
  - 영속성 컨텍스트를 완전히 초기화
- `em.close();`
  - 영속성 컨텍스트를 종료

#### 예시

![ORM03-17](/images/jpa/ORM-JPA/ORM03-17.png)

- 상태값이 변경 됬지만, update 쿼리문이 날라가지 않는다.

<br><br>

