---
title: 스프링 부트 활용13 - 스프링 데이터
date: 2019-04-15 14:34:06
tags: SpringBoot
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

![springboot](/images/springboot_logo.png)
# 스프릥 부트 개념과 활용13(inflearn) - 백기선 
## Spring boot

<!-- more -->

### 스프링 데이터
![springboot](/images/springboot/springboot13-1.png)

#### 인메모리 데이터베이스
- 스프링 부트가 지원하는 인-메모리 데이터베이스
    - H2 (추천, 콘솔 때문에...)
    - HSQL
    - Derby

- Spring-JDBC가 클래스패스에 있으면 자동 설정이 필요한 빈을 설정 해줍니다.
    - DataSource
    - JdbcTemplate

- 스프링부트는 인메모리 데이터베이스 의존성이 설정이 되있고, Data Source를 설정하지 않으면 스프링 부트는 자동으로 인메로리 데이터베이스를 설정해준다.

- 인-메모리 데이터베이스 기본 연결 정보 확인하는 방법
![springboot](/images/springboot/springboot13-2.png)
    - URL: “testdb”
    - username: “sa”
    - password: “”

- table 생성 예시
![springboot](/images/springboot/springboot13-4.png)
    >**createStatement()**
    데이터베이스로 SQL 문을 보내기 위한 SQLServerStatement 개체를 만듭니다.
    
    - 위와 같이 connection객체에서 getMetaData를 통해서도 연결정보를 확인할 수 있다.
  
    

- H2 콘솔 사용하는 방법
    - spring-boot-devtools를 추가하거나...
    - spring.h2.console.enabled=true 만 추가.
    - /h2-console로 접속 (이 path도 바꿀 수 있음)
    ![springboot](/images/springboot/springboot13-3.png)

- Data Source뿐만 아니라 JdbcTemplate도 빈으로 등록됨으로 사용할 수 있다.
![springboot](/images/springboot/springboot13-5.png)
![springboot](/images/springboot/springboot13-6.png)
    - Jdbc api(connection등)를 사용하는것보다 JdbcTemplate를 사용하는것을 추천한다.
        - 보다 간결한 코드를 사용한다.
        - 보다 안전한다. (리소스 반납처리가 잘 되어있다.(try catch finally등))
        - 보다 가독성 좋은 에러메세지도 확인할 수 있다.
        
