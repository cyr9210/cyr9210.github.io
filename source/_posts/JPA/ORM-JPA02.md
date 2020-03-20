---
title: ORM02 - JPA 시작하기(프로젝트 생성, 애플리케이션 개발)
date: 2019-10-08 10:58:14
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

## JPA 시작하기
<!-- more -->
### 프로젝트 생성

#### H2 데이터베이스
- 최고의 실습용 DB
- 가볍다
- 웹용 쿼리를 제공
- MySQL, Oracle 데이터베이스 시뮬레이션 기능
- sequence, Auto Increment 기능 지원

#### H2 데이터베이스 설치와 실행
- http://www.h2database.com/
 
  ![ORM02-1](/images/jpa/ORM-JPA/ORM02-1.png)
  - OS에 맞게 다운로드 및 압축해제
    - 윈도우 외 : All Platforms 다운

- 압축해제위치/h2/bin/h2.sh 실행(mac의 경우)
  ![ORM02-2](/images/jpa/ORM-JPA/ORM02-2.png)

#### 프로젝트 생성
- 자바8
- 메이븐
  - groupId : jpa-basic
  - artifactId : ex1-helo-jpa
  - version : 1.0.0

#### 라이브러리 추가 - pom.xml
```xml
<dependencies>
    <!-- JPA 하이버네이트 -->
    <dependency>
        <groupId>org.hibernate</groupId>
        <artifactId>hibernate-entitymanager</artifactId>
        <version>5.3.10.Final</version>
    </dependency>
    <!-- H2 데이터베이스 -->
    <dependency>
        <groupId>com.h2database</groupId>
        <artifactId>h2</artifactId>
        <version>1.4.199</version>
    </dependency>
</dependencies>
```

- JPA는 인터페이스의 모음이며 구현체로 하이버네이트를 선택했다.
- hibernate 라이브러리가 jpa 인터페이스를 가지고 있다.
  ![ORM02-3](/images/jpa/ORM-JPA/ORM02-3.png)
  
- h2 DB 버전을 맞추는게 좋다. 
  ![ORM02-4](/images/jpa/ORM-JPA/ORM02-4.png)

#### JPA 설정하기 - persistence.xml
- JPA 설정파일 
- 위치는 반드시 /META-INF/persistence.xml
  ![ORM02-7](/images/jpa/ORM-JPA/ORM02-7.png)

- persistence-unit_name으로 이름지정 가능
  ![ORM02-6](/images/jpa/ORM-JPA/ORM02-6.png)

- javax.persistence로 시작 : JPA 표준 속성
  - hibernate가 아닌 다른 구현체를 사용하여도 같은 설정이 가능

