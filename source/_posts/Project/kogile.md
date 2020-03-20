---
title: Project_kogile
date: 2019-02-27 16:44:14
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
## KOSTA 최종프로젝트
### Kogile
**Project github : https://github.com/cyr9210/Project_kogile_Final**
<!-- more -->
#### 구현내용

##### 로그인, 회원가입
![kogile](/images/Project/kogile/kogile_project26.png)

![kogile](/images/Project/kogile/kogile_project27.png)
- 카카오 로그인 및 회원가입이 가능하다.
<br><br>

##### 프로젝트
![kogile](/images/Project/kogile/kogile_project28.png)

![kogile](/images/Project/kogile/kogile_project38.png)
- 로그인된 아이디가 참가하고 있는 프로젝트 목록을 보여준다.
- 생성할 수 있다.
- 프로젝트의 정보를 확인, 수정, 삭제할 수 있다.
<br><br>

##### 포스트 이동
![kogile](/images/Project/kogile/kogile_project29.png)

![kogile](/images/Project/kogile/kogile_project38.png)

- 해당 작업을 이동할 수 있다.
- DB에도 반영된다.
<br><br>

##### 체크리스트
![kogile](/images/Project/kogile/kogile_project30.png)

![kogile](/images/Project/kogile/kogile_project31.png)

![kogile](/images/Project/kogile/kogile_project32.png)
- 상세보기에서 체크리스트를 추가할 수 있다.
- 체크리스트에 리스트를 추가 할 수 있다.
- 체크된 작업을 계산하여 완성률을 보여준다.   
(모달창과 상세보기 페이지 모두에서 확인 가능하다.)
<br><br>

##### 라벨
![kogile](/images/Project/kogile/kogile_project33.png)
- 프로젝트 생성 시, 각 칼라별 라벨이 기본적으로 생성된다.(4개)
- 라벨을 추가할 수 있다.(프로젝트 별)
- 라벨을 클릭 시, 라벨을 가시적으로 표현할 수 있다.   
(모달창과 상세보기 페이지 모두에서 확인 가능하다.)
<br><br>

##### 마감일
![kogile](/images/Project/kogile/kogile_project34.png)

![kogile](/images/Project/kogile/kogile_project35.png)
- 마감일을 등록할 수 있다.
- 삭제, 수정이 가능하다.
- 메인 화면에서 D-day까지 남은일을 확인 할 수 있다.
<br><br>

##### 공지사항
![kogile](/images/Project/kogile/kogile_project36.png)

![kogile](/images/Project/kogile/kogile_project37.png)
- 기본적인 게시판을 구현하였다. (프로젝트 별 소유)
<br><br>

##### 검색
![kogile](/images/Project/kogile/kogile_project39.png)

![kogile](/images/Project/kogile/kogile_project40.png)

![kogile](/images/Project/kogile/kogile_project41.png)

![kogile](/images/Project/kogile/kogile_project43.png)
- 프로젝트 및 회원을 검색할 수 있다.
- 프로젝트를 클릭 시, 해당 프로젝트로 이동한다.
- 회원을 클릭 시, 해당 회원을 현재 프로젝트에 초대한다.
- 초대된 회원은 알림이 발생한다.
<br><br>

##### 댓글(태그) 및 알림
![kogile](/images/Project/kogile/kogile_project44.png)

![kogile](/images/Project/kogile/kogile_project45.png)
- 댓글 등록 후, 태그 input란에 현 프로젝트원을 입력 시, 자동완성이 된다.
- 태그된 회원은 알림이 발생한다.
- 확인한 알림은 확인상태로 변경된다.
<br><br>

### 회고

#### gitignore
Project에 원하지 않는 Backup File이나 LogFile, 혹은 컴파일 된 파일들을 Git에서 제외시킬수 있는 설정 File이다.

- gitignore파일 생성
```
touch .gitignore
```

- [gitignore.io](www.gitignore.io)에 접속하여, 언어 및 IDE등을 입력하면 자동으로 파일내용을 만들어준다.

- 만약 git 사용도중에 gitignore설정 시, 아래와 같이 실행한다.
    - 전체 내용 및 캐쉬삭제 후, 다시 업로드
    ```
    git rm -r --cached
    git add .
    git commit -m "commit message"
    git push origin <branch_name>
    ```
OS 및 툴을 다양하게 사용하고 있던 우리조는 gitignore를 설정하지 않아 어려움을 겪었다.
각자의 브랜치를 만들어서 작업도중에 이러한 사실을 알게되어, 모든 조원의 브랜치를 merge한 후, gitignore를 설정해줌으로써 해결하였다.

#### UNION 쿼리문 활용
![kogile](/images/Project/kogile/kogile_project46.png)
- union : 합집합 중복된 행은 제거
- union all : 합집합 중복된 행까지 포함

union 쿼리문을 사용하여 일반가입회원과 카카오톡으로 가입한 회원을 합하여 사용하였다.

