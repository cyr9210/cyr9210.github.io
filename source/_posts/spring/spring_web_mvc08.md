---
title: 스프링 MVC 활용08 - HTTP 요청 맵핑하기 
date: 2019-06-24 21:02:45
tags: SpringWebMvc
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

# 스프링 웹 MVC(inflearn) - 백기선 
## SpringMVC 활용

<!-- more -->

### 스프링 MVC 핵심 기술 소개
- [reference 문서](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-controller)

#### 애노테이션 기반의 스프링 MVC
- 요청 맵핑하기
- 핸들러 메소드
- 모델과 뷰
- 데이터 바인더
- 예외 처리
- 글로벌 컨트롤러

#### 사용할 기술
- 스프링 부트
- 스프링 프레임워크 웹 MVC
- 타임리프

#### 학습 할 애노테이션
- @RequestMapping
    - @GetMapping, @PostMapping, @PutMapping, ...
- @ModelAttribute
- @RequestParam, @RequestHeader
- @PathVariable, @MatrixVariable
- @SessionAttribute, @RequestAttribute, @CookieValue
- @Valid
- @RequestBody, @ResponseBody
- @ExceptionHandler
- @ControllerAdvice
<br><br>

### HTTP요청 맵핑하기 - 요청 메소드
- Handler : 요청을 처리할 수 있는 메소드를 보통 핸들러라고 부른다.

- HTTP Method
    - GET, POST, PUT, PATCH, DELETE, ...

#### GET 요청
- 클라이언트가 서버의 리소스를 요청할 때 사용한다.
- 캐싱 할 수 있다. 
    - 캐시와 관련된 헤더를 응답에 실어 보낼 수 있다.
    - 동일한 요청을 보낼 때 조건적인 GET으로 바뀔 수 있다.
        - 304 not modified 라고 응답하면, body를 보내지 않아도 클라이언트 쪽에서 캐싱하고 있던 그정보 드대로 보여준다.   
- 브라우저 기록에 남는다.
- 북마크 할 수 있다.
- 민감한 데이터를 보낼 때 사용하지 말 것. (URL에 다 보이니까)
- idempotent (동일한 요청 시, 동일한 응답)

#### POST 요청
- 클라이언트가 서버의 리소스를 수정하거나 새로 만들 때 사용한다. (idempotent X)
- 서버에 보내는 데이터를 POST 요청 본문에 담는다.
- 캐시할 수 없다.
- 브라우저 기록에 남지 않는다.
- 북마크 할 수 없다.
- 데이터 길이 제한이 없다.

#### PUT 요청
- URI에 해당하는 데이터를 새로 만들거나 수정할 때 사용한다.
- POST와 다른 점은 “URI”에 대한 의미가 다르다.
    - POST의 URI는 보내는 데이터를 처리할 리소스를 지칭하며
    - PUT의 URI는 보내는 데이터에 해당하는 리소스를 지칭한다.
- Idempotent

#### PATCH 요청
- PUT과 비슷하지만, 기존 엔티티와 새 데이터의 차이점만 보낸다는 차이가 있다.
    - 일부의 데이터만 수정하고 싶을 때, 사용   
- Idempotent

#### DELETE 요청
- URI에 해당하는 리소스를 삭제할 때 사용한다.
- Idempotent

#### 스프링 웹 MVC에서 HTTP method 맵핑하기
● @RequestMapping(method=RequestMethod.GET)
● @RequestMapping(method={RequestMethod.GET, RequestMethod.POST})
● @GetMapping, @PostMapping, ...

#### 참고
- https://www.w3schools.com/tags/ref_httpmethods.asp
- https://tools.ietf.org/html/rfc2616#section-9.3
- https://tools.ietf.org/html/rfc2068
<br><br>