#### MySQL
- 지원하는 DBCP(DataBaseConnectionPool)
    - HikariCP (기본)
        - [기본적인 설정](https://github.com/brettwooldridge/HikariCP#frequently-used)
    - Tomcat CP
    - Commons DBCP2

>**JDBC와 DBCP**
Java에서 Database와 연결하기 위해선 JDBC를 필요로 하며, JDBC를 이용해 생성한 Connection을 효율적으로 활용하기 위해 Connection 객체를 관리하는 것을 DBCP

- 스프링 부트 DBCP 설정
    - spring.datasource.hikari.*
    ![springboot](/images/springboot/springboot13-7.png)
    - spring.datasource.tomcat.*
    - spring.datasource.dbcp2.*
    - DBCP 확인
    ![springboot](/images/springboot/springboot13-8.png)
    
    
- MySQL 커넥터 의존성 추가
```
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
```

- MySQL 실치 및 서버 실행 (도커사용)
    ```
    docker run -p 3306:3306 --name mysql_boot -e MYSQL_ROOT_PASSWORD=1 
    -e MYSQL_DATABASE=springboot -e MYSQL_USER=keesun -e MYSQL_PASSWORD=pass -d mysql
    ```
    - docker를 실행한다. (mysql 이미지가 없으면 다운받는다.)
        - 컨테이너 3306포트를 로컬호스트 3306포트에 연결
        - 컨테이너이름 = mysql_boot
        - MYSQL_ROOT 비밀번호 : 1
        - 사용자 : bong
        - 비밀번호 : pass
- MySQL bash로 실행
    ```
    docker exec -i -t mysql_boot bash
    ```

- MySQL 접속
    ```
    mysql -u root -p
    /*or*/
    mysql -u <username> -p
    ```
    - root로 접속 시 root 비밀번호(1), user로 접속 시 user비밀번호(pass)

- [MySQL 명령어 참고 블로그](https://doorbw.tistory.com/21)

- MySQL용 Datasource 설정
    - spring.datasource.url=jdbc:mysql://localhost:3306/springboot?useSSL=false
    - spring.datasource.username=keesun
    - spring.datasource.password=pass

- MySQL 라이센스 (GPL) 주의
    - 소스 코드 공개 의무 여부 확인
    - 유료
    - 대안
        - MySQL 대신 MariaDB 사용 검토
            - 무료지만 MariaDB 또한 GPL로 소스 코드 공개가 필요하다.
        - **PostgreSQL 사용이 가장 안전하다.**

>**오픈소스 라이센스**
GPL : 소스코드 공개 의무 O
MIT : 소스코드 공개 의무 X

#### PostgreSQL(포스트그레스)
- 의존성 추가
    ```
    <dependency>
        <groupId>org.postgresql</groupId>
        <artifactId>postgresql</artifactId>
    </dependency>
    ```
    
- PostgreSQL 설치 및 서버 실행 (docker)
    ```
    docker run -p 5432:5432 -e POSTGRES_PASSWORD=pass -e POSTGRES_USER=bong 
    -e POSTGRES_DB=springboot --name postgres_boot -d postgres
    ```
    - mysql과 같은 방식으로 진행한다.

- PostgreSQL bash로 실행
```
docker exec -i -t postgres_boot bash
```

- PostgreSQL 접속
```
/*유저를 postgres로 변경*/
su - postgres

/*DB접속*/
psql -d <DBname> -U <username>

/*password 를 쓰고 싶다면*/
psql -d <DBname> -U <username> -W

/*전체 데이터베이스 조회*/
\l or \list

/*전체 테이블 조회*/
\dt
```
    - DB접속이 안될 때 (role에러)
    ![springboot](/images/springboot/springboot13-9.png)
    
    ```
    psql -U <username> <DBname>
    ```
    ex) psql -U bong springboot
        - 맥북도 발생한다.. os문제는 아닌것 같음.

- PostgreSQL용 Datasource 설정
    - spring.datasource.url=jdbc:postgresql://localhost:5432/springboot
    - spring.datasource.username=bong
    - spring.datasource.password=pass

- Postgres 주의사항
    - user명칭을 사용할 수 없다.
    - user가 키워드로 사용된다.
    - org.postgresql.jdbc.PgConnection.createClob() is not yet implemented 경고
        - spring.jpa.properties.hibernate.jdbc.lob.non_contextual_creation=true
        프로퍼티에 추

- 인텔리제이 DataBase기능 활용
    - docker가 실행되고 있는 상태에서 진행
    - 우측의 DataBase 탭에서 progreSQL을 추가한다.
    ![springboot](/images/springboot/springboot13-10.png)
    - DB이름 , user, password 설정
    ![springboot](/images/springboot/springboot13-11.png)
    - 테이블 더블클릭으로 내용 확인 가능하며 쿼리문도 작성하여 확인할 수 있다.
    ![springboot](/images/springboot/springboot13-12.png)
    
#### 스프링 데이터 JPA
- ORM(Object-Relational Mapping)과 JPA (Java Persistence API)
    - 객체와 릴레이션을 맵핑할 때 발생하는 개념적 불일치를 해결하는 프레임워크
    - http://hibernate.org/orm/what-is-an-orm/
    - JPA: ORM을 위한 자바 (EE) 표준

- 스프링 데이터 JPA
    - JPA 표준스펙을 아주 쉽게 사용할 수 있게 추상화 시켜놓은것.
    - 구현체는 hibernate를 사용
    - Repository 빈 자동 생성
    - 쿼리 메소드 자동 구현
    - @EnableJpaRepositories (스프링 부트가 자동으로 설정 해줌.)
    - SDJ -> JPA -> Hibernate -> Datasource
    - 기존 jdbc를 모두 활용할 수 있다. (의존성을 보면 확인이 가능하다.)
    ![springboot](/images/springboot/springboot13-13.png)

#### JPA 연동
- 스프링 데이터 JPA 의존성 추가
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
```
- 스프링 데이터 JPA 사용하기
    - @Entity 클래스 만들기
    ![springboot](/images/springboot/springboot13-14.png)
        - @GernerateValue 자동으로 값을 생성
        - lombok을 사용하면 보다 쉽게 만들 수 있다.
        ![springboot](/images/springboot/springboot13-15.png)
        >**Lombok**
        자바에서 Model Object를 만들 때, getter/setter, Tostring등 반본적으로 사용되는 코드를 어노테이션을 통해 줄여주는 라이브러리
        참고 블로그 : [갓대희의 작은공간](https://goddaehee.tistory.com/95)
        
    - Repository 만들기
    ![springboot](/images/springboot/springboot13-15.png)
        - Interface
        - extends JpaRepository<entity type , id type>
    
- 스프링 데이터 리파지토리 테스트 만들기
    - 슬라이스 테스트를 할때는 인메모리 데이터베이가 반드시 필요하다.
        - H2 DB를 테스트 의존성에 추가하기
        ```
        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
            <scope>test</scope>
        </dependency>
        ```
    - @DataJpaTest (슬라이스 테스트) 작성
        - 레퍼지토리를 포함한 레퍼지토리와 관련된 빈들만 등록을 하여 테스트
        ![springboot](/images/springboot/springboot13-17.png)    
            - DataSource
            - JdbcTemplate
            - 해당 Repository
            - ...
        - 테스트 결과
        ![springboot](/images/springboot/springboot13-18.png)

- 어플리케이션 정상 작동을 위해서는 DataBase설정이 필요하다.
    - DB 드라이버 의존성 추가(PostgreSQL)
    - DataSource 설정
    - SpringBootTest 적용 시, 모든 빈들이 등록됨으로 프로퍼티값들도 등록된다.
    (즉, postgreSQL이 적용된다.)
    ![springboot](/images/springboot/springboot13-19.png)
        - 테스트 DB가 하나 필요하게 됨으로 추천하지 않는다.

- JPA 활용
    - 기본 JPA api
    ![springboot](/images/springboot/springboot13-20.png)
    - Repostiry에 원하는 메소드 추가 후 활용
    ![springboot](/images/springboot/springboot13-22.png)
    - 직접 SQL쿼리문을 사용하고 싶을 때
    ![springboot](/images/springboot/springboot13-23.png)
    - Optional 사용
    ![springboot](/images/springboot/springboot13-24.png)
    
#### 데이터베이스 초기화
- 스키마 자동생성
     - 테스트 코드에선 스키마가 자동 생성된다.(embeded DB사용)
     ![springboot](/images/springboot/springboot13-26.png)
     - 실제로 구동했을 때, 스키마에 생성되지 않는다.
     ![springboot](/images/springboot/springboot13-27.png)

- JPA를 사용한 데이터베이스 초기화
    - spring.jpa.hibernate.ddl-auto
        ![springboot](/images/springboot/springboot13-28.png)
        - update : 수정 및 추가된것만 수정된다.
            - 데이터가 남기때문에 자주 사용
            - **컬럼명이 변경되는것은 알지못함으로 필드명은 변경하면 안된다.**
        - create : 있는것을 지우고 만든다.
        - create-drop : 생성하고 애플리케이션 종료시 지워준다.
    
    - spring.jpa.generate-ddl=true
        - 기본값이 false이기 때문에 설정해주지 않으면 상기사항이 적용되지 않는다.
    
    - 운영 DB의 경우 validate 설정이 보다 안정적이다.
        - spring.jpa.hibernate.ddl-auto=validate
        spring.jpa.generate-ddl=false
        - 릴레이션이 잘 설정되어있는지 확인해준다.
            - 클래스와 잘 매핑되있으면 오류없이 실행된다.
            - 매핑이 되지않으면 오류발생
            ![springboot](/images/springboot/springboot13-29.png)
            - 따라서 DB수정이 필요 시, 업데이트 환경으로 진행
            ![springboot](/images/springboot/springboot13-30.png)

- SQL 스크립트를 사용한 데이터베이스 초기화
    - 테스트 코드를 돌려 쿼리문을 사용하면 간편하다.
        ![springboot](/images/springboot/springboot13-25.png)
        - jpa관 설정들은 테스트 코드에도 적용됨으로 주의할 것.
            
    - schema.sql 또는 schema-${platform}.sql
        - empty값이 아니어야 한다.
    - data.sql 또는 data-${platform}.sql
        - schema.sql 먼저 호출된다.
    - ${platform} 값은 spring.datasource.platform 으로 설정 가능.
        - schema.sql과 공존하면 우선순위는 schema.sql
        - schema.sql과 공존할 때, 둘 다 empty값이 아니어야한다.
 
#### 데이터베이스 마이그레이션
- [Flyway](https://docs.spring.io/spring-boot/docs/2.0.3.RELEASE/reference/htmlsingle/#howto-execute-flyway-database-migrations-on-startup)와 Liquibase가 대표적인데, Flyway를 사용하겠습니다
- 의존성 추가
    ```
    <dependency>
        <groupId>org.flywaydb</groupId>
        <artifactId>flyway-core</artifactId>
    </dependency>
    ```

- 마이그레이션 디렉토리
    - db/migration 또는 db/migration/{vendor}
        - 파일들의 쿼리문 문법을 잘 지켜야한다. (;까지)
        ![springboot](/images/springboot/springboot13-32.png)
        
    - spring.flyway.locations로 변경 가능

- 마이그레이션 파일 이름
    ![springboot](/images/springboot/springboot13-33.png)
    - V숫자__이름.sql
    - **V는 꼭 대문자로.**
    - 숫자는 순차적으로 (타임스탬프 권장)
    - **숫자와 이름 사이에 언더바 두 개.**
    - 이름은 가능한 서술적으로.

- **적용된 스크립트 파일(sql)은 절대로 건들면 안된다.**
    - 수정사항 발생 시, 새로운 파일을 작성한다.
        - 변경사항
        ![springboot](/images/springboot/springboot13-34.png)
        - 새로운 스크립트 작성
        ![springboot](/images/springboot/springboot13-35.png)

#### Redis
- 캐시, 메시지 브로커, 키/밸류 스토어 등으로 사용 가능.
- spring-boot-data-redis 의존성 추가

- Redis 설치 및 실행 (도커)
    - docker run -p 6379:6379 --name redis_boot -d redis
    - docker exec -i -t redis_boot redis-cli
 
-  스프링 데이터 Redis
    - https://projects.spring.io/spring-data-redis/
    - StringRedisTemplate 또는 RedisTemplate
    ![springboot](/images/springboot/springboot13-36.png)
    - extends CrudRepository
    ![springboot](/images/springboot/springboot13-37.png)
 
 - Redis 주요 커맨드
    ![springboot](/images/springboot/springboot13-38.png)
    - https://redis.io/commands
    - keys *
    - get {key}
    - hgetall {key}
    - hget {key} {column}   

- 커스터마이징
    - spring.redis.*
        - url : localhost가아니면 설정이 필요하다.
        - port : 기본포트번호(6379) 사용안하면 설정이 필요하다.

#### MongoDB
- MongoDB는 JSON 기반의 도큐먼트 데이터베이스
    - 스키마가 없다
- Webflux를 사용하는 경우, MongoDB reactive를 사용하여 reactive한 repository를 만들 수 있다.

- 참고 : ApplicationRunner를 만드는 새로운 방법
    - ApplicationRunner를 리턴하는 메소드를 빈으로 등록한다.
    ![springboot](/images/springboot/springboot13-39.png)
    - 람다를 사용하면 보다 간결하게 사용가능
    ![springboot](/images/springboot/springboot13-40.png)

- collections 생성
    ![springboot](/images/springboot/springboot13-41.png)
    - RDB(Relation Data Base)의 테이블로 보면 된다.
    
- MongoDB 설치 및 실행 (도커)
    ![springboot](/images/springboot/springboot13-42.png)
    - docker run -p 27017:27017 --name mongo_boot -d mongo
    - docker exec -i -t mongo_boot bash
    - mongo
    
- 스프링 데이터 몽고DB
    - MongoTemplate
        ![springboot](/images/springboot/springboot13-43.png)
    - MongoRepository
        ![springboot](/images/springboot/springboot13-44.png)
    - 내장형 MongoDB (테스트용)
        - 의존성 추가
        ```
        <dependency>
            <groupId>de.flapdoodle.embed</groupId>
            <artifactId>de.flapdoodle.embed.mongo</artifactId>
            <scope>test</scope>
        </dependency>
        ```
        - @DataMongoTest
            ![springboot](/images/springboot/springboot13-46.png)
        - 운영용 MongoDB에 영향을 주지 않는다.
            - 운영용 DB를 포함하지만, test에서 만든 데이터는 운영 DB에 추가 되지않는다.
            ![springboot](/images/springboot/springboot13-45.png)

#### Neo4j
- Neo4j는 노드간의 연관 관계를 표현하는데 빠르고 다양한 기능을 제공하는 그래프 데이터베이스
ex) 친구의친구의친구를 찾는다거나.. 친구의 세번째링크에 관련된 모든 친구를 불러온다거나..

- 의존성 추가
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-neo4j</artifactId>
</dependency>
```

- Neo4j 설치 및 실행 (도커)
    - docker run -p 7474:7474 -p 7687:7687 -d --name noe4j_boot neo4j
        - 포트를 두개 설정해주어야 한다.
            - HTTP(앞) → 서버에서 사용한다.
            - 볼트 프로토 (뒤)
    - http://localhost:7474/browser
        - 바로 접속할 수 있다.
        - 기본 패스워드 Neo4j 비밀번호를 바꿔야한다.
            - 기본 패스워드가 변경되었기 때문에 설정해줘야한다.
            ![springboot](/images/springboot/springboot13-48.png)

- NodeEntity 생성
    ![springboot](/images/springboot/springboot13-49.png)
    - id 는 숫자값만 줄수 있다.

- 스프링 데이터 Neo4J
    - 최신버전으로 오면서 등록되던 빈들이 사라졌다.
        - Neo4jTemplate
        - Neo4jOpertion
    - **SessionFactory**
        ![springboot](/images/springboot/springboot13-50.png)
    - Neo4jRepository
        ![springboot](/images/springboot/springboot13-55.png)
    

- 관계보기
    - NodeEntity 생성 및 데이터입력 (이전과정에서 진행하였다.)
    - 브라우저에서 확인 가능하다.
    ![springboot](/images/springboot/springboot13-51.png)
    - Relationship을 가질 NodeEntity를 하나 더 만든다.
    ![springboot](/images/springboot/springboot13-47.png)
    - Relationship 설정 (Account가 Role을 가지도록 설정하였다.)
    ![springboot](/images/springboot/springboot13-53.png)
    - 롤 부여 및 실행
    ![springboot](/images/springboot/springboot13-52.png)
    - 브라우저에서 확인
    ![springboot](/images/springboot/springboot13-54.png)
     
       