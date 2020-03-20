---
title: 스프링 데이터 JPA01 - 관계형 데이터 베이스와 자바, ORM
date: 2019-04-29 15:19:45
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
스프링 웹 MVC와 함께 JPA 강좌를 수강하고 있습니다.
JPA는 Java Persistence API ORM(Object Relational Mapping)을 위한 표준 기술입니다.
ORM이란 RDB 테이블을 객체지향적으로 사용하기 위한 기술을 말합니다.
모든 예제는 [github](https://github.com/cyr9210/springJPA-study)에 올리고 있습니다.

---
### 관계형 데이터 베이스와 자바
- 관계형 데이터 베이스
    - 현재 가장 많이 사용되고 있는 데이터베이스의 한 종류
    - 테이블(table)로 이루어져 있으며, 이 테이블은 키(key)와 값(value)의 관계를 나타낸다.
    - 데이터의 종속성을 관계(relationship)로 표현하는 것이 관계형 데이터베이스의 특징
    
- JDBC : 데이터 베이스와 자바의 연결고리
    ![jpa](/images/jpa/jpa01-1.png)
    - 어플리케이션에 데이터를 영속화(persistence)하기 위함

#### 기본 JDBC를 사용한 프로젝트 예제
- 메이븐 프로젝트 생성
- 의존성 추가
    ```
    <dependency>
        <groupId>org.postgresql</groupId>
        <artifactId>postgresql</artifactId>
        <version>42.2.5</version>
    </dependency>
    ```

- 커넥션 생성
    ```
    public class Application {
        public static void main(String[] args) throws SQLException {
            String url = "jdbc:postgresql://localhost:5432/springdata";
            String username = "bong";
            String password = "pass";
    
            try (Connection connection = DriverManager.getConnection(url, username, password)){
    
            }
        }
    }
    ```
    - try with resource : 자바7 이부터 사용할 수 있는 문법으로, try 문 사용시 ()안의 리소스를 정리해준다. (close해줌)
        - maven 프로젝트로 생성 시, 자바 5버전으로 생성되었으 확률이 크다.
        - 오류 발생시 project Structure에서 자바7버전 이후 버전으로 변경해준다.
        - 그래도 오류가 발생한다면.. pom.xml에서 자바 버전을 설정해준다.([참고](https://stackoverflow.com/questions/29888592/errorjava-javactask-source-release-8-requires-target-release-1-8))
            ```
            <build>
                <plugins>
                    <plugin>
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-compiler-plugin</artifactId>
                        <configuration>
                            <source>1.8</source>
                            <target>1.8</target>
                        </configuration>
                    </plugin>
                </plugins>
            </build>
            ```

- sql 작성 및 커넥션 사용
    ![jpa](/images/jpa/jpa01-2.png)

- 참고 : [postgresql 사용법](https://cyr9210.github.io/2019/04/15/Spring/springboot13/#PostgreSQL-%ED%8F%AC%EC%8A%A4%ED%8A%B8%EA%B7%B8%EB%A0%88%EC%8A%A4)  

#### 무엇이 문제인가?
- SQL을 실행하는 비용이 비싸다.
- SQL이 데이터베이스 마다 다르다.
- 스키마를 바꿨더니 코드가 너무 많이 바뀌네...
- 반복적인 코드가 너무 많아.
- 당장은 필요가 없는데 언제 쓸 줄 모르니까 미리 다 읽어와야 하나...
<br><br>

### ORM(Object-Relation Mapping)
- ORM은 애플리케이션의 클래스와 SQL 데이터베이스의 테이블 사이의 **맵핑 정보를 기술한 메타데이터를 사용**하여, 자바 애플리케이션의 객체를 SQL 데이터베이스의 테이블에 **자동으로 (또 보다 비침투적으로) 영속화** 해주는 기술이다.
#### JDBC와 도메인 모델 사용의 차이점
- JDBC 사용
    ```
    try(Connection connection = DriverManager.getConnection(url, username, password)) {
        System.out.println("Connection created: " + connection);
        String sql = "INSERT INTO ACCOUNT VALUES(1, 'keesun', 'pass');";
        
        try(PreparedStatement statement = connection.prepareStatement(sql)){
        statement.execute();
        }
    }
    ```

- 도메인 모델 사용
    ```
    Account account = new Account("bong", "pass");
    accountRepository.save(account);
    ```
    
- JDBC 대신 도메인 모델 사용하려는 이유
    - 객체 지향 프로그래밍의 장점을 활용하기 좋으니까.
    - 각종 디자인 패턴
    - 코드 재사용
    - 비즈니스 로직 구현 및 테스트 편함(코드 간결)
    
도메인 모델을 사용하기 위해서 ORM툴인 JPA를 쓴다고 할 수 있다.

#### ORM툴 사용의 장단점
- 장점
    - 생산성
    - 유지보수성
    - 성능
        - 하나의 예를 들면.. Hibernate는 객체와 테이블에 들어있는 데이터 사이의 캐쉬가 존재한다.
            - 불필요한 쿼리를 날리지 않는다.
            - 예).. 한 트랜잭션안에서 하나의 데이터에 대하여 하나의 트랜잭션에서 값을 여러번 변경했을 때, 마지막 값이 초기값과의 변경사항을 체크하고 쿼리를 날린다.  
    - 밴더 독립성
        - DB의 종류에 따라서 변경될 코드가 없다.(방언(diarect)만 설정)
- 단점
    - 학습비용
        - sql을 포함한 학습이 반드시 필요하다.
        
### ORM: 패러다임 불일치
객체를 릴레이션에 맵핑하려니 발생하는 문제(ORM으로 해결이 가능하다.)

#### 밀도(Granularity) 문제
- 객체
    - 다양한 크기의 객체를 만들 수 있음.
    - 커스텀한 타입 만들기 쉬움.
- 릴레이션
    - 테이블
    - 기본 데이터 타입 (UDT는 비추)

#### 서브타입(Subtype) 문제
- 객체
    - 상속 구조 만들기 쉬움
    - 다형성
- 릴레이션
    - 테이블 상속이라는게 없음
    - 상속 기능을 구현했다 하더라도 표준 기술이 아님
    - 다형적인 관계를 표현할 방법이 없음
    
#### 식별성(Identity) 문제
- 객체
    - 레퍼런스 동일성 (==)
    - 인스턴스 동일성 (equals() 메소드)
- 릴레이션
    - 주키 (primary key)

#### 관계(Association) 문제
- 객체
    - 객체 레퍼런스로 관계 표현
    - 근본적으로 ‘방향'이 존재 한다.
    - 다대다 관계를 가질 수 있음
- 릴레이션
    - 외래키(foreign key)로 관계 표현
    - ‘방향'이라는 의미가 없음. (그냥 Join으로아무거나 묶을 수 있음.)
    - 태생적으로 다대다 관계를 못만들고, 조인 테이블 또는 링크 테이블을 사용해서 두개의 1대다 관계로 풀어야 함
    
#### 데이터 네비게이션(Navigation)의 문제
- 객체
    - 레퍼런스를 이용해서 다른 객체로 이동 가능
    - 콜렉션을 순회할 수도 있음.
- 릴레이션
    - 하지만 그런 방식은 릴레이션에서 데이터를 조회하는데 있어서 매우 비효율적이다.
    - 데이터베이스에 요청을 적게 할 수록 성능이 좋다.(따라서 Join을 쓴다.)
    - 하지만, 너무 많이 한번에 가져오려고 해도 문제다.
    - 그렇다고 lazy loading을 하자니 그것도 문제다. (n+1 select)
<br><br>
