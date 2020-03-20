---
title: 스프링 데이터 JPA04 - Cascade, Fetch, Query, 스프링 데이터 JPA 소개 및 원리
date: 2019-04-30 20:12:00
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
![springf](/images/jpa_logo.png)
# 스프링 데이터 JPA(inflearn) - 백기선 
## 핵심 개념 이해
### 엔티티의 상태와 Cascade
<!-- more -->
#### 엔티티의 상태
![springjpa](/images/jpa/jpa04-1.png)
- Transient: JPA가 모르는 상태
    - ex)  new 사용하여 객체생성 상태
- Persistent: JPA가 관리중인 상태 (1차 캐시, Dirty Checking(변경사항을 계속체크), Write Behind, ...)
    - ex)  session.save()를 통해 저장한 상태
    - 1차 캐시(persistent context(session, entitiymanager등)에 저장된다.)
        ![springjpa](/images/jpa/jpa04-2.png)
        - 바로 쿼리문을 날리지 않고 1차캐싱 후, 트랜잭션 종료 시 객체를 체크하여 쿼리문을 날린다.
        (불필요한 쿼리문 날리지 않는다.)
- Detached: JPA가 더이상 관리하지 않는 상태.
    - session이 끝난상태
- Removed: JPA가 관리하긴 하지만 삭제하기로 한 상태.

#### Cascade
- 엔티티의 상태 변화를 전파 시키는 옵션
- 참조하고 있던 객체들도 함께 변화한다.(설정을 해주어야한다.)
    ![springjpa](/images/jpa/jpa04-3.png)
    - remove 옵션을 주면 함께 사라진다.
    ![springjpa](/images/jpa/jpa04-4.png)
    
### Fetch
- 연관 관계의 엔티티의 정보를 어떻게 가져올것인가에 대한 설정
    - 지금 : Eager
        - @ManyToOne (기본값)
            - comment를 통해서 post를 호출할때 쿼리를 두개 날라지 않는다. 조인쿼리문을 사용하여 출력
            ![springjpa](/images/jpa/jpa04-6.png)
        - @OneToMany의 fetch모드를 eager로 변경하게 되면 comment정보 까지 가져오는것을 확인 할 수 있다.
        ![springjpa](/images/jpa/jpa04-5.png)
    - 나중에 : Lazy
        - @OneToMany (기본값)
        
>**Hibernate API (get vs load)**
session.get();, session.load();과 같이 사용이 가능하다.
get
    - DB에서 정보를 가져온다. (Null이 나올 수 있음.)
load
    - DB뿐만 아니라 프록시에서도 정보를 가져올 수 있다.
    - 정보가 없으면 예외를 던진다.

<br>

### Query
지금까지 Hibernate를 사용하여(entitymanager.unwrap(Session.class);) Session객체를 사용하였다.(save(), get() 등의 api를 사용했다.)
(JPA를 사용하여 EntityManager의 persist(), find()를 사용할 수도 있다.)
이제 JPA를 사용하여 Query하는 방법에 대해 알아보자. 

#### JPQL
```
TypedQuery<Post> query = entityManager.createQuery("SELECT p FROM Post As p", Post.class);
List<Post> posts = query.getResultList();
```

- Java Persistence Query Language / Hibernate Query Language
- **데이터베이스 테이블이 아닌, 엔티티 객체 모델 기반으로 쿼리 작성.**
- JPA 또는 하이버네이트가 해당 쿼리를 SQL로 변환해서 실행함.
- [참고문서](https://docs.jboss.org/hibernate/orm/5.2/userguide/html_single/Hibernate_User_Guide.html#hql)
- 단점
    - 오타발생이 가능
    - 타입세이프하지 않다.(문자열이기 때문에..)

#### Criteria
```
CriteriaBuilder builder = entityManager.getCriteriaBuilder();
CriteriaQuery<Post> criteria = builder.createQuery(Post.class);
Root<Post> root = criteria.from(Post.class);
criteria.select(root);
List<Post> posts = entityManager.createQuery(criteria).getResultList();
```

- 타입 세이프 쿼리
- [참고문서](https://docs.jboss.org/hibernate/orm/5.2/userguide/html_single/Hibernate_User_Guide.html#criteria)
<br>

#### Native Query
```
List<Post> posts = entityManager
.createNativeQuery("SELECT * FROM Post", Post.class)
.getResultList();
```
- 타입을 지원하지 않지만 타입을 받긴한다..
- [참고문서](https://docs.jboss.org/hibernate/orm/5.2/userguide/html_single/Hibernate_User_Guide.html#sql)
<br>

### 스프링 데이터 JPA 소개 및 원리

#### JpaRepository<Entity, Id> 인터페이스
- 매직 인터페이스
- @Repository가 없어도 빈으로 등록해 줌.

#### @EnableJpaRepositories
- 매직의 시작은 여기서 부터

#### 매직은 어떻게 이뤄지나?
- 시작은 @Import(JpaRepositoriesRegistrar.class)
- 핵심은 ImportBeanDefinitionRegistrar 인터페이스
<br><Br>
---
##### tip
- sql 문 보이도록 하기
    - logging.level.org.hibernate.SQL=debug
    - spring.jpa.show-sql=true
- sql문에서 value값 보기
    - logging.level.org.hibernate.type.descriptor.sql=trace
---
<br>

