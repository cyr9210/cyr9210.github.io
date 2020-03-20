---
title: ORM09 - 값 타입(값 타입, 임베디드 타입, 값 타입 컬렉션)
date: 2019-12-22 13:09:10
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

## 값 타입

### 기본값 타입
<!-- more -->
#### JPA의 데이터 타입분류

- 엔티티 타입
  - @Entity로 정의하는 객체
  - 데이터가 변해도 식별자로 지속해서 추적가능
- 값 타입
  - int, Integer, String처럼 단순히 값으로 사용하는 자바 기본 타입이나 객체
  - 식별자가 없고 값만 있으므로 추적 불가
#### 값 타입 분류

- 기본값 타입
  - 자바 기본 타입(int, double)
  - 래퍼 클래스(Integer, Long)
  - String
- 임베디드 타입
  - 복합 값 타입 (ex> x, y 좌표등의 값)
- 컬렉션 값 타입
  - 자바 컬렉션객체에 기본값이나 임베디드 타입을 넣는것.

#### 기본값 타입

- 예) String name, int age 등
- 생명주기를 엔티티에 의존
  - 회원이 삭제되면 이름, 나이 필드도 함께 삭제
- **값 타입은 공유하면 안된다.**
  - 회원 이름 변경 시, 다른 회원의 이름도 함께 변경되면 안된다.

> **자바의 기본 타입은 절대 공유가 안된다.**
> 자바의 기본타입(primitive type)은 공유되지 않는다.
> 기본타입은 항상 값을 복사한다.
> Integer같은 래퍼 클래스나 String같은 특수한 클래스는 공유 가능한 객체이지만 변경이 되지 않는다.

<br><br>

### 임베디드 타입

- 새로운 값 타입을 직접 정의할 수 있다.
- JPA는 임베디드 타입 이라고 한다.
- 주로 기본값 타입을 모아서 만들어서 복합 값 타입이라고도 한다.

#### 임베디드 타입을 어떤경우에 사용할까?

##### example

- 회원 엔티티는 이름, 근무 시작일, 근무 종료일, 주소도시, 주소번지, 주소 우편번호를 가진다.

  ![ORM09-1](/images/jpa/ORM-JPA/ORM09-1.png)

- 이러한 경우 근무기간(근무 시작일, 근무 종료일), 집주소(도시, 번지, 우편번호)로 그룹지어 관리할 수 있다.

  ![ORM09-2](/images/jpa/ORM-JPA/ORM09-2.png)

- 최종적으로 JPA는 아래와 같은 모습으로 관리하게 된다.

  ![ORM09-3](/images/jpa/ORM-JPA/ORM09-3.png)

#### 임베디드 타입 장점

- 재사용
- 높은 응집도
- **해당 값 타입만 사용하는 의미 있는 메소드를 만들 수 있다.**
  - 예) `WorkPeriod.isWork()` (현재 근무 상태를 체크)와 같이 의미 있는 메소드를 만들어 사용할 수 있다.
- 임베디드 타입을 포함한 모든 값 타입은, 값을 소유한 엔티티의 생명주기에 의존한다.

#### 임베디드 타입과 테이블 매핑

![ORM09-4](/images/jpa/ORM-JPA/ORM09-4.png)

- 임베디드 타입은 엔티티의 값일 뿐이다.

- **임베디드 타입을 사용하기 전과 후에 매핑하는 테이블은 같다.**

  - 테이블 입장에서는 차이가 없다.

    - 임베디드 타입 미사용

      ![ORM09-5](/images/jpa/ORM-JPA/ORM09-5.png)

    - 임베디드 타입 사용

      ![ORM09-6](/images/jpa/ORM-JPA/ORM09-6.png)

- 객체와 테이블을 아주 세밀하게 매핑하는 것이 가능

- 잘 설계한 ORM 애플리케이션은 매핑한 테이블의 수보다 클래스의 수가 더 많다.

#### 임베디드 타입 사용법

- `@Embeddable` : 값 타입을 정의하는곳에 표시

  - 기본생성자 필수

- `@Embedded` : 값 타입을 사용하는 곳에 표시 

- 데이터입력 예시

  ![ORM09-8](/images/jpa/ORM-JPA/ORM09-8.png)

