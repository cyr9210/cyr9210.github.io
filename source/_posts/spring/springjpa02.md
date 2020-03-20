---
title: 스프링 데이터 JPA02 - 프로젝트 세팅
date: 2019-04-30 15:18:46
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
<!-- more -->
### 프로젝트 세팅
- 데이터 베이스 실행(PostgreSQL로 진행)
    - 참고 : [스프링 데이터 PostgreSQL](https://cyr9210.github.io/2019/04/15/Spring/springboot13/#PostgreSQL-%ED%8F%AC%EC%8A%A4%ED%8A%B8%EA%B7%B8%EB%A0%88%EC%8A%A4)
    - 드라이버 추가 (postgres 의존성 추가)
- 프로젝트 생성
- 의존성 추가
    - 스프링 부트
        - 스프링 부트 v2.*
        - 스프링 프레임워크 v5.*
    - 스프링 부트 스타터 JPA
        - JPA 프로그래밍에 필요한 의존성 추가
            - JPA v2.*
            - Hibernate v5.*
        - 자동 설정: HibernateJpaAutoConfiguration
            - 컨테이너가 관리하는 EntityManager (프록시) 빈 설정
            - PlatformTransactionManager 빈 설정
            - JPA사용하는데 필요한 모든 빈들이 자동으로 등록된다.
            
- JDBC 설정
    - jdbc:postgresql://localhost:5432/springboot
    - username = bong
    - password = pass
    
- application.properties 설정
    - spring.jpa.properties.hibernate.jdbc.lob.non_contextual_creation=true
        - createclob()이라는 메소드를 구현하지 않아 warning이 발생하는데 그것을 방지함
    - spring.jpa.hibernate.ddl-auto=create
        - 매번 스키마를 생성한다.(기존에 있던것을 삭제하고..)
        - update로 정의 시, 데이터들이 남아있고 추가된것만 추가된다.(컬럼 및 데이터)
            - 그러나, 컬러명 변경 또는 삭제는 해결할 수 없다.(DB 마이그레이션 툴 사용이 필요하다.)
        
- Entity 생성
    ![springjpa](/images/jpa/jpa02-1.png)
    - 모든 필드값에 @Column이 생략되어있다고 생각하면 된다.
    - lombok 사용하였음
    
- 데이터 저장
    - JPA 사용
        [springjpa](/images/jpa/jpa02-2.png)
        - EntityManager는 JPA에 가장 핵심 클래스이다.
        - EntityManager와 관련된 모든 작업은 한 트랜잭션 안에서 일어나야한다.
            - @Transactional 사용하여 적용
                - 클래스에 적용하여 모든 메소드에 적용
                - 메소드에 적용 시, 해당 메소드에만 작
    - Hibernate 사용
        [springjpa](/images/jpa/jpa02-3.png)
        - Hibernamte의 가장 핵심적인 api는 Session이다.
        - Session을 꺼내서 사용하여 데이터를 저장한다. 
<br><br>