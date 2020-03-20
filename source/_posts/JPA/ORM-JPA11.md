---
title: ORM11 - 객체지향 쿼리언어2 중급문법(경로표현식, 페치조인, 다형성쿼리(상속), NamedQuery, 벌크연산, @BatchSize)
date: 2020-02-20 23:50:25
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

## 객체지향 쿼리언어2 - 중급 문법

### JPQL - 경로 표현식
<!-- more -->
- 점(.)을 찍어 객체 그래프를 탐색하는 것

  ![ORM11-1](/images/jpa/ORM-JPA/ORM11-1.png)

  - 상태 필드(state field) : 단순히 값을 저장하기 위한 필드
  - 연관 필드(association field) : 연관관계를 위한 필드
    - 단일 값 연관 필드 : 대상이 엔티티
      - `@ManyToOne`
      - `@OneToOne`
    - 컬렉션 값 연관 필드 : 대상이 컬렉션
      - `@OneToMany`
      - `@ManyToMany`
#### 경로 표현식 특징

- 상태 필드 : 경로 탐색의 끝, 더이상 탐색이 불가능

- 단일 값 연관 경로 : 묵시적 내부 조인(inner join) 발생, 탐색이 더 가능하다.

  ![ORM11-2](/images/jpa/ORM-JPA/ORM11-2.png)

  - **실무에서는 묵시적인 내부조인이 발생하지 않도록 쿼리를 짜야한다...**
    - 튜닝이 어려움.
    - SQL과 비슷하게 JPQL을 짜야한다.

- 컬렉션 값 연관 경로 : 묵시적 내부 조인 발생, **더이상 탐색이 불가능**

  - **FROM 절에서 명시적인 조인을 통해 별칭을 얻으면 별칭을 통해 탐색이 가능하다.**

    ![ORM11-3](/images/jpa/ORM-JPA/ORM11-3.png)

#### 명시적 조인, 묵시적 조인

- 명시적 조인 : join 키워드를 직접 사용

  ```sql
  SELECT m FROM Member m join m.team t
  ```

- 묵시적 조인 : 경로 표현식에 의해 묵시적으로 SQL 조인 발생(내부 조인만 가능)

  ```sql
  SELECT m.team FROM Member m
  ```

#### 경로 표현식 예제

- select o.member.team  from Order o -> 성공
- select t.members from Team -> 성공
- select t.members.username from Team t -> 실패
  - 컬렉션 값 연관경로는 추가 탐색 불가능
- select m.username from Team t join t.members m -> 성공
  - FROM절에서 명시적 조인사용

#### 경로 탐색을 위한 묵시적 조인 주의사항

- 항상 내부 조인
- 컬렉션은 경로 탐색의 끝, 명시적 조인을 통해 별칭을 얻어야함.
- 경로 탐색은 주로 SELECT, WHERE 절에서 사용하지만 묵시적 조인으로 인해 SQL의 FROM(JOIN)절에 영향을 준다.

**묵시적 조인은 사용하지 않고, 명시적 조인을 사용하는것을 권장한다.**

<br><br>

### JPQL - 페치조인

- SQL 조인 종류가 아니다.
- JPQL에서 성능 최적화를 위해 제공하는 기능
- **연관된 엔티티나 컬렉션을 SQL 한 번에 함께 조회하는 기능**
- join fetch 명령어 사용
- 페치 조인 ::= [ LEFT (OUTER) | INNER ] JOIN FETCH 조인경로

#### 엔티티 페치 조인

- 회원을 조회하면서 연관된 팀도 함께 조회(SQL 한 번에)

- 예시

  - JPQL

    ```sql
    SELECT m FROM Member m join fetch m.team
    ```

  - SQL

    ```sql
    SELECT m.*, T.* FROM Member m 
    INNER JOIN TEAM t
    ON m.team_id = t.id
    ```

#### 예시

![ORM11-4](/images/jpa/ORM-JPA/ORM11-4.png)

- join

  ![ORM11-5](/images/jpa/ORM-JPA/ORM11-5.png)

  - N+1 문제가 발생한다.

- **join fetch**

  ![ORM11-6](/images/jpa/ORM-JPA/ORM11-6.png)

  - 한 번에 쿼리를 날린다.(N+1문제가 발생하지 않는다.)

#### 컬렉션 패치 조인

![ORM11-8](/images/jpa/ORM-JPA/ORM11-8.png)