#### 임베디드 타입과 연관관계

- 임베디드 타입의 값으로 엔티티를 참조 할 수 있다.

#### @AttributeOverride 속성 재정의

- 한 엔티티에서 같은 값 타입을 사용

  - 컬러몀이 중복됨

- `@AttributeOverrides`, `@AttributeOverride` 를 사용해서 컬럼명 속성을 재정의

  ```java
  @Embedded
    @AttributeOverrides({
        @AttributeOverride(
            name = "city", column = @Column(name = "work_city")
        ),
        @AttributeOverride(
            name = "street", column = @Column(name = "work_street")
        ),
        @AttributeOverride(
            name = "zip_code", column = @Column(name = "work_zip_code")
        )
    })
    private Address workAddress;
  ```

  ![ORM09-7](/images/jpa/ORM-JPA/ORM09-7.png)

#### 임베디드 타입과 null

- 임베디드 타입의 값이 null 이면 매핑한 컬럼 모두 null

<br><br>

### 값 타입과 불변 객체

- 값 타입은 복잡한 객체 세상을 조금이라도 단순화하려고 만든 개념
- 값 타입은 단순하고 안전하게 다룰 수 있어야 한다.

#### 값 타입 공유 참조

- 임베디드 타입 같은 값 타입을 여러 엔티티에서 공유하면 위험함

  - 부작용 발생 (값이 같이 변화하게됨)

    ```java
    Address address = new Address("city", "street", "zip_code");
    
    MemberForEmbeded member = new MemberForEmbeded();
    member.setUsername("bong");
    member.setHomeAddress(address);
    em.persist(member);
    
    MemberForEmbeded member2 = new MemberForEmbeded();
    member2.setUsername("rack");
    member2.setHomeAddress(address);
    em.persist(member2);
    
    member.getHomeAddress().setCity("anyang");
    ```

    ![ORM09-9](/images/jpa/ORM-JPA/ORM09-9.png)

    - update문이 2개 나가는것을 확인할 수 있다.

  - 공유하고 싶다면 엔티티로 사용해야한다.

#### 값 타입 복사

- 값 타입의 실제 인스턴스인 값을 공유하는 것은 위험

- 대신 값(인스턴스)을 복사해서 사용

  ![ORM09-10](/images/jpa/ORM-JPA/ORM09-10.png)

  ```java
  Address address = new Address("city", "street", "zip_code");
  
  MemberForEmbeded member = new MemberForEmbeded();
  member.setUsername("bong");
  member.setHomeAddress(address);
  em.persist(member);
  
  Address address2 = new Address(address.getCity(), address.getStreet(), address.getZipcode());
  MemberForEmbeded member2 = new MemberForEmbeded();
  member2.setUsername("rack");
  member2.setHomeAddress(address2);
  em.persist(member2);
  
  member.getHomeAddress().setCity("anyang");
  ```

#### 객체 타입의 한계

- 항상 값을 복사해서 사용하면 공유 참조로 인해 발생하는 부작용을 피할 수 있다.

- 문제는 **임베디드 타입처럼 직접 정의한 값 타입은 자바의 기본타입이 아니라 객체 타입**

  - 기본(primitive) 타입은 =으로 값을 할당하면, 복사된다.
  - **객체 타입은 참조 값을 직접 대입한다.**

- 객체의 공유참조는 피할 수 없다.

- 예시

  - 기본 (primitive) 타입

    ```java
    int a = 10;
    int b = a; 
    b = 4;
    ```

    - a = 10, b =4

  - 객체

    ```java
    Address a = new Address("Old");
    Address b = a;
    b.setCity("New");
    ```

    - a.city = "New", b.city = "New"
    - 참조값을 전달하기 때문에 함께 변경된다.

#### 불변객체

- 객체타입을 수정할수 없게 만들면 부작용을 원천 차단

- 값 타입은 **불변객체(immutable object)로 설계**해야한다.

- **생성시점 이후 절대 값을 변경 할 수 없는 객체**

  - 생성자로만 값을 설정, 수정자(Setter)를 만들지 않으면 됨(가장 쉬운 방법)
  - Integer, String 은 자바가 제공하는 대표적인 불변객체이다.

