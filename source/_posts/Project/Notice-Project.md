---
title: Notice-Project
date: 2019-05-24 10:04:44
tags: 프로젝트_회고
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
# Project
## 공지사항 웹 어플리케이션 
<!-- more -->
### 사용스택
- Java
- Spring(Boot)
- jQuery
- DB - h2(embedded DB)
- JPA
- SpringSecurity
- Oauth2(구글 로그인)
- 템플릿 엔진 - thymeleaf
- 디자인 - 부트스트랩 템플릿 활용
- maven

### 프로젝트 빌드 및 실행방법
- zip파일 다운 또는 clone 후에 아래와 같은 방법으로 실행합니다.
    1. IDE툴 사용하여 Application.class 실행
        ![noticeP](/images/Project/Notice_Project/notice-project1.png)

    2. jar 파일로 실행
        - 메이블 플러그인을 통해 jar파일 생성 
            - clone 또는 압축푼 폴더의 위치를 찾아 아래 명령어 입력  
            - mvn clean
                - target폴더 아래 파일들 삭제
            - mvn package -DskipTests
                - test제외하고 실행 target폴더 아래 jar파일 하나를 생성한다.

        - 실행
            - java -jar (target아래 생성된 .jar파일명)

- 접속url : http://localhost:8080 ([http://localhost:8080/notice/0](http://localhost:8080/notice/0) 로 이동됩니다.)
        
### 기능 구현
- 메인화면, 로그인, 회원가입페이지를 제외하고 모두 로그인 되어야 서비스 이용이 가능하다.(로그인 되어있지 않을 시, 로그인 페이지로 이동된다.)

- 구글 로그인이 가능하다.
- 일반 회원가입 및 로그인이 가능하다.
- 사용자는 텍스트로 된 공지를 추가할 수 있다.
- 사용자는 공지를 수정/삭제할 수 있다.
- 사용자는 공지목록을 조회할 수 있다.
- 조회시 제목, 작성일, 작성자, 최종수정일, 내용이 조회 가능하다.
- 목록은 페이징 기능이 있다.
- 제목/글쓴이로 검색이 가능하다.

### 구현결과
![noticeP](/images/Project/Notice_Project/notice-project2.png)
![noticeP](/images/Project/Notice_Project/notice-project3.png)
![noticeP](/images/Project/Notice_Project/notice-project4.png)
![noticeP](/images/Project/Notice_Project/notice-project5.png)
![noticeP](/images/Project/Notice_Project/notice-project6.png)
![noticeP](/images/Project/Notice_Project/notice-project7.png)


