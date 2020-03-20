---
title: ORM10 - 객체지향 쿼리언어1 기본문법(JPQL, 조인, 페이징, 서브쿼리, 조건식, 사용자 정의 방언)
date: 2020-01-27 22:32:42
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

## 객체지향 쿼리언어1 - 기본문법
<!-- more -->
- JPA는 다양한 쿼리 방법을 지원한다.
  - **JPQL**
    - 표준문법
  - JPA Criteria
    - 자바 코드로 JPQL을 빌드해주는 Generator 클래스의 모음
  - **QueryDSL**
    - 자바 코드로 JPQL을 빌드해주는 Generator 클래스의 모음
  - 네이티브 SQL
    - DB종속적인 문법 사용 시, 사용
  - JDBC API 직접사용(MyBatis, SpringJdbcTemplate 함께 사용)
- 대부분의 문제는 JPQL로 해결할 수 있다.
### 소개

#### JPQL

- JPA를 사용하면 **엔티티 객체를 중심으로 개발**

- 문제는 검색 쿼리

  - 검색을 할 때도 테이블이 아닌 **엔티티 객체를 대상으로 검색**
  - 모둔 DB 데이터를 객체로 변환해서 검색하는 것은 불가능
  - 애플리케이션이 필요한 데이터만 DB에서 불러오려면 결국 **검색 조건이 포함된 SQL이 필요**

- JPA는 SQL을 추상화한 JPQL이라는 **객체지향 쿼리 언어** 제공

- SQL과 문법이 유사 ANSI표준 SQL 문법을 전부 지원

  - SELECT, FROM, WHERE, GROUP BY, HAVING, JOIN 등

- JPQL은 엔티티 객체를 대상으로 쿼리

- SQL은 데이터베이스 테이블을 대상으로 쿼리

- **JPQL 작성하면 SQL로 번역되어서 실행된다.**

- 테이블이 아닌 객체를 대상으로 검색하는 **객체 지향 쿼리**

- SQL을 추상화해서 데이터베이스 SQL에 의존하지 않는다.

- 한마디로 정의하면 **객체지향 SQL**

- 예시

  ![ORM10-1](/images/jpa/ORM-JPA/ORM10-1.png)

#### Criteria

- 문자가 아닌 자바코드로 JPQL을 작성할 수 있음

- SQL스럽지 않다.

- JPQL 빌더 역할

- JPA 공식기능

- 너무 복잡하고 실용성이 없다.

  - **Criteria 대신 QueryDSL 사용을 권장한다.**

- 예시

  ```java
  CriteriaBuilder cb = em.getCriteriaBuilder();
  CriteriaQuery<Member> query = cb.createQuery(Member.class);
  
  Root<Member> m = query.from(Member.class);
  CriteriaQuery<Member> cq = query.select(m).where(cb.equal(m.get("username"), "kim"));
  
  List<Member> resultList = em.createQuery(cq).getResultList();
  ```

#### QueryDSL 

- SQL 스럽다.
- 사용하기 위해서는 Setting이 필요하다.
- 문자가 아닌 자바코드로 JPQL을 작성할 수 있다.
- JPQL 빌더 역할
- 컴파일 시점에 문법 오류를 찾을 수 있다.
- **동적쿼리 작성 편리함.**
- **단순하고 쉽다.**
- **실무사용 권장.**

#### NativeSQL

- JPA가 제공하는 SQL을 직접 사용하는 기능

- JPQL로 해결할 수 없는 특정 데이터베이스에 의존적인 기능

  - 예시) 오라클의 CONNECT BY, 특정 DB만 사용하는 SQL 힌트

- 예시

  ```java
   List resultList = em.createNativeQuery("select member_id, city, street from member").getResultList();
  ```