- 값을 바꾸고 싶다면, 전체 객체를 바꿔야한다.

  - 값타입을 복사하여 객체를 새로 생성하는 메소드를 활용하는것이 좋아보임.

    ```java
    public Address copyAddressExceptCity(String city, Address address) {
    	return new Address(city, address.getStreet(), adress.getZipcode());
    }
    ```

<br><br>

### 값 타입의 비교

- 값 타입 : 인스턴스가 달라도 그안에 값이 같으면 같은것으로 봐야한다.

  ```java
  int a = 10;
  int b = 10;
  boolean result = (a==b);
  ```

  - result = true

  ```java
  Address address1 = new Adderess("city", "street", "1000");
  Address address2 = new Adderess("city", "street", "1000");
  boolean result = (address1 == address2);
  ```

  - result = false
  - 인스턴스 참조값을 비교한다.

- 동일성 (identity) 비교 : 인스턴스의 참조값을 비교, == 사용

- 동등성(equivalence) 비교 : 인스턴스의 값을 비교, equals() 사용

- 값 타입은 `a.equals(b);` 사용하여 동등성을 비교해야한다.

- 값 타입의 `equals()` 메소드를 적절하게 재정의 해야한다.

  - 프록시를 사용하는등 복잡해지면 getter로 값을 가져와야 하는 경우도 있다,(인텔리제이 equals 생성 시, 옵션에서 선택가능)

    ![ORM09-11](/images/jpa/ORM-JPA/ORM09-11.png)

<br><br>

### 값 타입 컬렉션

![ORM09-12](/images/jpa/ORM-JPA/ORM09-12.png)

- 값타입을 하나이상 저장할 때 사용
- DB는 컬렉션을 같은 테이블에 저장할 수 없다.
- **컬렉션을 저장하기 위한 별도의 테이블이 필요하다.**
- `@ElementCollection`, `@CollectionTable` 사용
- **식별자를 따로두지 않고 값들을 묶어서 PK로 둔다.**

#### 사용 예제 - 설정

```java
@Entity
public class MemberForEmbeded {

  @Id @GeneratedValue
  private Long id;

  @Column(name = "name")
  private String username;

  @Embedded
  private Address homeAddress;

  @ElementCollection
  @CollectionTable(name = "favoirite_foods", joinColumns = @JoinColumn(name = "member_id"))
  @Column(name = "food_name")
  private Set<String> favoriteFoods = new HashSet<>();

  @ElementCollection
  @CollectionTable(name = "address", joinColumns = @JoinColumn(name = "member_id"))
  private List<Address> addressHistory = new ArrayList<>();
}
```

- favoriteFoods의 경우 String 단일 컬렉션으로 `@Column(name = "")` 저장될 컬럼이름을 설정할 수 있다.
- `@ElementCollection` 로 컬렉션임을 설정
- `CollectionTable` 로 생성될 테이블의 이름 및 join colum을 설정한다.

#### 저장

```java
MemberForEmbeded member = new MemberForEmbeded();
member.setUsername("bong");
member.setHomeAddress(new Address("city", "street", "1000"));

member.getFavoriteFoods().add("치킨");
member.getFavoriteFoods().add("족발");
member.getFavoriteFoods().add("피자");

member.getAddressHistory().add(new Address("old1" ,"street", "1000"));
member.getAddressHistory().add(new Address("old2" ,"street", "1000"));

em.persist(member);
tx.commit();
```

- 값 타입 컬렉션은 사용하고 있는 엔티티의 라이프사이클에 의존한다
  - 별도로 persist 및 update, remove 할 필요 없다.
  - **영속성 전이(Cascade) + 고아객체제거 기능을 필수로 가진다고 볼 수 있다.**

#### 조회

![ORM09-13](/images/jpa/ORM-JPA/ORM09-13.png)

![ORM09-14](/images/jpa/ORM-JPA/ORM09-14.png)

- 값 타입 컬렉션도 지연로딩 전략 사용

#### 수정