### 회고
#### 로그인
- google 로그인 및 자체 회원가입 및 로그인 기능을 구현을 목표하였다.
- Oauth2를 사용한 구글 로그인을 구현하고 블로깅했던 경험이 있으나, 자체 로그인을 동시구현해본적은 없었다.
    - [Oauth2 - 구글 로그인](https://cyr9210.github.io/2019/04/21/Spring/oauth2-google-login/)
<br>

##### 회고내용
- Security설정 시, form 로그인 및 구글로그인 동시 적용
    ![noticeP](/images/Project/Notice_Project/notice-project9.png)
    - web.ignoring().antMatchers()을 적용하여 security인증을 원하지 않는 페이지 주소를 지정한다.
        - js, css등 정적 리소스에 관하여도 path를 지정해주어야한다.
        - 스프링부트에서 정적 리소스 path는 /resources/static을 생략하고 지정해준다.
            ```
            <link href="/css/sb-admin-2.min.css" rel="stylesheet">
            ```
            <img src="/images/Project/Notice_Project/notice-project10.png" width="40%">
            
    - formLogin()을 사용하여, 뷰페이지 지정(없을 시, 스프링 시큐리티 에서 만든 페이지 적용)
    
    - PasswordEncoder를 빈으로 등록하였다.(굳이 security 설정 class에서 등록할 필요는 없다.)
- UserDetailsService 구현
    ![noticeP](/images/Project/Notice_Project/notice-project12.png)
    - 내가 구현한 회원 객체의 Service 클래스를 UserDetails를 implements한다.
    - loadUserByUsername를 구현한다.
        - return User객체는 스프링 시큐리티에서 만들어 주는 객체이다.
        - authorities()는 권한과 관련되었는데 "ROLE_USER"라는 권한을 가지도록 한다.
            - 자세한 내용은 잘모르겠습니다. 추후에 공부하여 다시 블로깅할 예정입니다.

- 로그인 유저 정보 처리
    - 이전에 로그인 기록이 없는 구글 로그인 발생 시, setAuthenticationSuccessHandler를 다음과 같이 하여 정보를 DB에 저장하였다.
    ![noticeP](/images/Project/Notice_Project/notice-project11.png)
    - 일반 로그인 시, UserDetailsServie에서 Session에 정보를 저장하여 사용할 수 있도록 한다.(구글 로그인 session처리는 이미 적용하였다.)
    ![noticeP](/images/Project/Notice_Project/notice-project12.png)

##### 어려움 및 주의사항
- 로그인 폼 name="username" 으로 줘야한다.
    ![noticeP](/images/Project/Notice_Project/username.png)

##### 보완해야 할 점
- 로그인 정보를 같은 table에 넣어, 구글 로그인 후에 기본 로그인이 가능하다.. 개선이 필요..
- 권한 분리 및 적용
<br><br>

#### thymeleaf 활용
- 타임리프를 처음 사용하여 어려움이 많았습니다.
- 사용 내용들을 정리 해보도록 하겠습니다.

##### 활용내용
- th:each, th:with, th:text, index 활용 
    ```
    <tbody th:with="offset=${list.getPageable().getOffset()}">
        <tr th:each="notice, index : ${list}">
                <td th:text="${list.getTotalElements() - (index.index + list.getPageable().getOffset())}">1</td>
                <td><a th:text="${notice.getTitle()}" th:href="@{'/notice/read/' + ${notice.getId()}}" class="readNoticeBtn">New York</a></td>
                <td th:text="${notice.getWriter().getName()}">Customer Support</td>
                <td th:text="${notice.getCreated()}">2011/01/25</td>
                <td th:text="${notice.getModified()}">2011/01/25</td>
        </tr>
    </tbody>
    ```
    - th:with 지역변수를 설정하여 사용 할 수 있다.
        - 지역변수이다. 위의 예는 tbody태그 밖에서는 사용할 수 없다.(동일레벨 태그도 불가능하다.)
        - **자동완성으로 인해 th:width를 사용하여 오랜시간동안 오류를 찾지 못했습니다... 주의하세요..**
    - th:text 에 지정한 값이 있으면, 해당 내용으로 text값이 출력된다.
        - th:value또한 같은 기능이나, value을 설정
    - th:each를 사용하면 위와같이 collection객체를 받아 그안의 객체들을 없을 때 까지 꺼낼 수 있다.
        - index를 같이 명명 해주면, index 값을 사용할 수 있다.
            - index.index, count, size등을 사용할 수 있다.(index는 0부터 시작, count는 1부터 시작)

- th:if, th:unless, th:block, th:href 활용
    ```
    <th:block th:unless="${first == 0}">
        <li class="paginate_button page-item previous"><a th:href="@{${first - 1}}" class="page-link">Previous</a></li>
    </th:block>
    <th:block th:each="i : ${#numbers.sequence(first, last)}">
        <li class="paginate_button page-item" th:unless="${i >= list.getTotalPages() || i == list.getNumber()}">
            <a class="page-link" th:text="${i + 1}" th:href="@{${i}}">1</a>
        </li>
        <li class="paginate_button page-item active" th:if="${i == list.getNumber() && i < list.getTotalPages()}">
            <a class="page-link" th:text="${i + 1}" th:href="@{${i}}">1</a>
        </li>
    </th:block>
    <th:block th:if="${last+1 < list.getTotalPages()}">
        <li class="paginate_button page-item next"><a th:href="@{${last + 1}}" class="page-link">Next</a></li>
    </th:block>
    ```
    - 위의 예는 타임리프를 사용하여 페이징 처리를 한 것이다.
    - th:block 타임리프 스코프 지정
    - th:if 값이 참일 때, 실행
    - th:unless 값이 거짓일 때, 실행
    - th:href @, $를 사용하여 url을 지정할 수 있다.
    
- 참고 블로그 : [strongstar-스프링부트:타임리프](https://strongstar.tistory.com/17?category=793728)
<br><br>

#### Page 
![Page](/images/Project/Notice_Project/page.png)
- offset() 현재 페이지 시작 엘리먼트 인덱스
<br><br>

#### 검색내용 저장 및 사용
- 검색 내용 페이징 처리 시, 처음 검색내용을 파라미터로 가지는 컨트롤러에서 NullPointException이 발생한다.
이를 해결하기 위해 Session 또는 쿠키를 사용하여 값을 저장하려고 하였다.

- [Cookie VS Session](https://cyr9210.github.io/2019/01/03/JSP/JSP03/#Cookie)
    - 쿠키는 클라이언트 쪽에 저장되어, 보안이 취약하다.
    - 세션은 보안에 강하지만, 세션은 서버에 저장되어 메모리를 차지하기 때문에 많은 자료가 쌓이면 문제가 생길 수 있다.

- 검색내용은 로그인 정보와 같은 보안이 중요한 사항이 아니라고 판단하여 쿠키를 사용하기로 결정하였다.

##### 쿠키저장
![noticeP](/images/Project/Notice_Project/notice-project14.png)
- 키워드를 쿠키에 저장 후, checkbox 체크값에 따라 제목, 글쓴이, 제목/글쓴이로 검색하는 컨트롤러로 매핑

##### @CookieValue
![noticeP](/images/Project/Notice_Project/notice-project13.png)
- HTTP쿠키 값을 HttpServletRequest등을 통해 읽을 필요없이 스프링 컨트롤러에서 파라미터로 전달 받을 수 있게해준다. 
    - required 속성 값을 이용해 필수여부 설정 (기본값은 true: 쿠키값없을 시, 에러발생)
    - defaultValue 속성 값을 이용해서 기본값을 지정
<br><br>

#### Js cookie
- 자바스크립트에서 쿠키를 보다 편하게 사용할 수 있도록 도와주는 라이브러리
- https://github.com/js-cookie/js-cookie

##### 쿠키 저장
![getCookies](/images/Project/Notice_Project/getCookies.png)

##### 쿠키 사용
![setCookies](/images/Project/Notice_Project/setCookies.png)




    
    