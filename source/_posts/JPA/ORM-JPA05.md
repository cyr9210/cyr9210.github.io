---
title: ORM05 - 연관관계 매핑 기초(단방향 연관관계, 양방향 연관관계, 연관관계의 주인, 연관관계 매핑 주의사항)
date: 2019-10-13 17:49:58
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

## 연관관계 매핑 기초
<!-- more -->
- 객체와 테이블 연관관계의 차이를 이해가 필요
- **객체의 참조와 테이블의 외래키 매핑**
- 방향(Direction) : 단방향, 양방향
- 다중성(Multiplicity) : 다대일(N : 1), 일대다 (1 : N), 일대일 (1 : 1), 다대다(N : M)
- 연관관계의 주인(Owner) : 객체 양방향 연관관계는 관리 주인이 필요

### 연관관계가 필요한 이유

- 객체를 테이블에 맞추어 데이터 중심으로 모델링하면, 협력 관계를 만들 수 없다.
- 테이블은 외래 키로 조인을 사용해서 연관된 테이블을 찾는다.
- 객체는 참조를 사용해서 연관객체를 찾는다.
- 테이블과 객체 사이에는 이런 큰 간격이 있다.

### 단방향 연관 관계

![ORM05-1](/images/jpa/ORM-JPA/ORM05-1.png)

#### 도메인 모델링

```java
@Entity
@Getter @Setter @NoArgsConstructor
public class Member {

    @Id
    @GeneratedValue
    private Long id;

    @Column(name = "name")
    private String username;

    @ManyToOne
    @JoinColumn(name = "team_id")
    private Team team;
}
```

```java
@Entity
@Getter @Setter @NoArgsConstructor
public class Team {

    @Id @GeneratedValue
    @Column(name = "team_id")
    private Long id;

    @Column(name = "name", length = 50)
    private String name;
}
```

#### 실행 코드

```java
public class JpaMain {

    public static void main(String[] args) {
        EntityManagerFactory emf = Persistence.createEntityManagerFactory("hello");

        EntityManager em = emf.createEntityManager();

        EntityTransaction tx = em.getTransaction();
        tx.begin();

        try {
            Team team = new Team();
            team.setName("team A");
            em.persist(team);

            Member member = new Member();
            member.setUsername("bong");
            member.setTeam(team);
            em.persist(member);

            Member findMember = em.find(Member.class, member.getId());
            Team findTeam = findMember.getTeam();

            System.out.println("findTeam.name : " + findTeam.getName());

            tx.commit();
        } catch (Exception e) {
            tx.rollback();
        }finally {
            em.close();
        }
        emf.close();
    }
}
```

- member.getTeam() 으로 team을 찾을 수 있다.

#### 연관관계수정 실행코드

```java
public class JpaMain {

    public static void main(String[] args) {
        EntityManagerFactory emf = Persistence.createEntityManagerFactory("hello");

        EntityManager em = emf.createEntityManager();

        EntityTransaction tx = em.getTransaction();
        tx.begin();

        try {
            Team team = new Team();
            team.setName("team A");
            em.persist(team);

            Member member = new Member();
            member.setUsername("bong");
            member.setTeam(team);
            em.persist(member);

            Member findMember = em.find(Member.class, member.getId());
            System.out.println("findTeam.name : " + findMember.getTeam().getName());

            Team newTeam = new Team();
            newTeam.setName("updateTeam");
            em.persist(newTeam);

            findMember.setTeam(newTeam);
            System.out.println("update findTeam.name : " + findMember.getTeam().getName());

            tx.commit();
        } catch (Exception e) {
            tx.rollback();
        }finally {
            em.close();
        }
        emf.close();
    }
}
```

- 객체만 변경해주면 DB에도 업데이트 된다.

<br><br>

### 양방향 연관관계와 연관관계의 주인

![ORM05-2](/images/jpa/ORM-JPA/ORM05-2.png)

#### 도메인 모델링

```java
@Entity
@Getter @Setter @NoArgsConstructor
public class Member {

    @Id @GeneratedValue
    @Column(name = "member_id")
    private Long id;

    @Column(name = "name")
    private String username;

    @ManyToOne
    @JoinColumn(name = "team_id")
    private Team team;
}
```

```java
@Entity
@Getter @Setter @NoArgsConstructor
public class Team {

    @Id @GeneratedValue
    @Column(name = "team_id")
    private Long id;

    @Column(name = "name", length = 50)
    private String name;

    @OneToMany(mappedBy = "team")
    private List<Member> members = new ArrayList<>();
}
```

- 연관관계의 주인은 주로 관계의 다수 인쪽에 설정하는것이 좋다.
  - 바꿔 말하면 @ManyToOne인쪽이 주인
- mappedBy는 연관관계의 주인이 아닌쪽에 설정한다.
  - 바꿔 말하면 @OneToMany인쪽에 mappedBy

테스트 실행에 앞서 알아야 할 내용들이 있다.

#### 객체의 양방향 관계

- 객체 연과관계 = 단방향 연관관계 2개

  - 회원 -> 팀 연관관계 1개(단방향)
  - 팀 -> 회원 연관관계 1개(단방향)

- **객체의 양방향 관계는 사실 양방향 관계가 아니라 서로 다른 단방향 관계 2개이다.**

  - 객체를 양방향으로 참조하려면 단방향 연관관계 2개를 만들어야 한다.

    ![ORM05-6](/images/jpa/ORM-JPA/ORM05-6.png)

#### 테이블의 양방향 연관관계

- 테이블 연관관계 1개

  - 회원 <->팀 연관관계 1개(양방향)

