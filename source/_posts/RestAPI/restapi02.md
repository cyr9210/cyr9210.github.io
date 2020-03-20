---
title: 스프링 기반 REST API 개발02 - 이벤트 생성 API 개발 (@MockBean, ModelMapper, ErrorSerializer, JUnitParams)
date: 2019-06-25 17:10:57
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
# 스프링 기반 REST API 개발(inflearn)2 - 백기선
## 이벤트 생성 API 개발
### 이벤트 API 테스트 클래스 생성
#### 스프링 부트 슬라이스 테스트
<!-- more -->
- @WebMvcTest
    - MockMvc 빈을 자동 설정 해준다. 따라서 그냥 가져와서 쓰면 됨.
    - 웹 관련 빈만 등록해 준다. (슬라이스)

#### MockMvc
- 스프링 MVC 테스트 핵심 클래스
- 웹 서버를 띄우지 않고도 스프링 MVC (DispatcherServlet)가 요청을 처리하는 과정을 확인할 수 있기 때문에 컨트롤러 테스트용으로 자주 쓰임.

#### 테스트 할 것
- 입력값들을 전달하면 JSON 응답으로 201이 나오는지 확인.
    - Location 헤더에 생성된 이벤트를 조회할 수 있는 URI 담겨 있는지 확인.
    - id는 DB에 들어갈 때 자동생성된 값으로 나오는지 확인
- 입력값으로 누가 id나 eventStatus, offline, free 이런 데이터까지 같이 주면?
    - Bad_Request로 응답 vs 받기로 한 값 이외는 무시
- 입력 데이터가 이상한 경우 Bad_Request로 응답
    - 입력값이 이상한 경우 에러
    - 비즈니스 로직으로 검사할 수 있는 에러
    - 에러 응답 메시지에 에러에 대한 정보가 있어야 한다.
- 비즈니스 로직 적용 됐는지 응답 메시지 확인
    - offline과 free 값 확인
- 응답에 HATEOAS와 profile 관련 링크가 있는지 확인.
    - self (view)
    - update (만든 사람은 수정할 수 있으니까)
    - events (목록으로 가는 링크)
- API 문서 만들기
    - 요청 문서화
    - 응답 문서화
    - 링크 문서화
    - profile 링크 추가
<br><br>

### Event 생성 API 구현 - 201 응답 받기 
- @RestController
    - @ResponseBody를 모든 메소드에 적용한 것과 동일하다.
- ResponseEntity를 사용하는 이유
    - 응답 코드, 헤더, 본문 모두 다루기 편한 API
- Location URI 만들기
    - HATEOS가 제공하는 linkTo(), methodOn() 사용
- 객체를 JSON으로 변환
    - ObjectMapper 사용

#### 테스트 할 것
- 입력값들을 전달하면 JSON 응답으로 201이 나오는지 확인.
    - Location 헤더에 생성된 이벤트를 조회할 수 있는 URI 담겨 있는지 확인.
    - id는 DB에 들어갈 때 자동생성된 값으로 나오는지 확인
<br><br>

### Event 생성 API - EventRepository 구현
#### 스프링 데이터 JPA
- JpaRepository 상속 받아 만들기
- Enum을 JPA 맵핑시 주의할 것
    - 기본값인 ORDINAL은 숫자로 저장되는데 데이터가 꼬일 수 있다.
    - @Enumerated(EnumType.STRING)

- @MockBean
    - Mockito를 사용해서 mock 객체를 만들고 빈으로 등록해 줌.
        ![restapi](/images/restapi/restapi02-1.png)
        - 하지않으면 Repository는 Mock 객체기 때문에 save나 무엇을 하더라도 return 값은 null이 나온다.
    - (주의) 기존 빈을 테스트용 빈이 대체 한다.

#### 테스트 할 것
- 입력값들을 전달하면 JSON 응답으로 201이 나오는지 확인.
    ![restapi](/images/restapi/restapi02-2.png)
    - Location 헤더에 생성된 이벤트를 조회할 수 있는 URI 담겨 있는지 확인.
    - id는 DB에 들어갈 때 자동생성된 값으로 나오는지 확인
<br><br>

### 입력값 제한하기
#### 입력값 제한
- id 또는 입력 받은 데이터로 계산해야 하는 값들은 입력을 받지 않아야 한다.
- EventDto 적용

