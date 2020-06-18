---
title: 연관관계 편의 메소드 사용 시, list를 전부 조회하여 성능이 떨어지지 않을까?
toc: true
widgets:
  - type: toc
    position: right
  - type: tagcloud
    position: right
sidebar:
  right:
    sticky: true
date: 2020-06-19 08:38:55
tags: JPA
---
![JPA](/images/jpa_logo.png)
<!-- more -->
# JPA

## Debug

### 문제

- 연관관계 편의 메소드 사용 시, list를 전부 조회하여 성능이 떨어지지 않을까?
- 예시상황
  - product,  order 양방향 관계
  - 양쪽에 편의메소드를 생성하고 테스트 해보자.
    - product쪽에 addOrder라는 편의 메소드
    - Order쪽에 addOrder라는 편의 메소드

### 과정

- Product

  ```java
  @Getter @Setter
  @NoArgsConstructor
  @Entity
  public class Product {
  
    @Id @GeneratedValue
    private Long id;
  
    private String name;
  
    private int price;
  
    private int stockAmount;
  
    @OneToMany(mappedBy = "product", cascade = CascadeType.ALL)
    private List<Order> orders = new ArrayList<Order>();
  
    public void addOrders(Order order) {
      order.setProduct(this);
      this.orders.add(order);
    }
  }
  ```

- Order

  ```java
  @Getter @Setter
  @NoArgsConstructor
  @Entity
  @Table(name = "orders")
  public class Order {
  
    @Id @GeneratedValue
    private Long id;
  
    private int orderAmount;
  
    @Embedded
    private Address address;
  
    @ManyToOne
    @JoinColumn(name = "product_id")
    private Product product;
  
    public void addOrder(Product product) {
      this.product = product;
      product.getOrders().add(this);
    }
  }
  ```

- 테스트

  ```java
  public class JpqlMain {
  
    public static void main(String[] args) {
      EntityManagerFactory emf = Persistence.createEntityManagerFactory("hello");
      EntityManager em = emf.createEntityManager();
  
      EntityTransaction tx = em.getTransaction();
      tx.begin();
  
      try {
        Product product = new Product();
        product.setName("product");
        product.setPrice(1000);
        product.setStockAmount(100);
  
        Order order = new Order();
        order.setOrderAmount(1);
        order.setProduct(product);
        product.getOrders().add(order);
  
        em.persist(product);
  
        System.out.println("flush");
        em.flush();
        em.clear();
        System.out.println("=========");
  
  
        Product product1 = em.find(Product.class, 1L);
  
        Order order2 = new Order();
        order2.setOrderAmount(2);
  
        order2.addOrder(product1);
        
        tx.commit();
      } catch (Exception e) {
        tx.rollback();
        e.printStackTrace();
      } finally {
        em.close();
      }
  
      emf.close();
    }
  }
  ```

  - sample data를 넣고 `em.flush()` , `em.clear()` 
  - product를 find 후에 편의메소드를 사용하여 flush이후에 나오는 쿼리문을 확인한다.
    - `Order.addOrder()`
    - `Product.addOrder()`

### 결과

![jpa_mapping_method01](/images/jpa/jpa_mapping_method01.png)

- **product 쪽 편의메소드 사용 시, 리스트 조회하지 않는다.**

![jpa_mapping_method02](/images/jpa/jpa_mapping_method02.png)

- **Order쪽 편의메소드 사용시에도 마찬가지로 리스트를 조회하지 않는다.**
- **`get(index)` 나 `size()` 로 조회하면 조회쿼리가 나가지만 인서트시에 해당 리스트를 전체 조회하지는 않는다.**

<br><br>