- NativeQuery 사용 대신 **JDBC직접사용, SpringJdbcTemplate으로 대체**하여 사용할 수 있다. (더 편함)

  - JPA를 사용하면서 JDBC 커넥션을 직접 사용하거나, 스프링 JdbcTemplate, 마이바티스등을 함께 사용 가능
  - 단 영속성 컨텍스트를 적절한 시점에 강제로 플러시 필요
    - 예)**JPA를 우회해서 SQL을 실행하기 직전에 영속성 컨텍스트 수동 플러시 해야한다.**
      - JPQL은 **commit 할 때, 또는 query 날릴 때(createQuery()등) flush가 발생한다.**
      - JPA가 아닌 직접 DB connection으로 query를 날릴 때(JDBC직접사용, SpringJdbcTemplate사용 등)는 사전에 `em.flush()` 를 사용해서 수동으로 flush를 해줘야한다.

#### 정리

- JPQL을 확실히 알아야한다.
- QueryDSL로 작성하면 문법 버그 및 오타를 줄일 수 있다.
- JPQL 및 QueryDSL을 중점으로 학습하자.

<br><br>

### JPQL

- Java Persistence Query Language
- 객체지향 쿼리 언어
  - 테이블을 대상으로 쿼리하는것이 아닌 엔티티 객체를 대상으로 쿼리한다.
- **JPQL은 SQL을 추상화해서 특정 데이터베이스에 의존하지 않는다.**
- **JPQL은 결국 SQL로 변환된다.**

#### 문법

- select
  - select 절
    from 절
    [where 절] 
    [groupby 절]
    [having 절]
    [orderby 절]
- update
  - update 절 [where 절]
  - 한번에 여러개를 update 할 때, 사용
- delete
  - delete 절 [where 절]
  - 한번에 여러개 delete 할 때, 사용

```sql
select m from Member as m where m.age > 18
```

- **엔티티와 속성은 대소문자를 구분한다.**(Member, age...)
- JPQL 키워드는 대소문자를 구분하지 않는다.(select, from, where)
- **엔티티 이름 사용, 테이블 이름이 아니다.**
- 별칭은 필수(m)
  - as는 생략가능

#### 집합과 정렬

- JPA는 ANSI SQL 표준 function을 지원한다.
- COUNT(m) : 회원수
- SUM(m.age) : 나이 합
- AVG(m.age) : 나이 평균
- MAX(m.age) : 최대 나이
- MIN(m.age) : 최소 나이
- GROUP BY, HAVING
- ORDER BY

#### TypeQuery, Query

- TypeQuery : 반환 타입이 명확할 때 사용한다.

  ```java
  TypedQuery<Member> findMember = em.createQuery("select m from Member m", Member.class);
  ```

- Query : 반환 타입이 명확하지 않을 때 사용

  ```java
  Query query = em.createQuery("select m.age, m.name from Member m");
  ```

- 결과조회

  - `query.getResultList()` : 결과가 하나 이상일 때, List 반환
    - 결과가 없으면 빈 List
    
  - `query.getSingleResult()` : 결과가 한개일 때, 단일 객체 반환
    - 결과가 없으면, javax.persistence.NoResultException
    - 결과가 둘 이상 이면, javax.persistence.NonUniqueResultException

#### 파라미터 바인딩 - 이름 기준, 위치기준

- 이름기준

  ```java
  TypedQuery<Member> query = em.createQuery("select m from Member m where m.name = :username", Member.class);
  query.setParameter("username", "bong");
  Member findMember = query.getSingleResult();
  ```

- 위치기준

  ```java
  TypedQuery<Member> query = em.createQuery("select m from Member m where m.name = ?1", Member.class);
  query.setParameter(1, "bong");
  Member findMember = query.getSingleResult();
  ```

- **위치기준은 혼선을 줄 수 있으니, 안쓰는것을 추천한다.**

<br><br>

### 프로젝션

- SELECT 절에 조회할 대상을 지정하는 것

- 프로젝션 대상

  - 엔티티 타입

    `SELECT m FROM Member m`

    `SELECT m.team FROM Member m`

  - 임베디드 타입

    `SELECT m.address FROM Member m`

  - 스칼라 타입(숫자, 문자등 기본 데이터타입)

    `SELECT m.username, m.age FROM Member m`

- DISTINCT로 중복 제거