- 데이터 중복 발생

  - 왜?

    ![ORM11-7](/images/jpa/ORM-JPA/ORM11-7.png)

    - 조회 ROW가 두개 나오기 때문에
      같은 주소값을 가진 결과가 두 줄나온다.

#### 페치 조인과 DISTINCT

- SQL의 DISTINCT는 중복된 결과를 제거하는 명령

- JPQL의 DISTINCT

  - SQL에 DISTINCT를 추가

  - 애플리케이션에서 중복된 엔티티 제거

    - SQL에 DISTINCT 만으로는 데이터가 다르므로 중복제거가 되지 않는다.

      ![ORM11-9](/images/jpa/ORM-JPA/ORM11-9.png)

    - 애플리케이션에서 중복 제거 시도

      ![ORM11-11](/images/jpa/ORM-JPA/ORM11-11.png)

    - 결과

      ![ORM11-10](/images/jpa/ORM-JPA/ORM11-10.png)

#### 페치 조인과 일반 조인의 차이

- 일반 조인 실행 시, 연관된 엔티티를 함께 조회하지 않는다.

  - join은 나가되 select 절에서 포함하지 않음

    ![ORM11-12](/images/jpa/ORM-JPA/ORM11-12.png)

- JPQL은 결과를 반환할 때, 연관관계를 고려하지 않는다.

- 단지 SELECT 절에 지정한 엔티티만 조회한다.

- 페치 조인을 사용할 때만, 연관된 엔티티도 함께 조회(즉시로딩)

- **페치 조인은 객체 그래프를 SQL 한번에 조회하는 개념**

- 대부분의 N+1문제를 페치 조인으로 해결한다.

<br><br>

###  패치 조인의 특징과 한계

- **패치 조인 대상에는 별칭을 줄 수 없다.**

  - 하이버네이트는 가능, 가급적 사용하지 않는것이 좋다.

    - 연관관계가 여러개 일때, join을 하기 위해서만 사용하자.

      ```sql
      # 예시
      select t From Team t
      join fetch t.members m
      join fetch m.something s
      ```

- **둘 이상의 컬렉션은 패치 조인 할 수 없다.**

  - 데이터가 잘 안맞는다.

- **컬렉션을 패치 조인하면 페이징 API(setFirstResult, setMaxResult)를 사용할 수 없다.**

  - 일대일, 다대일 관계들은 페치조인해도 페이징 가능

  - 하이버네이튼 경고 로그를 남기고 메모리에서 페이징(전체를 조회..매우위험)

  - 다대일로 변경하여 페치조인하여 해결하거나,  `@BatchSize(size = 100)` 를 사용하여 레이지 로딩 시, 사이즈만큼 IN 쿼리를 날려 한번에 조회가 가능하다.

    ![ORM11-13](/images/jpa/ORM-JPA/ORM11-13.png)

    - size는 보통 1000이하로 크게잡는다.

    - N + 1을 해결하는 방법중 하나

    - 실무에서는 글로벌 세팅으로 사용한다.

      ![ORM11-14](/images/jpa/ORM-JPA/ORM11-14.png)

      - @BatchSize를 안써도된다.

- 연관된 엔티티들을 SQL한번으로 조회(성능 최적화)

- 글로벌 로딩 전략보다 우선 순위로 적용된다.

  - `@OneToMany(fetch = FetchType.LAZY)` 보다 우선..
  
    (실무에서 글로벌 로딩전략은 모두 지연 로딩으로 적용)

- 최적화가 필요한곳은 페치 조인 적용

  - JPA 성능문제의 70~80% 는 페치조인으로 해결할 수 있다.

#### 페치조인 - 정리

- **페치 조인은 객체그래프를 유지할 때 사용하면 효과적**
- 여러 테이블을 조인해서 엔티티가 가진 모양이 아닌 전혀 다른 결과를 내야하면, 패치조인 보다는 일반 조인을 사용하고 필요한 데이터들만 조회해서 DTO로 반환하는 것이 효과적
  - 예를들면 통계 데이터

<br><br>

### JPQL - 다형성 쿼리 

- 아래 예제 공통 

  ![ORM11-15](/images/jpa/ORM-JPA/ORM11-15.png)

#### TYPE

- 조회 대상을 특정 자식으로 한정

  - 예) item 중 Book과 Movie를 조회하라

    - JPQL

      ```sql
      select i from Item i
      where type(i) IN (Book, Movie)
      ```

    - SQL

      ```sql
      select i.* from Item i
      where i.DTYPE IN ('B', 'M')
      ```

