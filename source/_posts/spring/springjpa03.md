---
title: 스프링 데이터 JPA03 - 엔티티 맵핑, Value 타입 맵핑, 관계 맵핑
date: 2019-04-30 16:24:09
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
### 엔티티 맵핑
- xml, @어노테이션으로 맵핑하는 방법 2가지가 있다. 일반적으로 어노테이션을 활용하는 방법을 사용한다.

#### @Entity
- “엔티티”는 객체 세상에서 부르는 이름.
- 보통 클래스와 같은 이름을 사용하기 때문에 값을 변경하지 않음.
    - 기본값은 클래스 이름을 쓴다.
        - 변경하고 싶을 때는 @Entity(name="변경이름")
        - User라는 이름을 사용하지 못하는 DB가 있다. 그럴경우 변경해야한다.
- 엔티티의 이름은 JQL에서 쓰임.

#### @Table
- “릴레이션" 세상에서 부르는 이름.
- @Entity의 이름이 기본값.
- 테이블의 이름은 SQL에서 쓰임.

#### @Id
- 엔티티의 주키를 맵핑할 때 사용.
- 자바의 모든 primitive 타입(int, long..)과 그 랩퍼 타입(Integer, Long..)을 사용할 수 있음
    - Date랑 BigDecimal, BigInteger도 사용 가능.
- 복합키를 만드는 맵핑하는 방법도 있지만 그건 논외로..

#### @GeneratedValue
- 주키의 생성 방법을 맵핑하는 애노테이션
- 생성 전략과 생성기를 설정할 수 있다.
    - 기본 전략은 AUTO: 사용하는 DB에 따라 적절한 전략 선택
    - TABLE, SEQUENCE, IDENTITY 중 하나.
        - @GenerateValue(strategy=) 로 변경이 가능

#### @Column
- 멤버변수에는 @Column이 붙어있는거랑 마찬가지이다.
- 여러설정이 가능하다. 
    ![springjpa](/images/jpa/jpa03-1.png)
    - unique
    - nullable
    - length
    - columnDefinition
    - ...

#### @Temporal
![springjpa](/images/jpa/jpa03-2.png)
- 타입으로 날짜, 시간, 날짜+시간(TIMESTAMP) 설정가능
- 현재 JPA 2.1까지는 Date와 Calendar만 지원.
    - 커스텀한 컨버터를 등록하면 사용이 가능하다.

#### @Transient
- 컬럼으로 맵핑하고 싶지 않은 멤버 변수에 사용.

#### 쿼리문 확인하기
- application.properties
    - spring.jpa.show-sql=true
        - 쿼리문 보여주기
    - spring.jpa.properties.hibernate.format_sql=true
        - 가독성 높이기
    - 로거설정을 통해 value값을 확인하는 법도 있다.
<br><br>

### Value 타입 맵핑

#### 엔티티 타입과 Value 타입 구분
- 식별자가 있어야 하는가.
- 독립적으로 존재해야 하는가.

#### Value 타입 종류
- 기본 타입 (String, Date, Boolean, ...)
- Composite Value 타입
- Collection Value 타입
    - 기본 타입의 콜렉션
    - 컴포짓 타입의 콜렉션
- Composite Value 타입 맵핑
    - @Embeddable
    ![springjpa](/images/jpa/jpa03-3.png)
    - @Embedded
        ![springjpa](/images/jpa/jpa03-4.png)
        - @AttributeOverrides
        - @AttributeOverride
<br>

### 관계 맵핑
- 관계에는 항상 두 엔티티가 존재 합니다.
    - 둘 중 하나는 그 관계의 주인(owning)이고
    - 다른 쪽은 종속된(non-owning) 쪽입니다.
    - 해당 관계의 반대쪽 레퍼런스를 가지고 있는 쪽이 주인.

#### 단방향
- 관계를 정의한 쪽이 그 관계의 주인입니다.

- @ManyToOne
    ![springjpa](/images/jpa/jpa03-8.png)
    - 기본값은 FK 생성

- @OneToMany
    ![springjpa](/images/jpa/jpa03-10.png)
    - 기본값은 조인 테이블 생성
        - hibernate.ddl-auto=create로 되어있어도 조인테이블은 삭제되지 않는다.
        엔티티로 정의 된 테이블만 삭제
- ManyToOne 인지 OneToMany인지 헷갈린다면 필드객체를 보아라.
    ![springjpa](/images/jpa/jpa03-9.png)

#### 양방향
- FK 가지고 있는 쪽이 오너 따라서 기본값은 @ManyToOne 가지고 있는 쪽이 주인.
- 주인이 아닌쪽(@OneToMany쪽)에서 mappedBy 사용해서 관계를 맺고 있는 필드를 설정해야 합니다.
    ![springjpa](/images/jpa/jpa03-11.png)
- 주인한테 관계를 설정해야 DB에 반영이 됩니다.
    - 양쪽모두에 설정해주는것이 제일 좋다.
        - 이렇게 메소드를 만들어 쓰는것을 추천한다.
        ```
        public void add(Study study) {
            this.getStudies().add(study);
            study.setOwner(this);
        }
        ```
        - 사용
        ![springjpa](/images/jpa/jpa03-12.png)
        
    
    

    
#### issue
- JSON 변환 시, 무한루프
    ![springjpa](/images/jpa/jpa03-6.png)
    
- @Entity 와 @Data(lombok)
    ![springjpa](/images/jpa/jpa03-5.png)

- 양방향 관계를 사용하는 이유
    ![springjpa](/images/jpa/jpa03-7.png)
<br>