#### Oracle Hint
![kogile](/images/Project/kogile/kogile_project48.png)
- 여러개의 테이블을 join하여 사용 시, Hint절에 alias 이름을 사용해야 한다.
- 원하는 순서대로 정렬하기 위해서는 메인 테이블을 기준으로 여러개의 hint를 줘야 한다.
- 참고 블로그 : [양군의 놀이터](https://yanggoony.tistory.com/24)

#### 한글처리
![kogile](/images/Project/kogile/kogile_project49.png)
- 필터 매핑을 반드시 해줘야한다.

#### focus 이벤트
버블링 발생안함
- focus : 포커스 될 때, 이벤트 실행
- blur : 포커스에서 벗어날 때, 이벤트 실행 
<br>

버블링 발생
- focusin : 포커스 될 때, 이벤트 실행
- focusout : 포커스에서 벗어날 때, 이벤트 실행

##### 예제
![kogile](/images/Project/kogile/kogile_project64.png)

![kogile](/images/Project/kogile/kogile_project65.png)


#### 동적 생성된 tag에 대한 이벤트
- 동적으로 생성된 tag들은 이벤트가 먹지 않는 경우가 있다.
- document 객체를 기준으로 이벤트를 줄 경우 해결이 가능하다.
![kogile](/images/Project/kogile/kogile_project49.png)

#### .closest() vs .parents()
- .closest()
    - 현재 요소부터 시작
    - 제공된 선택자와 일치하는 요소를 찾을 때까지 DOM트리를 탐색
    - 없거나 하나의 요소(zero or one)를 포함한 jQuery객체를 반환
- .parents()
    - 부모 요소부터 시작
    - 문서의 root요소부터 DOM트리를 탐색
    - 각 부모 요소들로 임시 집합을 구성하고 제공된 선택자에 의해 임시 집합에서 추출
    - 없거나 하나 이상의 요소(zero or multiple elements)를 포함한 jQuery객체를 반환

#### 문자 date 타입변환
- String to Date
```
String fromString = "2013-04-08 10:10:10";
SimpleDateFormat transFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
Date toDate = transFormat.parse(fromString);
```

- Date to String
```
Date fromDate = new Date();
SimpleDateFormat transFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
String toString = transFormat.format(fromDate);
```
- JsonFormat 어노테이션 활용하면 해당 객체로 들어오는 Json객체에 대하여 형변환을 해준다. (**양방향**,  String ↔︎ Date)
![kogile](/images/Project/kogile/kogile_project71.png)
    - timezone으로 현재 위치 설정

#### 빽팁(`)활용
![kogile](/images/Project/kogile/kogile_project73.png)
- url, tag 등을 작성시 빽팁(`)을 활용하면 jQuery문법처럼 편하게 사용이 가능하다.
- explorer는 지원하지 않는다.

#### Json객체 변환
![kogile](/images/Project/kogile/kogile_project54.png)

![kogile](/images/Project/kogile/kogile_project55.png)

![kogile](/images/Project/kogile/kogile_project56.png)
- ajax 시, 해당 설정 반드시 해주어야 한다.

#### 이벤트 적용 팁
포스테 제목 변경에 관한 이벤트 적용을 좋은예로 볼 수 있다.
![kogile](/images/Project/kogile/kogile_project57.png)

![kogile](/images/Project/kogile/kogile_project58.png)

![kogile](/images/Project/kogile/kogile_project59.png)
- isUpdate라는 상태값을 선언
- 클릭 시, 상태값을 true로 변경
- focusout 또는 keyCode13(enter)을 눌렀다가 뗄 때 false로 다시 변경
- 상태가 true일 때, 실행되는 메소드를 정의하고 focusout, 13keyup에 메소드를 실행하도록 한다.

#### form태그의 serialize
- serializeObject 함수 정의 
    - json객체로 serialize해준다.
    ![kogile](/images/Project/kogile/kogile_project60.png)
- 사용
![kogile](/images/Project/kogile/kogile_project61.png)

#### auto complete 사용 시, 모달창에 가려져 안나올 경우 해결방법
![kogile](/images/Project/kogile/kogile_project66.png)
- appendTo 사용하여 autoComplete를 앞으로 불러온다.

#### SessionAttributes
##### Session 저장
![kogile](/images/Project/kogile/kogile_project67.png)

![kogile](/images/Project/kogile/kogile_project68.png)<br>
##### Session 사용
![kogile](/images/Project/kogile/kogile_project69.png)

![kogile](/images/Project/kogile/kogile_project70.png)

#### ResponseBody 한글처리
```
@RequestMapping(value="/add", produces="application/text; charset=utf8")
public @ResponseBody String add(){
    return "성공";
}
```
- produces를 활용하여 명시적으로 인코딩
<br><br>

### 느낀점
kosta의 교육이 끝났다.
4개월이란 짧은 시간동안 많은 것을 배웠고, 최종 프로젝트를 통해서 활용해 볼 수 있었다.
처음엔 어떻게 해야하나, 감도 잡히지 않았는데.. 조원들도 잘 참여해주고, 의견도 많이 내줘서 나름 괜찮은 결과물을 만들 수 있었던 것 같다. (프로젝트 우수상도 받았다.)
<img src="/images/Project/kogile/kosta_projectaward.png" width="40%">
교육을 통해서 배운것도 많지만, 좋아하는 일을 찾게 된 것 같아 값진 교육이었다고 생각한다.😀
이제 교육이 끝났지만 앞으로가 더 중요하다고 생각한다.                                                     
공부할게 너무 너무 많이 남아있다. 끝났다고 게을리하지않고 매일 꾸준히 노력할 것이다.
좋아하는 일을 찾은 만큼 노력해서 좋은 결실을 맺을 것이다.
<br><br>