#### DTO -> 도메인 객체로 값 복사
- ModelMapper 사용 (DTO를 도메인으로 convert)
    ```
    <dependency>
        <groupId>org.modelmapper</groupId>
        <artifactId>modelmapper</artifactId>
        <version>2.3.1</version> 
    </dependency>
    ```
    - 빈으로 등록후 사용해야한다.
    - [참고 - ModelMapper Customizing](http://blog.naver.com/PostView.nhn?blogId=kbh3983&logNo=220988245343)

- 통합 테스트로 전환
    - @WebMvcTest 빼고 다음 애노테이션 추가
        - @SpringBootTest
        - @AutoConfigureMockMvc
    - Repository @MockBean 코드 제거

- 테스트 할 것
    - 입력값으로 누가 id나 eventStatus, offline, free 이런 데이터까지 같이 주면?
    - Bad_Request로 응답 vs 받기로 한 값 이외는 무시 (선택)
<br><br>

### 입력값 이외에 에러 발생
#### ObjectMapper 커스터마이징
- spring.jackson.deserialization.fail-on-unknown-properties=true
    - springboot에서 제공한다.
<br><br>

### Bad Request 처리하기
#### @valid 와 BindingResult (또는 Errors)
- BindingResult는 항상 @Valid 바로 다음 인자로 사용해야 함. (스프링 MVC)
![restapi](/images/restapi/restapi02-3.png)
- @NotNull, @NotEmpty, @Min, @Max, ... 사용해서 입력값 바인딩할 때 에러 확인할 수 있음
![restapi](/images/restapi/restapi02-5.png)

#### 도메인 Validator 만들기
- Validator 인터페이스 없이 만들어도 상관없음
- 빈으로 등록 후, 사용
![restapi](/images/restapi/restapi02-6.png)
![restapi](/images/restapi/restapi02-4.png)

#### 테스트 설명 용 애노테이션 만들기
![restapi](/images/restapi/restapi02-7.png)
![restapi](/images/restapi/restapi02-8.png)
<br><br>

#### Bad Request 응답 본문 만들기

- errors 객체를 JSON으로 serialization이 안되기 때문에 body에 담아도 에러가 발생한다.
  ![restapi02-9](/images/restapi/restapi02-9.png)
  - errors는 자바빈 스펙을 준수하고 있는 객체가 아니다… -> beanSerializer를 사용할 수 없다.
  - ErrorsSerilizer를 만들어서 문제를 해결 할 수 있다.

> **Error의 rejectValue와 reject의 차이점** 
>
> reject : 글로벌 에러
>
> rejectValue : field 에러

- 생성한 ErrorSerializer를 ObjectMapper에 등록한다.
  - 스프링부트에서는 @JsonComponent를 제공한다. (ObjectMapper에 등록해준다.)
    ![restapi02-10](/images/restapi/restapi02-10.png)

<br><br>

#### 매개변수를 이용한 테스트

- 테스트 코드 리팩토링
  - 테스트에서 중복 코드 제거
  - 매개변수만 바꾸면서 사용하고 싶을 때, JUnitParams를 사용하면 해결 할 수 있다.
    - 라이브러리 추가
      ```
      <dependency>
      	<groupId>pl.pragmatists</groupId>
      	<artifactId>JUnitParams</artifactId>
      	<version>1.1.1</version>
      	<scope>test</scope>
      </dependency>
      ```

    - test Class 설정
      ```
      @RunWith(JUnitParamsRunner.class)
      public class EventTest {
      	....
      }
      ```

    - 사용
      ```
      @Test
      @Parameters({
        "0, 0, ture",
        "100, 0, false",
        "0, 100, false"
      })
      public void testFree(int basePrice, int maxPrice, boolean isFree) {
          
        //given
        Event event = Event.builder()
          .basePrice(basePrice)
          .maxPrice(maxPrice)
          .build();
      
        //when
        event.update();
      
        //then
        assertThat(event.isFree()).isEqualTo(isFree);
      
      }
      ```

    - TypeSafe하게 사용하고 싶다면
      ```
      @Test
      @Parameters(method = "parametersForTestFree")
      public void testFree(int basePrice, int maxPrice, boolean isFree) {
        
        // given
        Event event = Event.builder()
          .basePrice(basePrice)
          .maxPrice(maxPrice)
          .build();
      
        // when
        event.update(); 
        
        // then
        assertThat(event.isFree()).isEqualTo(isFree);
      
      }
      
      
      private Object[] parametersForTestFree() {
      	return new Object[] {
      		new Object[] {0, 0, true},
      		new Object[] {100, 0 , false},
      		new Object[] {0, 100, false}
      	};
      }
      ```
      - parametersFor~~ 를 사용하면 method = "parametersFor~~"는 생략 가능하다.
<br><br>