### HTTP 요청 맵핑하기 - URI패턴 맵핑
#### URI, URL, URN
![springwebmvc](/images/springwebmvc/springwebmvc08-1.png)
- [참고](https://stackoverflow.com/questions/176264/what-is-the-difference-between-a-uri-a-url-and-a-urn)

#### 요청 맵팽하기
- @RequestMapping은 다음의 패턴을 지원합니다.
    - ? : 한 글자 (“/author/???” => “/author/123”)
    - \* : 여러 글자 (“/author/*” => “/author/keesun”)
    - \*\* : 여러 패스 (“/author/** => “/author/keesun/book”)
- @RequestMapping은 클래스에도 선언이 가능하며, 조합도 가능하다.
    ![springwebmvc](/images/springwebmvc/springwebmvc08-2.png)
- 정규표현식 맵핑도 가능하다. (띄어쓰기 주의)
    ![springwebmvc](/images/springwebmvc/springwebmvc08-3.png)
- 패턴이 중복되는 경우
    - 가장 구체적으로 맵핑되는 핸들러를 선택한다.
    ![springwebmvc](/images/springwebmvc/springwebmvc08-4.png)

- URI 확장자 맵핑 지원
    - 스프링 mvc는 기본적으로 지원한다.
        - 예를 들어 "/bong"으로 매핑 시, "/bong.*"을 함께 맵핑
            - "/bong.json", "/bong.html", "/bong.xml"등을 암묵적으로 맵핑해준다.
    - 이 기능은 권장하지 않음 (**스프링부트에서는 기본적으로 이 기능을 사용하지 않도록 설정해준다.**)
        - 보안 이슈(RFD Attack)
            - Reflected File Download
            - https://www.trustwave.com/en-us/resources/blogs/spiderlabs-blog/reflected-file-download-a-new-web-attack-vector/
            - https://www.owasp.org/index.php/Reflected_File_Download
            - https://pivotal.io/security/cve-2015-5211
        - URI변수, Path 매개변수, URI 인코딩을 사용할 때 불명확함.

- 최근에는 accept header에 요청할 파일의 확장자를 설정해준다.
    - header를 통해 응답의 유형을 판단할 수 있다.(권장)
    - request parameter를 통해 설정할수도 있다.(차선책)
        - ex).. "/bong?type=xml"
<br><br>

### HTTP 요청 맵핑하기 - 미디어 타입 맵핑
- 특정한 타입의 데이터를 담고 있는 요청만 처리하는 핸들러
    ![springwebmvc](/images/springwebmvc/springwebmvc08-5.png)
    - @RequestMapping(consumes=MediaType.APPLICATION_JSON_UTF8_VALUE)
        - 문자열을 입력하는 대신 MediaType을 사용하면 상수를 (IDE에서) 자동 완성으로 사용할 수있다.
        - MediaType.APPLICATION_JSON_UTF8_VALUE은 문자열로 직접 넣어줄 수 있으나 type safe하지 않다.
    - Content-Type 헤더로 필터링
    - 매치 되는 않는 경우에 415 Unsupported Media Type 응답
- 특정한 타입의 응답을 만드는 핸들러
    ![springwebmvc](/images/springwebmvc/springwebmvc08-6.png)
    - @RequestMapping(produces=”application/json”)
    - Accept 헤더로 필터링 (하지만 살짝... 오묘함)
    - 매치 되지 않는 경우에 406 Not Acceptable 응답

- 클래스에 선언한 @RequestMapping에 사용한 것과 조합이 되지 않고 메소드에 사용한 @RequestMapping의 설정으로 덮어쓴다.
- Not (!)을 사용해서 특정 미디어 타입이 아닌 경우로 맵핑 할 수도 있다.
<br><br>

### HTTP 요청 맵핑하기 - 헤더와 매개변수
- 특정한 헤더가 있는 요청을 처리하고 싶은 경우
    - @RequestMapping(headers = “key”)
- 특정한 헤더가 없는 요청을 처리하고 싶은 경우
    - @RequestMapping(headers = “!key”)
- 특정한 헤더 키/값이 있는 요청을 처리하고 싶은 경우
    - @RequestMapping(headers = “key=value”)
- 특정한 요청 매개변수 키를 가지고 있는 요청을 처리하고 싶은 경우
    - @RequestMapping(params = “a”)
- 특정한 요청 매개변수가 없는 요청을 처리하고 싶은 경우
    - @RequestMapping(params = “!a”)
- 특정한 요청 매개변수 키/값을 가지고 있는 요청을 처리하고 싶은 경우
    - @RequestMapping(params = “a=b”)
<br><br>

### HTTP 요청 맵핑하기 - HEAD와 OPTIONS 요청 처리

#### 우리가 구현하지 않아도 스프링 웹 MVC에서 자동으로 처리하는 HTTP Method
- HEAD
- OPTIONS

#### HEAD
![springwebmvc](/images/springwebmvc/springwebmvc08-7.png)
- GET 요청과 동일하지만 **응답 본문을 받아오지 않고 응답 헤더**만 받아온다.
    

#### OPTIONS
![springwebmvc](/images/springwebmvc/springwebmvc08-8.png)
- 사용할 수 있는 HTTP Method 제공
- 서버 또는 특정 리소스가 제공하는 기능을 확인할 수 있다.
- 서버는 Allow 응답 헤더에 사용할 수 있는 HTTP Method 목록을 제공해야 한다.

#### 참고
- https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html
- https://github.com/spring-projects/spring-framework/blob/master/spring-test/src/test/java/org/springframework/test/web/servlet/samples/standalone/resultmatchers/HeaderAssertionTests.java
<br><br>

### HTTP 요청 맵핑하기 - 커스텀 애노테이션
#### @RequestMapping 애노테이션을 메타 애노테이션으로 사용하기
- @GetMapping 같은 커스텀한 애노테이션을 만들 수 있다.

#### 메타(Meta) 애노테이션
- 애노테이션에 사용할 수 있는 애노테이션
- 스프링이 제공하는 대부분의 애노테이션은 메타 애노테이션으로 사용할 수 있다.

#### 조합(Composed) 애노테이션
- 한개 혹은 여러 메타 애노테이션을 조합해서 만든 애노테이션
- 코드를 간결하게 줄일 수 있다.
- 보다 구체적인 의미를 부여할 수 있다.

#### @Retention
- 해당 애노테이션 정보를 언제까지 유지할 것인가.
- Source: 소스 코드까지만 유지. 즉, 컴파일 하면 해당 애노테이션 정보는 사라진다는 이야기.
- Class: 컴파인 한 .class 파일에도 유지. 즉 런타임 시, 클래스를 메모리로 읽어오면 해당 정보는 사라진다.
- Runtime: 클래스를 메모리에 읽어왔을 때까지 유지! 코드에서 이 정보를 바탕으로 특정 로직을 실행할 수 있다.

#### @Target
- 해당 애노테이션을 어디에 사용할 수 있는지 결정한다.
- 배열로 여러 엘리먼트도 지정 가능하다.

#### @Documented
- 해당 애노테이션을 사용한 코드의 문서에 그 애노테이션에 대한 정보를 표기할지 결정한다.

#### 메타 애노테이션
- https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#beansmeta-annotations
- https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/annotation/AliasFor.html
<br><br>



