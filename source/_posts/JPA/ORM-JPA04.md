---
title: ORM04 - 엔티티 매핑(객체와 테이블 매핑(@Table), 데이터베이스 스키마 자동생성, 필드와 컬럼 매핑(@Column), 기본 키 매핑(@Id, @GenerateValue))
date: 2019-10-11 17:44:27
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

## 엔티티 매핑
<!-- more -->
#### 객체와 테이블 매핑

#### @Entity

- JPA가 관리, 엔티티라고 한다.
- **기본 생성자 필수**(파라미터가 없는 public 또는 protected 생성자)
- final, enum, interface, inner 클래스 사옹불가
- 저장할 필드에 final 사용 불가

#### @Entity 속성 정리

- name
  - 기본값은 클래스의 이름
  - JPA에서 사용할 엔티티 이름을 지정
  - 같은 클래스 이름이 없으면 가급적 기본값을 사용하는것을 추천한다.

#### @Table

- 엔티티와 매핑할 테이블 지정
- name
  - 매핑할 테이블 이름
  - 기본값은 엔티티 이름을 사용
- catalog
  - 데이터베이스 catalog 매핑
- schema
  - 데이터베이스 schema 매핑
- uniqueConstraints
  - DDL생성 시에 유니크 제약 조건 생성

<br><br>

### 데이터베이스 스키마 자동 생성

- DDL을 애플리케이션 실행 시점에 자동 생성
- 테이블 중심 -> 객체 중심
- 데이터베이스 방언을 활용하여 데이터베이스에 맞는 적절한 DDL 생성
- **이렇게 생성한 DDL은 개발 장비에서만 사용**
  - 생성된 DDL은 운영서버에서는 사용하지 않거나, 적절히 다듬은 후 사용

#### hibernate.hbm2ddl.auto 설정

- create : 기존 테이블 삭제 후 다시 생성 (DROP + CREATE)
- create-drop : create와 같으나 종료시점에 테이블 DROP
- update : 변경된 부분만 반영(운영 DB에는 사용하면 안됨)
  - 기존에 있던 컬럼이 지워지는 경우는 아무런 일이 일어나지 않는다.
- validate : 엔티티와 테이블이 정상 매핑 되었는지만 확인
- none : 사용하지 않음
  - 사실 없음
  - 매칭되는게 없으나 관례적으로 none이라고 적는다.

#### 주의

- **운영 장비에서는 절대 create, create-drop, update는 사용하면 안된다.**
- 개발 초기 단계는 create or update
- 테스트 서버 update or validate
- 스테이징, 운영서버는 validate or none

#### DDL 생성 기능

- 제약조건 추가
  - `@Column(nullable = false, length = 10)`
- 유니크 제약조건 추가
  - `@Table(uniqueConstraints = {@UniqueContstranin(name = "NAME_AGE_UNIQUE", columnNames = {"NAME", "AGE" })})`
- **DDL 생성 기능은 DDL을 자동 생성할 때만 사용되고, JPA의 실행 로직에는 영향을 주지 않는다.**

<br><br>

### 필드와 컬럼 매핑

| 어노테이션  | 설명                                        |
| ----------- | ------------------------------------------- |
| @Column     | 컬럼매핑                                    |
| @Temporal   | 날짜 타입 매핑                              |
| @Enumerated | enum 타입 매핑                              |
| @Lob        | BLOB, CLOB 매핑                             |
| @Transient  | 특정 필드를 컬럼에 매핑하지 않음(매핑 무시) |

#### @Column

| 속성                  | 설명                                                         | 기본값                        |
| --------------------- | ------------------------------------------------------------ | ----------------------------- |
| name                  | 필드와 매핑할 테이블의 컬럼 이름                             | 객체의 필드 이름              |
| insertable, updatable | 등록, 변경 가능여부                                          | TRUE                          |
| nullalbe(DDL)         | null값 허용여부. <br />false로 설정 시 DDL 생성할 때 not null 제약 조건이 붙는다. |                               |
| unique(DDL)           | @Table의 uniqueConstraints와 같지만 한 컬럼에 간단한 유니크 제약조건을 줄 때 사용한다. | 255                           |
| length(DDL)           | 문자 길이 제약조건, String 타입에만 사용한다.                | 255                           |
| precision, scale(DDL) | BigDecimal 타입에서 사용한다.(BigInteger도 사용할 수있다.)<br />prevision은 소수점을 포함한 전체 자릿수<br />sacle은 소수의 자릿수<br />double, float타입에는 적용되지 않는다.<br />아주 큰 숫자나 정밀한 소수를 다룰때 사용. | precision = 19<br />sclae = 2 |

