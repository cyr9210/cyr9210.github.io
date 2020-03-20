---
title: ORM06 - 다양한 연관관계 매핑(다대일, 일대다, 일대일, 다대)
date: 2019-10-19 00:21:33
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

## 다양한 연관관계 매핑

### 연관관계 매핑시 고려사항
<!-- more -->
- 다중성
- 단방향, 양방향
- 양방향일 때, 연관관계의 주인

#### 다중성

- 다대일 : @ManyToOne
- 일대다 : @OneToMany
- 일대일 : @OneToOne
- 다대다 : @ManyToMany
  - 실무에서는 쓰지 않는다.

#### 단방향, 양방향

- 테이블
  - 외래키 하나로 양쪽 조인 가능
  - 사실 방향이라는 개념이 없음
- 객체
  - 참조용 필드가 있는 쪽으로만 참조가능
  - 한쪽만 참조하면 단방향
  - 양쪽이 서로 참조하면 양방향
    - 사실 **단방향 두개**

#### 연관관계의 주인

- 테이블은 외래 키 하나로 두 테이블이 연관관계를 맺음
- 객체 양방향 관계는 A -> B, B -> A 처럼 참조가 두개
- 객체 양방향 관계는 참조가 2군데 있음. 둘중 테이블 외래키를 관리할 곳을 지정해야한다.
- 연관관계의 주인 : 외래키를 관리하는 참조
- 주인의 반대편 외래 키에 영향을 주지 않음, 단순 조회 가능

<br><br>

### 다대일(N : 1)

#### 다대일 단방향

![ORM06-2](/images/jpa/ORM-JPA/ORM06-2.png)

- 가장 많이 사용하는 연관관계
- 다대일의 반대는 일대다

#### 다대일 양방향

![ORM06-1](/images/jpa/ORM-JPA/ORM06-1.png)

- 외래 키가 있는 쪽이 연관관계의 주인
  - 다가 연관간계의 주인이 되는것이 좋다.
- 양쪽을 서로 참조하도록 개발
  - 주인이 아닌쪽은 읽기만 가능

<br><br>

### 일대다(1 : N)

#### 일대다 단방향

![ORM06-3](/images/jpa/ORM-JPA/ORM06-3.png)

- 이모델은 권장하지 않음

- 일대다 단방향은 일대다(1 : N)에서 **일(1)이 연관관계의 주인**

- 테이블 일대다 관계는 항상 **다(N) 쪽에 외래 키가 있음**

- 객체와 테이블의 차이 때문에 반대편 테이블의 외래키를 관리하는 특이한 구조

  ![ORM06-4](/images/jpa/ORM-JPA/ORM06-4.png)

- @JoinColumn을 꼭 사용해야 한다. 그렇지 않으면 조인 테이블 방식을 사용함(중간에 테이블이 추가된다.)

- 단점

  - 엔티티가 관리하는 외래키가 다른 테이블에 있음
  - 연간관계 관리를 위해 추가로 UPDATE SQL 실행

- **일대다 단방향 매핑보다는 다대일 양방향 매핑을 사용하자.**

#### 일대다 양방향

![ORM06-5](/images/jpa/ORM-JPA/ORM06-5.png)

- 이런 매핑은 공식적으로 존재 X

- `@JoinColumn(insertable=false, updatable=false) ` 을 추가한다.

  ```java
  @Entity
  public class Team {
  
      @Id @GeneratedValue
      @Column(name = "team_id")
      private Long id;
  
      @Column(name = "name", length = 50)
      private String name;
  
      @OneToMany
      @JoinColumn(name = "team_id")
      private List<Member> members = new ArrayList<>();
  }
  ```

  ```java
  @Entity
  public class Member {
  
      @Id @GeneratedValue
      @Column(name = "member_id")
      private Long id;
  
      @Column(name = "name")
      private String username;
      
      @ManyToOne
      @JoinColumn(name="team_id", insertable = false, updatable = false)
      private Team team;
  }
  ```

  - 읽기 전용 필드를 사용해서 양방향 처럼 사용하는 방법
    - insertable = false, updatable = false 를 쓰면 읽기 전용이 된다.
  - **다대일 양방향을 사용하자.**

<br><br>

### 일대일(1 : 1)

#### 일대일 관계

![ORM06-6](/images/jpa/ORM-JPA/ORM06-6.png)