```java
List<Member> findMember = em.createQuery("select m from Member m", Member.class).getResultList();
```

- findMember는 영속성 컨텍스트에 관리가 될까?

  - 결과로 나오는 Member 모두 관리가 된다.

    ![ORM10-2](/images/jpa/ORM-JPA/ORM10-2.png)

  - 연관관계의 엔티티도 마찬가지로 관리가 된다.

#### 프로젝션 - 여러 값 조회

```sql
SELECT m.username, m.age FROM Member m
```

- 위와 같이 String, Integer 두개의 스칼라 타입을 조회하는 쿼리문은 응답 타입이 2개인데 어떻게 가져올까?

  - Query 타입으로 조회

    ![ORM10-5](/images/jpa/ORM-JPA/ORM10-5.png)

    - Object[] 타입으로 변환하여 사용

  - Object[] 타입으로 조회

    ![ORM10-3](/images/jpa/ORM-JPA/ORM10-3.png)

  - new 명령어로 조회

    ![ORM10-4](/images/jpa/ORM-JPA/ORM10-4.png)

    - DTO를 생성
    - jpql new 명령어 사용
      - **패키지명을 포함한 전체 클래스 명 입력**
      - **순서와 타입이 일치하는 생성자 필요**
      - QueryDSL을 사용하면 보다 편하게 사용할 수 있다.

<br><br>

### 페이징 API

- JPA는 페이징을 다음 두 API로 추상화
  - setFirstResult(int startPosition) : 조회 시작 위취
  - setMaxResult(int maxResult) : 조회할 데이터 수

![ORM10-6](/images/jpa/ORM-JPA/ORM10-6.png)

- **설정한 방언에 따라서** 페이징 SQL을 만들어준다.(Oracle 등의 DB에서는 페이징 처리가 굉장히 복잡했다...)

<br><br>

### 조인

- 내부 조인 : `SELECT m FROM Member m [INNER] JOIN m.team t`

-  외부 조인 :  `SELECT m FROM Member m LEFT [OUTER] JOIN m.team t`

- 세타 조인 : `select count(m) from Member m, Team t where m.username = t.name`

#### ON 절

- JPA 2.1 부터 지원 (우리가 쓰는건 대부분 2.1 이상이다.)
  1. 조인 대상 필터링
  2. **연과관계 없는 엔티티 외부 조인(하이버네이트 5.1부터 지원)**

#### 조인대상 필터링

예시 : 회원과 팀을 조인하면서, 팀 이름이 A인 팀만 조인

- JPQL : `SELECT m, t FROM Member m LEFT JOIN m.team t ON t.name ='A'`

- SQL : `SELECT m.*, t.* FROM member m LEFT JOIN TEAM t ON m.team_id = t.id and t.name = 'A'`

#### 연관관계 없는 엔티티 외부 조인

예시 : 회원의 이름과 팀의 이름이 같은 대상 외부 조인 

- JPQL : `SELECT m, t FROM Member m LEFT JOIN Team t ON m.username = t.name`

- SQL : `SELECT m.*, t.* FROM member m LEFT JOIN Team t ON m.usernmae = t.name`

<br><br>

### 서브 쿼리

- 예시 : 나이가 평균보다 많은 회원

  ```sql
  SELECT m FROM Member m
  where m.age > (SELECT avg(m2.age) FROM Member m2)
  ```

- 한건이라도 준한 고객

  ```sql
  SELECT m from Member m
  where (SELECT count(o) FROM Order o WHERE m = o.member) > 0
  ```

#### 지원함수

- (NOT) EXISTS : 서브쿼리에 결과가 존재하면 참

  - 팀 A 소속인 회원

    ```sql
    SELECT m FROM Member m
    where exists (select t from m.team t where t.name = '팀A')
    ```

  - ALL 모두 만족 시, 참

    - 전체 상품 각각의 재고보다 주문량이 많은 주문들

      ```sql
      SELECT o FROM Order o
      WHERE o.orderAmount > ALL(SELECT p.stockAmount FROM Product p)
      ```

  - ANY, SOME : 조건을 하나라도 만족하면 참

    - 어떤 팀이든 팀에 소속된 회원

      ```sql
      SELECT m FROM Member m
      WHERE m.team = ANY (SELECT t from Team t)
      ```