- 테이블은 **외래 키** 하나로 두 테이블의 연관관계를 관리

  - MEMBER.TEAM_ID 외래 키 하나로 양방향 연관관계 가짐(양쪽으로 조인 가능)

    ![ORM05-7](/images/jpa/ORM-JPA/ORM05-7.png)

#### 외래 키 관리

![ORM05-5](/images/jpa/ORM-JPA/ORM05-5.png)

- 객체의 경우 두개의 단방향 관계이기 때문에 한족에서 외래 키를 관리해야한다.

- 외래키를 관리하는 객체를 **연관관계의 주인(Owner)**이라고 한다.

- 한쪽은 읽기만 가능한 가짜 매핑이 될  수 밖에 없다.

  ![ORM05-8](/images/jpa/ORM-JPA/ORM05-8.png)

#### 연관관계의 주인(Owner)

양방향 매핑 규칙을 기준으로 ..

- 객체의 두 관계중 하나를 연관관계의 주인으로 지정
- 연관관계의 주인만이 외래 키를 관리(등록, 수정)
- **주인이 아닌쪽은 읽기만 가능**
- 주인은 mappedBy 속성 사용 X
- 주인이 아니면 mappedBy 속성으로 주인 지정
  - 주인쪽에서 현재 자신으로 매핑된 필드 이름을 입력한다.

#### 누구를 주인으로?

- 외래키가 있는 곳을 주인
  - DB에서 foreign key는 다인쪽이 갖는다.
- 즉, 다대일 관계에서 다인쪽이 주인이 되는것이 좋다.(헷갈리지 않음)
  - 예) team(1) : member(n) -> 주인 member

#### 주의사항

- 양방향 매핑 시, 연관관계의 주인에 값을 입력 또는 수정해야 DB에 반영된다.
- **주인이 아닌쪽은 읽기만 가능**

#### 실행코드

- 연관관계 주인쪽 입력

  ```java
  Team team = new Team();
  team.setName("team A");
  em.persist(team);
  
  Member member = new Member();
  member.setUsername("bong");
  member.setTeam(team);
  em.persist(member);
  
  em.flush();
  em.clear();
  
  Team findTeam = em.find(Team.class, 1L);
  List<Member> members = findTeam.getMembers();
  ```

  ![ORM05-3](/images/jpa/ORM-JPA/ORM05-3.png)

- 주인이 아닌쪽 입력

  ```java
  Team team = new Team();
  team.setName("team A");
  em.persist(team);
  
  Member member = new Member();
  member.setUsername("bong");
  
  team.getMembers.add(member);
  
  em.persist(member);
  
  em.flush();
  em.clear();
  
  Team findTeam = em.find(Team.class, 1L);
  List<Member> members = findTeam.getMembers();
  ```

  ![ORM05-4](/images/jpa/ORM-JPA/ORM05-4.png)

  - **정상적으로 DB에 저장되지 않는것을 볼 수 있다.**

<br><br>

### 주의점

- 양방향 매핑시 가장 많이 하는 실수

  - 연관관게의 주인에 값을 입력하지 않음

    ```java
    Member member = new Member();
    member.setUsername("bong");
    em.persist(member);
    
    Team team = new Team();
    team.setName("team A");
    team.getMembers().add(member);
    em.persist(team);
    
    em.flush();
    em.clear();
    ```

    ![ORM05-4](/images/jpa/ORM-JPA/ORM05-4.png)

    - 연관관계의 주인은 Member이다. 
    - Team에서만 Member를 지정해주었다.
    - DB에는 반영되어지지 않는다.

#### 양방향 연관관계 주의

- **순수 객체 상태를 고려해서 항상 양쪽에 값을 설정하는것을 추천한다.**

  - 객체지향적으로 생각을하면 양쪽에 값을 넣는것 맞기 때문에...

  - flush하지 않고 1차캐시 값을 사용할 때, 값이 정확하지 않다.

    - flush() 사용 -> DB에 바로 반영

      ![ORM05-9](/images/jpa/ORM-JPA/ORM05-9.png)

    - flush() 사용안함 -> 1차 캐시 값 사용

      ![ORM05-10](/images/jpa/ORM-JPA/ORM05-10.png)

  - test케이스 작성 시, JPA를 사용하지 않고 JAVA코드로 작성할 때 값이 정확하지 않다.

  - **연관관계 편의 메소드를 생성하자**

    ```java
    public void changeTeam (Team team) {
        this.team = team;
        team.getMembers().add(this);
    }
    ```

    - 이런식으로 양방향 값을 변경해주는 메소드를 만들어 놓는것이 좋다.
    - 양쪽에 이러한 연관관계 설정 메소드가 있으면 헷갈리수 있으니 한쪽에만 만들어 놓는것을 추천한다.

- 양방향 매핑시에 무한 루프를 조심하자

  - toString()
    - 양방향 매핑 필드는 제외해야한다.
  - lombok
    - 양방향 매핑 필드는 제외해야한다.
  - JSON생성
    - Controller에서 Entity를 직접 반환하는일은 없도록 해야한다.
    - DTO사용하여 반환한는 것이 좋다.

##### 양방향 매핑 정리

- **단방향 매핑만으로 이미 연관관계 매핑은 설계는 완료가 되야한다.**
- 양방향 매핑은 반대 방향으로 조회기능만 추가된것 뿐
- JPQL에서 역방향으로 탐색할 일이 많을때 양방향이 필요.
- **단방향 매핑을 잘 하고 양방향은 필요할 때만 추가 (테이블에 영향을 주지 않음)**

<br><br>