- unique의 경우, Constraints 이름은 줄 수 없다. 

  ![ORM03-21](/images/jpa/ORM-JPA/ORM03-21.png)

- Constarints 이름을 주고 싶다면 @Table(uniqueConstraints)를 사용하자.

#### @Enumerated

- 자바 enum 타입을 매핑할 때 사용
- **ORDINAL 사용 X**
  - 추가 되거나, 수정 발생 시에 문제가 발생될 수 있다.

| 속성  | 설명                                                         | 기본값            |
| ----- | ------------------------------------------------------------ | ----------------- |
| value | EnumType.ORDINAL : enum 순서를 데이터 베이스에 저장<br />EnumType.STRING : enum 이름을 데이터 베이스에 저장 | Enumtype. ORDINAL |

#### @Temporal

- 날짜 타입을 매핑할 때 사용
- 자바8에 **LocalDateTime**, **LocalDate** 가 생겼기 때문에, 최근에는 거의 사용하지 않는다.	![ORM03-23](/images/jpa/ORM-JPA/ORM03-23.png)

  - 최신 하이버네이트 버전에서 지원하고 있다.

#### @Lob

- 데이터베이스 BLOB, CLOB 타입과 매핑
- 지정할 수 있는 속성이 없다.
- 매핑하는 필드 타입이 문자면 CLOB, 나머지는 BLOB 매핑
  - CLOB : String, char[], java.sql.CLOB
  - BLOB : byte[], java.sql.BLOB

> **CLOB**
>
> - 사이즈가 큰 데이터를 외부 파일로 저장하기 위한 데이터 타입
> - a문자열 데이터를 DB외부에 저장하기 위한 타입이다.
> - 데이터의 최대 길이는 외부 저장소에서 생성 가능한 파일 크기이다.
> - SQL문에서 문자열 타입으로 입출력 값을 표현한다.
> - CHAR, VARCHAR, NCHAR, NCHAR VARYING 타입과 호환
> - 명시적 타입 변환만 허용
> - 데이터 길이가 서로 다른 경우 최대 길이가 작은 타입에 맞추어 절삭된다.
>
> **BLOB**
>
> - 바이너리 데이터를 DB외부에 저장하기 위한 타입이다.
> - BLOB 데이ㅓㅌ의 최대 길이는 외부 저장소에서 생서 가능한 파일크기이다.
> - SQL문에서 비트열 타입으로 입출력 값을 표현한다.
> - BIT, BIT VARYING 타입과 호환
> - 명시적 타입 변환만 허용
> - 데이터 길이가 서로 다른 경우 최대 길이가 작은 타입에 맞추어 절삭된다.
> - BLOB타입값을 바이너리 값으로 변환하는 경우, 변환된 데이터는 초대 1GB를 넘을 수 없다.
> - 반대로 바이너리를 BLOB타입으로 변환하는 경우, 변환된 데이터는 BLOB저장소에서 제공하는 파일 크기를 넘을 수 없다.

<br><br>

### 기본 키 매핑

- 직접 할 : **@Id만 사용**
- 자동생성 : @GenerateValue
  - IDENTITY 데이터베이스에 위임, (MYSQL의 AUTO_INCREMENT)
  - SEQUENCE : 데이터베이스 시퀀스 오브젝트 사용, ORACLE
    - @SequenceGenerator 필요
  - TABLE : 키생성용 테이블 사용, 모든 DB에서 사용
    - @TableGenerator 필요
  - AUTO : 방언에 따라 자동 지정, 현재 기본값
    - 예를 들어 오라클이면 sequence 생성...
    - **변경사항이 있을 수 있으니 확인하고 사용하는 것을 추천.**

#### IDENTITY 전략

```java
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private String id;
```

- 기본 키생성을 데이터 베이스에 위임

- 주로 MY_SQL, PostgreSQL, SQL server, DB2에서 사용

- JPA는 보통 트랜잭션 커밋 시점에 INSERT SQL 실행

- AUTO_INCREMENT는 데이터 베이스에 INSERT SQL을 실행 한 이후에 ID값을 알 수 있음

- **IDENTITY 전략은 `em.persist(entity);` 시점에 즉시 INSERT SQL 실행하고 DB에서 식별자 조회**

  - DB에 아이디값이 null로 날라왔을 때, DB가 ID를 생성한다.
  - 생성한 ID값을 리턴받아서 영속성 컨텍스트에 넣어준다.

  ![ORM03-20](/images/jpa/ORM-JPA/ORM03-20.png)

#### SEQUENCE 전략