- 일대일 관계는 그 반대도 일대일
- 주 테이블이나 대상 테이블 중에 외래 키 선택 가능
  - 주 테이블 외래 키
  - 대상 테이블에 외래 키
- **외래 키에 데이터베이스 유니크 제약조건 추가**
  - 안줘도 되지만 세심한 관리가 필요해진다.
- @JoinColumn을 넣어주는것을 추천(default값이 지저분하다.)

#### 일대일: 주 테이블에 외래 키 양방향

![ORM06-7](/images/jpa/ORM-JPA/ORM06-7.png)

- 다대일 양방향 매핑 처럼 **외래 키가 있는곳이 연관관계의 주인**
- **반대편은 mappedBy 적용**

#### 일대일: 대상 테이블에 외래 키 단방향 정리

- **단방향 관계는 JPA에서 지원하지 않는다.**
- 양방향 관계는 지원

#### 일대일: 대상 테이블에 외래 키 양방향 정리

![ORM06-8](/images/jpa/ORM-JPA/ORM06-8.png)

- 일대일 주 테이블에 외래 키 양방향과 매핑 방법은 같음 

#### 정리

- 주 테이블 외래 키
  - 주 객체가 대상 객체의 참조를 가지는 것처럼 주 테이블에 외래 키를 두고 대상 테이블을 찾음
  - 객체지향 개발자가 선호
  - JPA매핑관리
  - 장점 : 주 테이블만 조회해도 대상 테이블에 데이터가 있는지 확인가능
  - 단점 : 값이 없으면 외래 키에 null 허용
- 대상 테이블에 외래 키
  - 대상 테이블에 외래 키가 존재
  - 전통적인 데이터베이스 개발자 선호
  - 장점 : 주 테이블과 대상 테이블을 일대일에서 일대다 관계로 변경할 때 테이블 구조 유지
  - 단점 : 프록시 기능의 한계로 **지연로딩으로 설정해도 항상 즉시 로딩됨**

<br><br>

### 다대다(N : M)

- **실무에서 사용하지 않는것을 추천한다.**

- 관계형 데이터베이스는 정규화된 테이블 2개로 다대다 관계를 표현할 수 없음

  ![ORM06-9](/images/jpa/ORM-JPA/ORM06-9.png)

  - 연결 테이블을 추가해서 일대다, 다대일 관계로 풀어내야한다.

- 객체는 컬렉션을 사용해서 객체 2개로 다대다 관계 가능

  ![ORM06-10](/images/jpa/ORM-JPA/ORM06-10.png)

![ORM06-12](/images/jpa/ORM-JPA/ORM06-12.png)

- `@ManyToMany` 사용

- `@JoinTable` 로 연결 테이블 지정

  - name = 생성될 연결 테이블의 이름

- 다대다 매핑 : 단방향, 양방향 가능

  - 양방향 시, `@ManyToMany(mappedBy ="")`  옵션으로 대상 객체에 추가

    ![ORM06-13](/images/jpa/ORM-JPA/ORM06-13.png)

#### 다대다 매핑의 한계

- 편리해 보이지만 실무에서 사용하지 않는것을 추천한다.
- 실제 로직은 연결 테이블이 단순히 연결만하고 끝나지 않음
- 주문시간, 수량같은 데이터가 들어올 수 있음.

#### 다다대 매핑의 한계 극복

- 연결 테이블용 엔티티 추가**(연결 테이블을 엔티티로 승격**)

  ![ORM06-11](/images/jpa/ORM-JPA/ORM06-11.png)

- @ManyToMany -> @OneToMany, @ManyToOne

  ```java
  @Entity
  @NoargsConstructor
  @Getter
  public class MemberProduct {
  
      @Id @GeneratedValue
      private Long id;
  
      @ManyToOne
      @JoinColumn(name = "member_id")
      private Member member;
  
      @ManyToOne
      @JoinColumn(name = "product_id")
      private Product product;
  }
  ```

  - 반대쪽에는 `@OneToMany(mappedBy = "product")`,  `@OneToMany(mappedBy = "member")`와같이 설정해준다.
  - 원하는 필드를 추가 가능하다.

- PK는 의미없는값을 쓰느것을 추천한다. 

  - 유연성이나, 운영시에 유리하다.

<br><br>