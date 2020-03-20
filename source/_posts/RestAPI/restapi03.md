---
title: 스프링 기반 REST API 개발03 - HATEOAS와 Self-Describtive Message 적용 (HATEOAS-Resource, REST Docs, 테스트용 DB와 설정 분리, API 인덱스)
date: 2019-08-08 10:24:27
tags: RestAPI
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
# 스프링 기반 REST API 개발(inflearn)3 - 백기선

## HATEOAS와 Self-Describtive Messgae 적용

### 스프링 HATEOAS 소개
<!-- more -->
- api를 만들 때, REST하게 resource를 보다 쉽게 제공해주는 라이브러리

- [reference문서](https://docs.spring.io/spring-hateoas/docs/current/reference/html/)

- 링크 만드는 기능

  - 문자열 가지고 만들기
  - 컨트롤러와 메소드로 만들기

- 리소스 만드는 기능

  - 리소스: 데이터 + 링크

- 링크 찾아주는 기능

  - Traverson
  - LinkDiscoverers
  - 강좌에서는 다루지 않는다..

- 링크

  - HREF : URL, URI등을 설정 
  - REL : 현재 리소스와의 관계를 설정해준다.
    - self
    - profile
    - update-events
    - query-events
    - ...

<br>
<br>

### 스프링 HATEOAS 적용

#### EventResource 만들기

- extends ResourceSupport 의 문제

- data resource가 Json으로 감싸져 있다.
  ![restapi03-1](/images/restapi/restapi03-1.png)

  

- 해결방안

  - @JsonUnwrapped

    ![restapi03-2](/images/restapi/restapi03-2.png)

  - extends Resource<T>

    ![restapi03-3](/images/restapi/restapi03-3.png)

    - @JsonUnwrapped가 포함되어 있다.

      ![restapi03-18](/images/restapi/restapi03-18.png)

    - 링크정보도 추가 할 수 있다.

    ![restapi03-5](/images/restapi/restapi03-5.png)

- test 결과

![restapi03-4](/images/restapi/restapi03-4.png)

- 링크정보를 포함하고 있기 때문에 response의 content Type은 hal+json

<br>
<br>

### 스프링 REST Docs 소개

- [reference문서](https://docs.spring.io/spring-restdocs/docs/2.0.2.RELEASE/reference/html5/)
- 스프링 MVC test를 사용하여 rest api문서의 일부분들을 생성하는데 유용한 기능을 제공해주는 라이브러리
- test를 실행하면서 요청과 응답 헤더등의 정보들을 가지고 문서의 조각(snipet)을 생성한다.
- 만들어진 문서의 조각을 사용하여 REST API 문서를 만들 수 있다.
- Ascidockor라는 툴을 사용하여 Aschidoc문법에 맞춘 snipet을 html문서로 만들어준다.
- MockMVC, WebTestClient, RESTAssured 등을 사용하여 만든 테스트에 적용할 수 있다.
- 스프링 버전5, Java8 이상 지원

<br>
<br>

### 스프링 REST Docs 적용

#### REST Docs 자동설정

```
@AutoConfigureRestDocs
public class EventControllerTest { }
```

### RestDocs 사용

![restapi03-6](/images/restapi/restapi03-6.png)

#### RestDocMockMvc 커스터마이징

- RestDcosMockMvcConfigurationCustomizer 구현한 빈 등록

  ![restapi03-7](/images/restapi/restapi03-7.png)

- 다른 기능을 적용하고 싶다면, [processors 목록](https://docs.spring.io/spring-restdocs/docs/2.0.2.RELEASE/reference/html5/#customizing-requests-and-responses-preprocessors) 을 확인하고 적용시키면 된다.

- 설정을 적용시키기 위해서는 TestClass에서 Import해주어야한다.

  ![restapi03-8](/images/restapi/restapi03-8.png)

<br><br>

### 스프링 REST Docs - 각종 문서 조각 생성하기

#### 요청 필드 문서화

- requestFields() + fieldWithPath()
- responseFields() + fieldWithPath()
- requestHeaders() + headerWithName()
- responseHedaers() + headerWithName()
- links() + linkWithRel()

#### Relaxed 접두어

- ResponseFields에 링크 정보가 포함되기 때문에 링크정보를 문서화 했음에도 불구하고, 에러가 발생한다 이러한 문제는 Relaxed라는 prefix를 붙여줌으로서 해결 할 수 있다.
  ![restapi03-9](/images/restapi/restapi03-9.png)
- 장점: 문서 일부분만 테스트 할 수 있다.
- 단점: 정확한 문서를 생성하지 못한다.
- **Relaxed를 쓰는거 보다는 ResponseFields에 링크정보를 중복으로 포함시키는 것을 추천한다. (변경사항이 있을 시, 반영을 위해서)**

<br><br>

### 스프링 REST Docs - 문서빌드

- [reference 문서](https://docs.spring.io/spring-restdocs/docs/2.0.2.RELEASE/reference/html5/)

#### Maven 플러그인 설정

- pom.xml에 설정

  ```
  <build>
      <plugins>
          <plugin>
              <groupId>org.asciidoctor</groupId>
              <artifactId>asciidoctor-maven-plugin</artifactId>
              <version>1.5.3</version>
              <executions>
                  <execution>
                      <id>generate-docs</id>
                      <phase>prepare-package</phase>
                      <goals>
                          <goal>process-asciidoc</goal>
                      </goals>
                      <configuration>
                          <backend>html</backend>
                          <doctype>book</doctype>
                      </configuration>
                  </execution>
              </executions>
              <dependencies>
                  <dependency>
                      <groupId>org.springframework.restdocs</groupId>
                      <artifactId>spring-restdocs-asciidoctor</artifactId>
                      <version>${spring-restdocs.version}</version>
                  </dependency>
              </dependencies>
          </plugin>
          <plugin>
              <artifactId>maven-resources-plugin</artifactId>
              <version>2.7</version>
              <executions>
                  <execution>
                      <id>copy-resources</id>
                      <phase>prepare-package</phase>
                      <goals>
                          <goal>copy-resources</goal>
                      </goals>
                      <configuration>
                          <outputDirectory>
                              ${project.build.outputDirectory}/static/docs
                          </outputDirectory>
                          <resources>
                              <resource>
                                  <directory>
                                      ${project.build.directory}/generated-docs
                                  </directory>
                              </resource>
                          </resources>
                      </configuration>
                  </execution>
              </executions>
          </plugin>
          <plugin>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-maven-plugin</artifactId>
          </plugin>
      </plugins>
  </build>
  ```

#### Gradle 플러그인 설정

- build.gradle에 설정

  ```
  plugins { 
  	id "org.asciidoctor.convert" version "1.5.3"
  }
  
  dependencies {
  	asciidoctor 'org.springframework.restdocs:spring-restdocs-asciidoctor:2.0.2.RELEASE' 
  	testCompile 'org.springframework.restdocs:spring-restdocs-mockmvc:2.0.2.RELEASE' 
  }
  
  ext { 
  	snippetsDir = file('build/generated-snippets')
  }
  
  test { 
  	outputs.dir snippetsDir
  }
  
  asciidoctor { 
  	inputs.dir snippetsDir 
  	dependsOn test 
  }
  
  bootJar {
  	dependsOn asciidoctor 
  	from ("${asciidoctor.outputDir}/html5") { 
  		into 'static/docs'
  	}
  }
  ```

#### 템플릿 파일 추가

- src/main/asciidoc/index.adoc을 만들고 asciidoc 문법에 맞추어 작성한다.

  - Maven 프로젝트

    ![restapi03-10](/images/restapi/restapi03-10.png)

    - target/generated-docs

    - target/classes/docs

    - 상기 두개의 디렉토리에 index.html이 생성되며 해당 파일을 실행하거나 어플리케이션 실행 시, /docs/index.html에서 확인할 수 있다.

      ![restapi03-11](/images/restapi/restapi03-11.png)

  - Gradle 프로젝트

    ![restapi03-12](/images/restapi/restapi03-12.png)

    - build/asciidoc/html5
    - Maven과 마찬가지이다.

#### 작동내용

- prepare-package :: process-asciidoc
  - package할때 prepare-package 과정에서 asciidoc을 처리하라는 process-ascidoc기능을 실행하는것이다.
  - src안의 모든 asciidoc문서를 html로 만들어준다. (generated-docs위치로)
- prepare-package :: copy-resources
  - generated-docs에서 classes/docs/static으로 복사한다.
- **asciidoc을 먼저 처리하고 후에 복사하기 때문에, 순서가 중요하다. (process-asciidoc -> copy-resources)**

#### profile 링크 추가

- REST의 조건인 Self-description을 만족하기 위해 생성한 문서의 링크를 profile이란 이름으로 추가해준다.

- RESTdocs문서 추가를 위해 테스트를 추가해준다.

  ![restapi03-13](/images/restapi/restapi03-13.png)

- Controller에서 profile 링크 정보를 추가해준다.

  ![restapi03-14](/images/restapi/restapi03-14.png)

<br><br>

### 테스트용 DB와 설정 분리하기

- 테스트 할 때는 H2 애플리케이션 서버를 실행할 때는 PostgreSQL을 사용하도록 변경

#### 의존성 추가 및 수정

```
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>test</scope>
</dependency>

<dependency>
    <groupId>org.postgresql</groupId>
    <artifactId>postgresql</artifactId>
    <scope>runtime</scope>
</dependency>
```

- H2 의존성은 scope를 test로 변경
- PostgreSQL 의존성을 추가한다.
- 참고 : [스프링 부트 활용 -스프링 데이터 - PostgreSQL](https://cyr9210.github.io/2019/04/15/Spring/springboot13/)

### 설정

- DB 설정(application.properties)
  ```
  spring.datasource.username=postgres
  spring.datasource.password=pass
  spring.datasource.url=jdbc:postgresql://localhost:5432/postgres
  spring.datasource.driver-class-name=org.postgresql.Driver
  ```

- 하이버네이트 설정(application.properties)
  ```
  spring.jpa.hibernate.ddl-auto=create-drop
  spring.jpa.properties.hibernate.jdbc.lob.non_contextual_creation=true
  spring.jpa.properties.hibernate.format_sql=true
  
  logging.level.org.hibernate.SQL=DEBUG
  logging.level.org.hibernate.type.descriptor.sql.BasicBinder=TRACE
  ```

### Profile 활용하여 Test 설정

- application-test.properties 파일을 test - resources 디렉토리에 생성 및 오라이딩할 내용만 작성한다.
  ```
  spring.datasource.username=sa
  spring.datasource.password=
  spring.datasource.url=jdbc:h2:mem:testdb
  spring.datasource.driver-class-name=org.h2.Driver
  
  spring.datasource.hikari.jdbc-url=jdbc:h2:mem:testdb
  spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.H2Dialect
  ```

- 테스트 클래스에서 @ActivePorfile("test") 적용

  ![restapi03-15](/images/restapi/restapi03-15.png)

  - 실행 시, profile = test로 실행된다.

<br><br>

### 인덱스 핸들러 만들기

#### 인덱스 핸들러

```java
@Getmapping("/api")
public void ResourceSupport root() {
  ResourceSupport index = new ResourceSupport();
  index.add(linkTo(EventController.class).withRel("evnets"));
  return index;
}
```

- 다른 리소스에 대한 링크 제공
- 문서화

### 에러 리소스

- 에러 발생 시, 돌아갈 수 있는 api는 index 이다.
  그에 따라서 인덱스로 가는 링크를 제공한다.

- ErrorsResouce 생성 (extends Resouce<Error>)

  ```java
  public class ErrorsResource extends Resource<Errors> {
      public ErrorsResource(Errors content, Link... links) {
          super(content, links);
          add(linkTo(methodOn(IndexController.class).index()).withRel("index"));
      }
  }
  ```

- Errors 사용하던 부분 ErrorsResouce로 변경

  ![restapi03-16](/images/restapi/restapi03-16.png)

- 문제점 @JsonUnwrapped JSON arrays는 unwrap이 되지 않는다.

  ![restapi03-17](/images/restapi/restapi03-17.png)

  - Serializer를 재정의 하거나 Content로 wrapped 되어있는 상태로 쓴다. 
    (여기서는 wrapped되어있는 상태로 쓰기위해서 TestCase를 수정했다.)

    ![restapi03-19](/images/restapi/restapi03-19.png)

<br>
<br>