- hibernate로 시작 : 하이버네이트 전용 속성
- 스프링 부트의 경우 properties or yml 파일로 설정이 가능하다.
- 설정파일 예시
  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <persistence version="2.2"
               xmlns="http://xmlns.jcp.org/xml/ns/persistence" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
               xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/persistence http://xmlns.jcp.org/xml/ns/persistence/persistence_2_2.xsd">
      <persistence-unit name="hello">
          <properties>
              <!-- 필수 속성 -->
              <property name="javax.persistence.jdbc.driver" value="org.h2.Driver"/>
              <property name="javax.persistence.jdbc.user" value="sa"/>
              <property name="javax.persistence.jdbc.password" value=""/>
              <property name="javax.persistence.jdbc.url" value="jdbc:h2:tcp://localhost/~/test"/>
              <property name="hibernate.dialect" value="org.hibernate.dialect.H2Dialect"/>
              <!-- 옵션 -->
              <property name="hibernate.show_sql" value="true"/>
              <property name="hibernate.format_sql" value="true"/>
              <property name="hibernate.use_sql_comments" value="true"/>
              <!--<property name="hibernate.hbm2ddl.auto" value="create" />-->
          </properties>
      </persistence-unit>
  </persistence>
  ```

  - javax.persistence.jdbc.driver : DB 드라이버
  - javax.persistence.jdbc.user : DB id
  - javax.persistence.jdbc.password : DB password
  - javax.persistence.jdbc.url : DB 위치
  - hibernate.dialect : 데이터 베이스 방언 설정
    - hibernate는 특정 데이터베이스에 종속적이지 않다.
    - 각 DB별 방언을 설정하여 일정한 결과를 나올 수 있도록한다.
    - hibernate는 40가지 이상의 데이터베이스 방언을 지원한다.

### 애플리케이션 개발

#### JPA 구동방식

![ORM02-5](/images/jpa/ORM-JPA/ORM02-5.png)

1. META-INF/persistence.xml에서 설정 정보를 확인
2. 확인한 정보로 EntityManagerFactory를 생성

   ![ORM02-8](/images/jpa/ORM-JPA/ORM02-8.png)

3. 필요할 때마다 EntityManger를 생성한다.

   ![ORM02-9](/images/jpa/ORM-JPA/ORM02-9.png)
   - EntityManagerFactory는 애플리케이션 로딩시점에 한번만 생성해준다.
   - 한 트랜잭션에 대한 DB커넥션을 위해서는 EntityManager를 만든다.
   - EntityManger는 쓰레드간 절대 공유하지 않는다.(사용 후 버림.)
   - **JPA의 모든 데이터 변경은 트랜잭션 안에서 실행**

#### 객체와 테이블을 생성하고 매핑하기

- h2 DB에 객체에 해당하는 테이블 생성 쿼리를 날린다.
  ```sql
  create table Member (
   id bigint not null,
   name varchar(255),
   primary key (id)
  );
  ```

- 객체 클래스 생성
  ```java
  @Entity
  public class Member {
  
      @Id
      private Long id;
      private String name;
  
      public Long getId() {
          return id;
      }
  
      public void setId(Long id) {
          this.id = id;
      }
  
      public String getName() {
          return name;
      }
  
      public void setName(String name) {
          this.name = name;
      }
  }
  ```

  - @Entity : JPA가 관리할 객체
  - @Table : name값을 지정해주면 설정한 이름의 테이블고 매핑된다. (없으면 클래스 이름)
    @Column 도 마찬가지로 동작한다. (없으면 필드 이름)
  - @Id : 데이터베이스 PK와 매칭

#### 회원저장

- em.persist(Object entity);![ORM02-10](/images/jpa/ORM-JPA/ORM02-10.png)
  - hibernate.show_sql : 쿼리가 출력된다.
  - hibernate.format_sql : 쿼리가 이쁘게 포맷팅된다.
  - hibernate.use_sql_comments : 이 쿼리가 왜나왔는지 주석이 달린다.
- 사실은 위와 같은 코드는 좋지 않은 코드이다.
- EntityManger를 만드는 과정에서 에러가 발생한다면.. close() 메소드등을 호출하지 못한다.
- 실제론 이와같은 코드가 될 것이다.![ORM02-11](/images/jpa/ORM-JPA/ORM02-11.png)

#### 회원 단 건 조회

- Member member = em.find(Member.class, 1L);
  ![ORM02-12](/images/jpa/ORM-JPA/ORM02-12.png)

#### 회원 삭제

- 찾은 회원을 remove 메소드 파라미터에 넣어준다.
  em.remove(entity);
  ![ORM02-14](/images/jpa/ORM-JPA/ORM02-14.png)

#### 회원수정

- 찾은 회원을 객체를 수정한다.

- **따로 저장하는 업데이트 메소드를 호출하지 않았지만, 트랜잭션을 커밋하는 시점에 변경사항을 체크한다. (dirty checking)**
  변경 사항이 있으면 update 쿼리를 날려준다.
  ![ORM02-13](/images/jpa/ORM-JPA/ORM02-13.png)

JPA 쓸 때, 원하는 데이터를 최적화 및 목적에 맞도록 query를 날리고 싶다면 어떻게 해야할까..
JPA에서는 JPQL을 통해 도와준다.

#### JPQL 소개

- JPA를 사용하면 엔티티 **객체를 중심으로 개발**
- 문제는 검색 쿼리
- 테이블이 아닌 **객체를 대상으로 검색하는 객체 지향 쿼리**
- 모든 DB 데이터를 객체로 변환해서 검색하는 것은 불가능
- 애플리케이션이 필요한 데이터만 DB에서 불러오려면 **결국 검색 조건이 포함된 SQL이 필요**
- SQL을 추상화해서 특정 데이터베이스 SQL에 의존X (DB방언)
- **JPQL을 한마디로 정의하면 객체 지향 SQL**
<br><br>