- 치킨 -> 한식

  ```java
  MemberForEmbeded findMember = em.find(MemberForEmbeded.class, member.getId());
  
  findMember.getFavoriteFoods().remove("치킨");
  findMember.getFavoriteFoods().add("한식");
  ```

  - immutable해야한다.
  - 치킨을 지우고 한식을 새로 넣어줘야한다.
  - 컬렉션의 값만 변경해도 JPA가 알아서 날려준다.

- Address 변경

  - equals메소드를 값 비교로 잘 구현 해놓으면, 같은 값을 가지는 값 객체를 remove메소드에 넣었을 때, 삭제된다.

    ```java
    findMember.getAddressHistory().remove(new Address("old1", "street", "1000"));
    findMember.getAddressHistory().add(new Address("new1", "street", "1000"));
    ```

  - 그러나 실행된 query를 살펴보면...
    old1에 대한 데이터만삭제하고 new1을 추가해주는 쿼리가 아니고
    **모든 데이터를 삭제하고 새로 추가를 해준다.**

    ![ORM09-15](/images/jpa/ORM-JPA/ORM09-15.png)

#### 값 타입 컬렉션의 제약사항

- 값 타입은 엔티티와 다르게 식별자 개념이 없다.
  - 값을 변경하면 추적이 어렵다.
- 값 타입 컬렉션에 변경 사항이 발생하면, 주인 엔티티와 연관된 모든 데이터를 삭제하고, 값 타입 컬렉션에 있는 현재 값을 모두 다시 저장한다.
  - 값 타입 컬렉션을 매핑하는 테이블은 모든 컬럼을 묶어서 기본 키를 구성해야한다. (null 입력 X, 중복저장 X)
  - `@OrderColumm(name = "address_history_order")` 등을 사용하면 순서값이 컬럼에 같이 저장되며, 모두 지우고 새로 입력해주는 동작이 아닌 update문을 사용하여 동작하지만, 이로인해 발생하는 사이드 이펙트가 많음으로 사용하지 않는것을 추천한다.

#### 값 타입 컬렉션 대안

- **실무에서는 상황에 따라 값 타입 컬렉션 대신에 일대다 관계를 고려**

- 일대다 관계를 위한 엔티티를 만들고, 여기에서 값 타입을 사용

- 영속성 전이(Cascade) + 고아 객체 제거를 사용해서 값 타입 컬렉션 처럼 사용

- **정말 단순한 내용일 때만, 값 타입 컬렉션을 사용한다. 나머지는 엔티티로 만들어서 사용하자.**

- 엔티티타입르로 승급

- 사용예시

  - AddressEntity

    ```java
    @Entity
    @Table(name = "address")
    public class AddressEntity {
    
      @Id @GeneratedValue
      private Long id;
    
      private Address address;
      
      public AddressEntity(String city, String street, String zipCode) {
        this.address = new Address(city, street, zipCode);
      }
    }
    ```

  - Member

    ```java
    @Entity
    public class MemberForEmbeded {
    
      @Id @GeneratedValue
      private Long id;
    
      @Column(name = "name")
      private String username;
      
      @OneToMany(cascade = CascadeType.ALL, orphanRemoval = true)
      @JoinColumn(name = "member_id")
      private List<AddressEntity> addressHistory = new ArrayList<>();
    }
    ```

  - insert

    ```java
    member.getAddressHistory().add(new AddressEntity("old1", "street", "1000"));
    ```

    - 영속성 전이 및 고아 객체 제거 옵션을 사용하여 라이프 사이클 관리를 엔티티에 맡긴다.
    - 그러나 일대다 맵핑이기 때문에 인서트 시에 업데이트 쿼리가 나간다.

### 엔티티 타입 vs 값 타입

- 엔티티
  - 식별자
  - 생명 주기 관리
  - 공유
- 값 타입
  - 식별자 X
  - 생명주기를 엔티티에 의존
  - 공유하지 않는것이 안전 (복사해서 사용하자.)
  - 불변 객체로 만들어야한다.

### 주의사항

- 값 타입은 정말 값 타입이라 판단될 때만 사용
-  엔티티와 값 타입을 혼동해서 엔티티를 값 타입으로 만들면 안됨
- 식별자가 필요하고, 지속해서 값을 추적, 변경해야 한다면 그것 은 값 타입이 아닌 엔티티

<br><br>