```java
@Id
@GeneratedValue(strategy = GenerationType.SEQUENCE)
private Long id;
```

- 숫자 타입 사용해야한다.

  - Integer를 쓴다고 해서 성능적 차이는 미비하다. Long을 쓰자.

- Sequence를 DB에 요청해서 먼저 받고 영속성 컨텍스트에 저장 -> 트랜잭션 commit 시점에 쿼리를 호출

  ![ORM03-19](/images/jpa/ORM-JPA/ORM03-19.png)

  - DB와 너무 많은 커넥션을 맺는것 아닌가..? 성능에 문제가 있지 않나?

    - **allocationSize만큼 DB의 시퀀스값으 올려두고 하나씩 사용한다.** (밑에 예는 allocationSize = 2)

      ![ORM03-24](/images/jpa/ORM-JPA/ORM03-24.png)

      - 첫번째 시퀀스 1을 더미처럼 호출한 후, 시퀀스를 2증가시킨 3까지 증가시키는 콜을 한다. (사용값 : 1, DB 시퀀스 현재값 : 3)
      - 2번째 엔티티를 persist할 때, 미리 증가시켰던 시퀀스 범위 안으로 시퀀스 2를 사용한다. (사용값 : 2, DB 시퀀스 현재값 : 3)
      - 3번째 엔티티를 persist할 때, 미리 증가시켰던 시퀀스 범위 안으로 시퀀스 3를 사용한다. (사용값 : 3, DB 시퀀스 현재값 : 3)
      - 4번째 엔티티를 persist할 때, 시퀀스를 2증가 시키는 콜을한다. (사용값 : 4, DB 시퀀스 현재값 : 5) 

    - 여러 WAS가 있어도 동시성 문제없이 다양한 문제를 해결해준다.

- Table마다 Sequence를 가지고 시픙면 설정을 해주면 된다.

  ```java
  @Entity
  @SequenceGenerator(
          name = "MEMBER_SEQ_GENERATOR",
          sequenceName = "MEMBER_SEQ",
          initialValue = 1,
          allocationSize = 1
  )
  public class Member {
  
      @Id
      @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "MEMBER_SEQ")
      private Long id;
  ```

- @SequenceGenerator 속성

| 속성            | 설명                              | 기본값             |
| --------------- | --------------------------------- | ------------------ |
| name            | 코드에서 사용할 이름              | **필수**           |
| sequenceName    | 데이터베이스에 매핑할 시퀀스 이름 | hibernate_sequence |
| initialValue    | 초기값                            | 1                  |
| allocationSize  | 증가값                            | **50**             |
| catalog, schema | 데이터베이스 catalog, schema 이름 |                    |

#### TABLE 전략

```java
@Entity
@TableGenerator(
        name = "MEMBER_SEQ_GENERATOR",
        table = "MY_SEQUENCES",
        pkColumnName = "MEMBER_SEQ", allocationSize = 1)
public class Member {

    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "MEMBER_SEQ_GENERATOR")
    private Long id;
```

- 키 생성 전용 테이블을 하나 만들어서 데이터베이스 시퀀스를 흉내내는 전략

- @TableGenerator가 필요

- 장점 : 모든 데이터베이스에 적용 가능

- 단점 : 성능;

- pikColumName 컬럼 이름

  ![ORM03-18](/images/jpa/ORM-JPA/ORM03-18.png)

- @TableGenerator 속성

| 속성 | 설명 | 기본값 |
|---|---|---|
| name                   | 식별자 생성기 이름                        | 필수                |
| table                  | 키생성 테이블명                           | hibernate_sequences |
| pkColumnName           | 시퀀스 컬럼명                             | sequence_name       |
| valueColumnNa          | 시퀀스 값 컬럼명                          | next_val            |
| pkColumnValue          | 키로 사용할 값 이름                       | 엔티티 이름         |
| initialValue           | 초기 값, 마지막으로 생성된 값이 기준이다. | 0                   |
| allocationSize         | 증가값(sequence전략과 동일)               | **50**              |
| catalog, schema        | 데이터베이스 catalog, schema 이름         |                     |
| uniqueConstraints(DDL) | 유니크 제약조건                           |                     |

#### 권장하는 식별자 전략

- 기본 키 제약 조건
  - null 아님
  - 유일
  - **변하면 안된다.**
- 미래까지 이 조건을 만족하는 자연키(주민등록번호, 전호번호 등)는 찾기 어렵다. 
  비지니스와 상관없는 대리키를 사용하자.
- 권장 : Long형 + 대체키 + 키 생성전략 사용

<br><br>