- (NOT) IN : 서브쿼리 결과중 하나라도 같은것이 있으면 참

  - 일반적인 SQL의 IN과 같다고 보면 된다.

#### JPA 서브쿼리 한계

- JPA는  WHERE, HAVING 절에서만 서브 쿼리 사용 가능
- SELECT 절도 가능 (JPA 표준 스펙은 아니고 하이버네이트에서 지원)
- **FROM 절의 서브쿼리는 현재 JPQL에서 불가능하다.**
  - **조인으로 풀 수 있으면 풀어서 해결**
  - 극복방법
    - Native SQL
    - 쿼리 분해해서 날린다.
    - 어플리케이션 로직에서 처리

<br><br>

### JPQL 타입 표현

- 문자 : 'HELLO', 'She''s' ('가 필요하면 두개')

- 숫자 : 10L(Long), 10D(Double), 10F(Float)

- Boolean : TRUE, FALSE

- ENUM : jpql.MemberType.ADMIN(패키지명 포함)

  ![ORM10-8](/images/jpa/ORM-JPA/ORM10-8.png)

  - 파라미터로 넣으면 좀 더 편하게 넣을 수 있다.

    ![ORM10-9](/images/jpa/ORM-JPA/ORM10-9.png)

- 엔티티 타입 : TYPE(m) = Member (상속 관계에서 사용)

  ![ORM10-7](/images/jpa/ORM-JPA/ORM10-7.png)

  - Book은 ITEM을 상속받는다.

#### JPQL 기타

- SQL과 문법이 같은 식은 다 가능하다.
  - AND, OR, NOT
  - =,  >, <, >=, <=
  - BETWEEN, LIKE, IS NULL 

<br><br>

### 조건식 - CASE

- 기본 CASE 식

  ```sql
  select
  case when m.age <= 10 then '학생요금' when m.age >= 60 then '경로요금'
  end
  else '일반요금'
  from Member m
  ```

- 단순 CASE 식 

  ```sql
  select
  case t.name
  when '팀A' then '인센티브110%' when '팀B' then '인센티브120%'
  end
  else '인센티브105%'
  from Team t
  ```

  - 정확한 매칭

- COALESCE : 하나씩 조회해서 null이 아니면 반환

  ![ORM10-11](/images/jpa/ORM-JPA/ORM10-11.png)

- NULLIF : 두 값이 같으면 null 반환, 다르면 첫번째 값 반환  

  ![ORM10-10](/images/jpa/ORM-JPA/ORM10-10.png)

<br><br>

### JPQL 기본함수

#### JPQL에서 제공하는 표준함수

- CONCAT 
  - 문자 더하기
- SUBSTRING 
  - 문자 자르기
- TRIM
  - 공백 없애기
- LOWER, UPPER
  - 대문자, 소문자
- LENGTH
  - String 길이
- LOCATE
  - String 검색 위치
- ABS, SQRT, MOD
- SIZE, INDEX
  - SIZE : 컬렉션의 사이즈..

#### 사용자 정의 함수

- 하이버네이트는 사용전 방언에 추가해야 한다.

  - 사용하는 DB방언을 상속받고, **사용자 정의함수를 등록한후, ** 사용한다.

    ```sql
    SELECT FUNCTION('group_concat', i.name) FROM ITEM i
    ```

  - 기본적인 함수들은 등록이 되어있다.

    ![ORM10-12](/images/jpa/ORM-JPA/ORM10-12.png)

  - 사용자 방언 생성

    - 사용하는 DB 방언을 상속받아서 만들고, 사용할 사용자정의함수를 등록한다.(생성자를 통해서)

      ![ORM10-14](/images/jpa/ORM-JPA/ORM10-14.png)

    - 생성한 방언을 사용한다고 설정한다.

      ![ORM10-13](/images/jpa/ORM-JPA/ORM10-13.png)

<br><br>