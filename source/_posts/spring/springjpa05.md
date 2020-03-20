---
title: 스프링 데이터 JPA05 - 스프링 데이터 Common
date: 2019-05-03 18:05:54
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
## 스프링 데이터 Common
<!-- more -->
### 스프링 데이터 JPA 활용 파트 소개
![springjpa](/images/jpa/jpa05-1.png)
- 스프링 데이터 : 하나의 프로젝트를 말하는것이 아니고, 여러개의 프로젝트들의 묶음을 말하는것.
(SQL & NoSQL 저장소 지원 프로젝트의 묶음.)
- 스프링 데이터 Common : 여러 저장소 지원 프로젝트의 공통 기능 제공.
- 스프링 데이터 REST : 저장소의 데이터를 하이퍼미디어 기반 HTTP 리소스로(REST API로) 제공하는 프로젝트.
- 스프링 데이터 JPA : 스프링 데이터 Common이 제공하는 기능에 JPA 관련 기능 추가
- [참고문서](https://spring.io/projects/spring-data)

### Repository
- 앞서 JpaRepository를 상속하는 클래스를 만들어 사용한적이 있었는데.. 사실 해당 부분은 JpaRepository가 스프링데이터 Common의 PagingAndSortingRepostiory를 상속받고 있다.
    이미지
    - PagingAndSortingRepostiory : paging, sorting 메소드를 지원
    - CrudRepository : CRUD 메소드를 지원
        - @NoRepositoryBean가 붙어있는데.. 실제 빈을 만들어 등록하지 않도록한다.
        (중간단계의 Repository에는 붙어있다. 실제 Repository가 아님을 표현한다.)
    - Repository : marker 인터페이스, 실질적인 기능을 하진 않는다.
    
#### CrudRepository 메소드
- save(entity); 
    - 하나의 엔티티를 저장하고 그 엔티티를 리턴해준다.
- saveall(Iterable<> entities);
    - 여러개의 엔티티를 저장하고 리턴(Iterable타입으로..)
- findById(id);
    - id로 엔티티를 찾아서 리턴한다.
    - Optional<>로 리턴된다.(null방지..)
- existById(id);
    - 해당 id에 해당하는 엔티티의 유무를 확인한다.
- findAll();
    - 어떤 한 엔티티의 모든 정보를 불러온다.
    - 거의 테스트용으로 사용된다.
    - Iterable로 리턴한다. (List<>로 리턴하는경우는 스프링 데이터 JPA를 사용하는것)
- findAllById(Iterable<> ids);
    - 여러id에 해당하는 목록을 가져온다.
- count();
    - 갯수
- delete(id);
    - 삭제
    - deleteAll() 도 있다.
    
##### test
- 기본적으로 데이터 테스트들은 @Transactional을 가지고 있는데.. 기본적으로 롤백을 한다.
    - 어자피 롤백할 쿼리이기 때문에 test클래스 실행 시, insert쿼리문은 날라가지 않는다.
    - 보고 싶다면 @Rollback(false)를 추가하자.
    
- CRUD
    ![springjpa](/images/jpa/jpa05-2.png)

- PagingAndSorting
    ![springjpa](/images/jpa/jpa05-3.png)
<br>

### Repository 인터페이스 정의하기
#### Repository 인터페이스로 공개할 메소드를 직접 일일히 정의하고 싶을 때
- 특정 레퍼지토리 정의
    - @RepositoryDefinition
    ![springjpa](/images/jpa/jpa05-4.png)
    - test(정의한 Repository인터페이스는 모두 테스트를 해주어야한다.)
    ![springjpa](/images/jpa/jpa05-5.png)
- 공통 인터페이스 정의
    - @NoRepositoryBean
    ![springjpa](/images/jpa/jpa05-6.png)
    - 사용할 인터페이스에서 상속
    ![springjpa](/images/jpa/jpa05-7.png)
<br>

### Null 처리하기
#### Optional
- 자바8 부터 지원
- Null을 리턴하지 않고, 비어있는 콜렉션을 리턴한다.
- 예시
    ![springjpa](/images/jpa/jpa05-8.png)
- Optional 메소드 예시
    ![springjpa](/images/jpa/jpa05-9.png)

#### Null 어노테이션
- 스프링 프레임워크 5.0부터 지원하는 Null 애노테이션 지원.
    - @NonNullApi
        ![springjpa](/images/jpa/jpa05-10.png)
        - package-info.java파일 생성 후, 어노테이션 사용
        - 패키지안의 모든 파라미터 및 리턴타입 및 모두 Null이 아니어야한다.
        - Null이 필요한곳에 @Nullable을 사용 해야한다.
    - @NonNull
        ![springjpa](/images/jpa/jpa05-11.png)
        - Null이 아니길 바랄때 사용한다.
    - @Nullable.
- 툴의 도움을 받을 수 있다.
    ![springjpa](/images/jpa/jpa05-12.png)![springjpa](/images/jpa/jpa05-13.png)
    - NonNullApi는 불가능
<br>

### 쿼리 만들기 개요
#### 스프링 데이터 저장소의 메소드 이름으로 쿼리 만드는 방법
- 메소드 이름을 분석해서 쿼리 만들기 (CREATE)
    ![springjpa](/images/jpa/jpa05-14.png)
- 미리 정의해 둔 쿼리 찾아 사용하기 (USE_DECLARED_QUERY)
    - 우선순위 Query > Procedure > NamedQuery
    - 기본적으로 JPQL을 써야하나, SQL을 쓰고 싶다면 nativequery를 true로 변경한다.
    ![springjpa](/images/jpa/jpa05-16.png)
- **미리 정의한 쿼리 찾아보고 없으면 만들기 (CREATE_IF_NOT_FOUND) (기본전략)**
    - @EnableJpaRepositories(queryLookupStrategy = )로 변경 가능하다.
        ![springjpa](/images/jpa/jpa05-15.png)

#### 쿼리 만드는 방법
- 리턴타입 {접두어}{도입부}By{프로퍼티 표현식}(조건식)\[(And|Or){프로퍼티표현식}(조건식)]{정렬 조건} (매개변수)
  - 접두어 : Find, Get, Query, Count, ...
  - 도입부 : Distinct, First(N), Top(N)
  - 프로퍼티 표현식 : Person.Address.ZipCode => find(Person)ByAddress_ZipCode(...)
  - 조건식 : IgnoreCase, Between, LessThan, GreaterThan, Like, Contains, ...
  - 정렬 조건 : OrderBy{프로퍼티}Asc|Desc
  - 리턴 타입 : E, Optional<E>, List<E>, Page<E>, Slice<E>, Stream<E>
  - 매개변수 : Pageable, Sort
<br>

### 쿼리 만들기 실습
#### 기본예제
```
List<Person> findByEmailAddressAndLastname(EmailAddress emailAddress, String lastname);
```

##### distinct
```
List<Person> findDistinctPeopleByLastnameOrFirstname(String lastname, String firstname);
List<Person> findPeopleDistinctByLastnameOrFirstname(String lastname, String firstname);
```

##### ignoring case
```
List<Person> findByLastnameIgnoreCase(String lastname);
List<Person> findByLastnameAndFirstnameAllIgnoreCase(String lastname, String firstname);
```

#### 정렬
```
List<Person> findByLastnameOrderByFirstnameAsc(String lastname);
List<Person> findByLastnameOrderByFirstnameDesc(String lastname);
```

#### 페이징
```
Page<User> findByLastname(String lastname, Pageable pageable);
Slice<User> findByLastname(String lastname, Pageable pageable);
List<User> findByLastname(String lastname, Sort sort);
List<User> findByLastname(String lastname, Pageable pageable);
```

#### 스트리밍
- try-with-resource 사용할 것. (Stream을 다 쓴다음에 close() 해야 함)
```
Stream<User> readAllByFirstnameNotNull();
```

#### 그 외 실습내용
[Repository class](https://github.com/cyr9210/springJPA-study/blob/master/03/src/main/java/me/bong/springjpa03/post/CommentRepository.java)
[test class](https://github.com/cyr9210/springJPA-study/blob/master/03/src/test/java/me/bong/springjpa03/post/CommnetRepositoryTest.java)
<br><br>

### 비동기 쿼리
#### Future
```
@Async
Future<User> findByFirstname(String firstname);
```
- Java5에서 생김
- @EnableAsync 해주어야한다.

#### CompletableFuture
```
@Async 
CompletableFuture<User> findOneByFirstname(String firstname);
```
- Java8에서 들어옴 
- @EnableAsync 해주어야한다.

#### ListenableFuture
```
@Async 
ListenableFuture<User> findOneByLastname(String lastname);
```
- 스프링에서 만들었다.
- 가장 깔끔함.
- 해당 메소드를 스프링 TaskExecutor에 전달해서 별도의 쓰레드에서 실행함.
- Reactive랑은 다른 것임

#### 권장하지 않는 이유
- 테스트 코드 작성이 어려움.
- 코드 복잡도 증가.
- 성능상 이득이 없음.
    - DB 부하는 결국 같고.
    - 메인 쓰레드 대신 백드라운드 쓰레드가 일하는 정도의 차이.
    - 단, 백그라운드로 실행하고 결과를 받을 필요가 없는 작업이라면 @Async를 사용해서 응답 속도를 향상 시킬 수는 있다.
<br><br>

### 커스텀 리포지토리
- 쿼리 메소드(쿼리 생성과 쿼리 찾아쓰기)로 해결이 되지 않는 경우 직접 코딩으로 구현 가능.
    - 스프링 데이터 리포지토리 인터페이스에 기능 추가.
    - 스프링 데이터 리포지토리 기본 기능 덮어쓰기 가능.
    
#### 구현방법
1. 커스텀 리포지토리 인터페이스 정의
![springjpa](/images/jpa/jpa05-17.png)
1. 인터페이스 구현 클래스 만들기 (기본 접미어는 Impl)
![springjpa](/images/jpa/jpa05-18.png)
    - EntityManager, JdbcTemplate 등을 주입받아 사용할 수 있다.
1. 엔티티 리포지토리에 커스텀 리포지토리 인터페이스 추가
![springjpa](/images/jpa/jpa05-19.png)

- 기본 기능을 덮어쓰고 싶다면 기본기능 메소드를 그대로 정의하고 위와 같은 방법으로 구현한다.
![springjpa](/images/jpa/jpa05-20.png)

#### 접미어 설정하기
- @EnableJpaRepositories(repositoryImplementationPostfix = "변경값")
    ![springjpa](/images/jpa/jpa05-21.png)    
    - 기본값은 "Impl" 이다.
<br><br>

### 기본 리포지토리 커스터마이징
- 모든 리포지토리에 공통적으로 추가하고 싶은 기능이 있거나 덮어쓰고 싶은 기본 기능이 있을때

#### 구현방법
1. JpaRepository를 상속 받는 인터페이스 정의
    ![springjpa](/images/jpa/jpa05-22.png)
    - @NoRepositoryBean
2. 기본 구현체를 상속 받는 커스텀 구현체 만들기
    ![springjpa](/images/jpa/jpa05-23.png)
3. @EnableJpaRepositories에 설정
    - repositoryBaseClass
    ![springjpa](/images/jpa/jpa05-25.png)
4. 엔티티 레포지토리에 설정 
    ![springjpa](/images/jpa/jpa05-24.png)
<br>

### 도메인 이벤트
도메인 관련 이벤트를 발생시키기에 대하여 스프링 프레임워크 사용방법 및 스프링 데이터 사용방법에 대해 알아보자.

#### 스프링 프레임워크의 이벤트 관련 기능
- [참고문서](https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#context-functionality-events)
- 이벤트를 만들때는 ApplicationEvent를 상속받도록한다. (extends ApplicationEvent)
    ![springjpa](/images/jpa/jpa05-26.png)
- ApplicationContext는 BeanFactory와 더불어 ApplicationEventPublisher도 상속받고 있다. (이벤트 등록 기능 지원)
    ![springjpa](/images/jpa/jpa05-27.png)
- 리스너 (하기 두가지 방법중 선택하여 사용한다.)
    - implements ApplicationListener<E extends ApplicationEvent>
        ![springjpa](/images/jpa/jpa05-28.png)
    - @EventListener
        ![springjpa](/images/jpa/jpa05-29.png)

#### 스프링 데이터의 도메인 이벤트 Publisher
- extends AbstractAggregateRoot<E>
    ![springjpa](/images/jpa/jpa05-30.png)
    - AbstractAggregateRoot안에 두 어노테이션이 있다.
        - @DomainEvents
        - @AfterDomainEventPublication
    - registerEvent() 메소드를 사용할 수 있게된다. (이벤트 등록)        
- 현재는 save() 할 때만 발생 합니다.
    ![springjpa](/images/jpa/jpa05-31.png)

### QueryDSL(Domain Specific Language)
#### QueryDSL을 사용하는 이유
- 조건문을 표현하는 방법이 타입세이프하다.
- 조건문을 Predicate 인터페이스로 조건문을 표현하는데, 조합 및 별도 관리가 가능하다.

#### 여러 쿼리 메소드는 대부분 두 가지 중 하나
- Optional<T> findOne(Predicate): 이런 저런 조건으로 무언가 하나를 찾는다.
- List<T>|Page<T>|.. findAll(Predicate): 이런 저런 조건으로 무언가 여러개를 찾는다.
- QuerydslPredicateExecutor 인터페이스

#### QueryDSL
- [참고문서](http://www.querydsl.com)
- 타입 세이프한 쿼리 만들 수 있게 도와주는 라이브러리
- JPA, SQL, MongoDB, JDO, Lucene, Collection 지원
- [QueryDSL JPA 연동 가이드](http://www.querydsl.com/static/querydsl/4.1.3/reference/html_single/#jpa_integration)

#### 연동 방법
- 기본 리포지토리 커스터마이징 안 했을 때.
    - 의존성추가(스프링 부트가 버전관리 해줌)
        - querydsl-apt : 쿼리용 Domain Specific Laguage를 생성해준다. 플러그인 설정이 필요하다.
        - queydsl-jpa
        ```
        <dependency>
            <groupId>com.querydsl</groupId>
            <artifactId>querydsl-apt</artifactId>
        </dependency>
        
        <dependency>
            <groupId>com.querydsl</groupId>
            <artifactId>querydsl-jpa</artifactId>
        </dependency>
        ```
    - 플러그인 추가
    ```
    <plugin>
        <groupId>com.mysema.maven</groupId>
        <artifactId>apt-maven-plugin</artifactId>
        <version>1.1.3</version>
        <executions>
            <execution>
                <goals>
                    <goal>process</goal>
                </goals>
                <configuration>
                    <outputDirectory>target/generated-sources/java</outputDirectory>
                    <processor>com.querydsl.apt.jpa.JPAAnnotationProcessor</processor>
                </configuration>
            </execution>
        </executions>
    </plugin>
    ```
    - compile 시, 해당 파일이 생성된다.(QAccount)
        ![springjpa](/images/jpa/jpa05-32.png)
        
    - Repostiory 인터페이스에 QuerydslPredicateExecutor<도메인타입> 상속을 추가한다.
        ![springjpa](/images/jpa/jpa05-33.png)
        
    - predicate를 사용하여 조건문을 만들고 사용한다.
        ![springjpa](/images/jpa/jpa05-34.png)
        
- 기본 리포지토리 커스타마이징 했을 때.
    - 기존에는 만든 커스터마이징 구현체에 SimpleJpaRepository를 상속하지 않도록하고 QuerydslJpaRepository를 상속하도록 함으로 해결 할 수 있었다.
    - 현재는 상기 방법가 동일하게 해도 구현되는것을 확인하였다.
<br><br>

### DomainClassConverter

#### 스프링 Converter
- [참고문서](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/convert/converter/Converter.html)

#### DomainClassConverter
- 사용 전
    ```
    @GetMapping("/posts/{id}")
        public String getAPost(@PathVariable Long id) {
            Optional<Post> byId = postRepository.findById(id);
            Post post = byId.get();
            return post.getTitle();
        }
    ```
    
- 사용
    - 해당아이디 값으 찾아서 Domain객체로 변환해준다.
        ```
        @GetMapping("/posts/{id}")
            public String getAPost(@PathVariable(“id”) Post post) {
                return post.getTitle();
            }
        ```

- Formatter
    - 문자열 기반
    - 문자열 -> 다른타입
    - 어떤타입 -> 문자열 프린팅
<br><br>

### Pageable과 Sort 매개변수
#### 스프링 MVC HandlerMethodArgumentResolver
- 스프링 MVC 핸들러 메소드의 매개변수로 받을 수 있는 객체를 확장하고 싶을 때 사용하는 인터페이스
- [참고문서](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/method/support/HandlerMethodArgumentResolver.html)

#### 페이징과 정렬 관련 매개변수
- page: 0부터 시작.
- size: 기본값 20.
- sort: property,property(,ASC|DESC)
- 예) sort=created,desc&sort=title (asc가 기본값)

#### 예제
- 컨트롤러
    ```
    @GetMapping("/posts")
        public Page<Post> getPost(Pageable pageable){
            return postRepository.findAll(pageable);
    }
    ```
- test
    ```
     @Test
     public void getPosts() throws Exception {
         Post post = new Post();
         post.setTitle("jpa");
         postRepository.save(post);
 
         mockMvc.perform(get("/posts/")
                 .param("page", "0")
                 .param("size", "10")
                 .param("sort", "created,desc")
                 .param("sort", "title"))
                 .andDo(print())
                 .andExpect(status().isOk())
         .andExpect(jsonPath("$.content[0].title", is("jpa")));
     }
    ```
<br><br>

### HATEOAS
#### Page를 PagedResource로 변환하기
- 일단 HATEOAS 의존성 추가 (starter-hateoas)
- 핸들러 매개변수로 PagedResourcesAssembler

#### 예제
- 컨트롤러
    ```
     @GetMapping("/posts")
     public PagedResources<Resource<Post>> getPosts (Pageable pageable, PagedResourcesAssembler<Post> assembler){
         return assembler.toResource(postRepository.findAll(pageable));
     }
    ```
- test
    ```
     @Test
     public void getPosts() throws Exception {
         createPost("jpa");
 
 
         mockMvc.perform(get("/posts/")
                 .param("page", "0")
                 .param("size", "10")
                 .param("sort", "created,desc")
                 .param("sort", "title"))
                 .andDo(print())
                 .andExpect(status().isOk())
         .andExpect(jsonPath("$.content[0].title", is("jpa")));
     }
    
     private void createPost(String title) {
         int postcount = 0;
 
         while(postcount <= 100){
             Post post = new Post();
             post.setTitle(title);
             postRepository.save(post);
             postcount++;
         }
    ```
    
- 결과
    ```
       "_embedded":{  
          "postList":[  
             {  
                "id":140,
                "title":"jpa",
                "created":null
             },
    ...
             {  
                "id":109,
                "title":"jpa",
                "created":null
             }
          ]
       },
       "_links":{  
          "first":{  
             "href":"http://localhost/posts?page=0&size=10&sort=created,desc&sort=title,asc"
          },
          "prev":{  
             "href":"http://localhost/posts?page=1&size=10&sort=created,desc&sort=title,asc"
          },
          "self":{  
             "href":"http://localhost/posts?page=2&size=10&sort=created,desc&sort=title,asc"
          },
          "next":{  
             "href":"http://localhost/posts?page=3&size=10&sort=created,desc&sort=title,asc"
          },
          "last":{  
             "href":"http://localhost/posts?page=19&size=10&sort=created,desc&sort=title,asc"
          }
       },
       "page":{  
          "size":10,
          "totalElements":100,
          "totalPages":20,
          "number":2
       }
    }
    ```

<br>