#### TREAT(JPA2.1)

- 자바의 타입 캐스팅과 유사

- 상속 구조에서 부모 타입을 특정 자식 타입으로 다룰 때 사용

- FROM, WHERE, SELECT (하이버네이트 지원) 사용

- 예) 부모 : Item , 자식 : Book

  - JPQL

    ```sql
    select i from Item i
    where treat(i as Book).author = 'kim'
    ```

  - SQL

    ```sql
    select i.* from Item i
    where i.DTYPE = 'B' and i.author = 'kim'
    ```

    - 상속관계 매핑 전략에 따라서 쿼리는 다르게 나간다.

<br><br>

### JPQL - 엔티티 직접 사용

- JPQL에서 엔티티를 직접 사용하면 SQL에서 해당 엔티티의 기본 키값을 사용한다.

  - JPQL

    ```sql
    select count(m.id) from Member m; 
    select count(m) from Member m;
    ```

    - 전자는 엔티티 아이디를 사용, 후자는 엔티티를 직접사용

  - SQL

    ```sql
    select count(m.id) as cnt from Member m;
    ```

    - 둘 다 같은 SQL이 실행됨.

- 엔티티를 파라미터로 전달

  ![ORM11-16](/images/jpa/ORM-JPA/ORM11-16.png)

  - 자동으로 식별자를 전달한다.

- 엔티티 직접 사용 - 외래 키 값

  ![ORM11-17](/images/jpa/ORM-JPA/ORM11-17.png)

  - 마찬 가지로 식별자를 전달

<br><br>

### JPQL - Named 쿼리

- 미리 정의해서 이름을 부여해두고 사용하는 JPQL

  ![ORM11-18](/images/jpa/ORM-JPA/ORM11-18.png)

  - 사용

    ![ORM11-19](/images/jpa/ORM-JPA/ORM11-19.png)

- **정적 쿼리만 가능하다.**

- 어노테이션, XML에 정의

- 애플리케이션 로딩 시점에 초기화 후, 재사용

- **애플리케이션 로딩 시점에 쿼리를 검증**

  - 배포 전에 알 수 있다.

- **SpringDataJpa에서 인터페이스 위에 @Query로 정의된 쿼리가 NamedQuery로 등록한다.**

  - 어플리케이션 로딩 시점에 파싱을 하여 SQL 문법 오류를 잡아준다.
  - 이름없는 NamedQuery라고 불린다.

#### Named 쿼리 환경에 따른 설정

- XML이 우선권을 가진다.(어노테이션 보다)
  - 애플리케이션 운영 환경에 따라 다른 XML을 배포할 수 있다.

<br><br>

### JPQL - 벌크연산

- 여러데이터를 수정 삭제하는 작업

  - 재고가 10개 미만인 모든상품의 가격을 10% 상승하려면?

- JPA 변경 감지 기능으로 실행하려면 너무 많은 SQL 실행

  - 전체 리스트 조회
  - 상품엔티티 가격 변경
  - 트랜잭션 커밋 시점에 변경 감지하여 동작
  - 변경된 데이터가 100건이라면 100번의 UPDATE SQL 실행

- 쿼리 한번으로 여러 테이블의 로우를 변경

- `executeUpdate()` 메소드 사용으로 벌크연산이 가능하다.

  ![ORM11-20](/images/jpa/ORM-JPA/ORM11-20.png)

  - result는 적용된 숫자

- UPDATE, DELETE 지원

- INSERT(insert into .. select )는 하이버네이트에서는 지원한다.

#### 벌크 연산 주의

- 영속성 컨텍스트를 무시하고 데이터 베이스에 직접 쿼리

- 데이터가 꼬일 수 있다.

- 해결책

  1. 벌크 연산을 먼저 실행

  2. **먼저 실행하지 못하면 벌크연산 수행 후 영속성 컨텍스트 초기화**

     - 플러쉬는 상관이 없다. 벌크연산도 JPQL이 실행되고 쿼리가 나가기 때문에플러쉬가 된다.

     - 벌크연산 전에 flush가 자동 호출된다.

       ![ORM11-21](/images/jpa/ORM-JPA/ORM11-21.png)

       - `em.flush()` 가 없어도 flush 된다.
       
       - `em.clear()` 는  반드시 필요
       
- Spring Data Jpa 에서는 Modifying Query를 제공한다.

  - 이 또한, 영속성컨텍스트를 자동으로 clear 해주지 않는다.

<br><br>