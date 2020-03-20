---
title: ORM07 - 고급매핑(상속관계 매핑, MappedSuperclass)
date: 2019-11-18 23:13:22
tags: JPA

---

# 자바 ORM 표준 JPA 프로그래밍 - 기본편

## 고급매핑

### 상속관계 매핑
<!-- more -->
- 객체는 상속관계가 있지만, **관계형 데이터베이스는 상속 관계가 없다.**

- 슈퍼타입 서브타입 관계라는 모델링 기법이 객체의 상속과 유사하다.

- 상속관계 매핑 : 객체의 상속과 구조와 DB의 슈퍼타입 서브타입 관계를 매핑

- 슈퍼타입 서브타입 논리 모델을 실제 물리 모델로 구현하는 방법

  - 조인 전략 : 각각 테이블로 변환 

    ![ORM07-1](/images/jpa/ORM-JPA/ORM07-1.png)

    - 각 테이블을 모두 생성 
    - 슈퍼타입 테이블의 PK를 서브타입의 PK,FK로 둔다.
    - 해당 PK로 조인하여 조회
    - 슈퍼타입에 구분하기 윈한 구분자 컬럼을 둔다.

  - 단일 테이블 전략 : 통합 테이블로 변환

    ![ORM07-2](/images/jpa/ORM-JPA/ORM07-2.png)

    - 모든 테이블을 하나의 테이블로 구현하고 구분자 컬럼을 둔다.

  - 구현 클래스마다 테이블 전략 : 서브타입 테이블로 변환

    ![ORM07-3](/images/jpa/ORM-JPA/ORM07-3.png)

    - 모든 테이블을 따로 구현한다.

#### 조인전략

```java
@Entity
@Inheritance(strategy = InheritanceType.JOINED)
@DiscriminatorColumn
public class Item {

    @Id @GeneratedValue
    private Long id;

    private String name;
    private int price;
}

@Entity
public class Book extends Item {
    private String author;
    private String isbn;
}

@Entity
public class Album extends Item {
    private String artist;
}

@Entity
public class Movie extends Item {
    private String director;
    private String actor;
}
```

- `@DiscriminatorColumn` 을 추가 하지 않으면 구분자 컬럼은 생략된다.

  - name값은 기본은 엔티티 이름이다.

  - 넣어주는게 DB에서 확인하기 좋다.

  - 구분자 컬럼 value값에 들어갈 값을 엔티티명이 아닌 다른값으로 변경하려면 `@DiscriminatorValue("A")` 처럼 사용할 수 있다.

    ```java
    @Entity
    @DiscriminatorValue("A")
    public class Album extends Item {
        private String artist;
    }
    ```

- 장점

  - 테이블이 정규화 된다.
  - 외래 키 참조 무결성 제약조건 활용가능
  - 저장공간 효율화
  - JPA와 가장 유사 모델

- 단점

  - 조회시 조인을 많이 사용 -> 성능 저하
  - 조회쿼리가 복잡하다.
  - 데이터 저장 시, INSERT 2회 호출

#### 단일 테이블 전략

```java
@Entity
@Inheritance(strategy = InheritanceType.SINGLE_TABLE)
@DiscriminatorColumn
public class Item {

    @Id @GeneratedValue
    private Long id;

    private String name;
    private int price;
}
```

- 기본전략(starategy를 적어주지 않았을 때는 이옵션이 선택된다.)
- 장점
  - 조인이 필요 없어 일반적으로 조회 성능이 빠름
  - 조회쿼리가 단순하다.
- 단점
  - 자식 엔티티가 매핑한 컬럼은 모두 null 허용이 필요하다.
  - 단일 테이블에 모든것을 저장하므로 테이블이 커질 수 있다.
    **(상황에 따라 조회 성능이 느려질 수 있다.)**
- `@DiscriminatorColumn` 가 없어도 hibernate가 DType(구분자 컬럼)을 생성해준다.

#### 구현 클래스마다 테이블 전략

- 구분자컬럼을 사용할 필요가 없다. (각각 생성되기 때문에)
- 장점
  - 서브 타입을 명확하게 구분해서 처리할 대 효과적이다.
  - not null 제약조건 사용가능
- 단점
  - 여러 자식 테이블을 함께 조회할 때 성능이 느림(UNION SQL 필요)
  - 자식 테이블을 통합해서 쿼리하기 어려움
  - **이 전략은 데이터베이스 설계와 ORM 전문가 둘 다 추천 X**

**조인 전략과 단일 테이블 전략 사이에서 고민을 하여 결정하자.** 

- 비지니스 로직적으로 중요하거나 데이터가 많은 경우, 조인전략
- 비교적 단순한경우 단일 테이블 전략

<br><br>

### @MappedSuperClass

![ORM07-4](/images/jpa/ORM-JPA/ORM07-4.png)

- 공통 매핑 정보가 필요할 때 사용(id, name등의 항상 필요한 컬럼)
- 상속관계 매핑 X
- 엔티티 X, 테이블과 매핑X
- **부모 클래스를 상속받는 자식 클래스에 매핑 정보만 제공**
- **조회, 검색 불가**
  - `em.find(BaseEntity)` 와 같은 검색 불가
- 직접 생성해서 사용할 일이 없으므로 **추상 클래스 권장**
- 테이블과 관계 없고, 단순히 엔티티가 공통으로 사용되는 매핑 정보를 모으는 역할
- 주로 등록일, 수정일, 등록자, 수정자 같은 전체 엔티티에서 공통으로 적용하는 정보를 모을 때 사용
- @Entity 클래스는 엔티티나 @MappedSuperclass로 지정된 클래스만 상속가능

<br><br>