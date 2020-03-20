---
title: 면접 후기01
date: 2019-05-28 16:46:11
tags: Interview
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
# 면접 후기
## 웹 백엔드 개발자 지원
<!-- more -->
처음으로 개발회사에서 면접을 보게되었고, 면접을 마치고 후기를 씁니다.
면접은 기초적인 질문들과 과제 프로젝트관련 질문들이 있었습니다.
면접 시, 정말 좋았던건 면접관님들께서 매우 친절했으며, 과제프로젝트에 관련하여 질문만 해주신것이 아닌 고쳐야 할 점에대해 코드리뷰를 해주었다는 점입니다.
면접은 잘 못 본 것 같지만.. 좋은 경험이 되었습니다. 😂

---

### 기초 질문

#### 접근 제어자
- 멤버 또는 클래스에 사용되어 해당 멤버 및 클래스를 외부에서 접근하지 못하도록 제한하는 역할을 함.
- 객체지향언어의 주요 특성중 하나인 캡슐화 및 정보숨김을 구현
- public : 모든 클래스에 의해 접근 가능
- protected : 동일 패키지 및 하위클래스 관계 접근 가능
- private : 자기 자신 클래스 내에서만 접근가능
- **(default) : 동일 패키지 접근가능**

default 접근제어자가 왜 생각이 안났는지... 
<br><br>

#### Map과 List에 관하여..
##### Map
- 자료형으로서 대응관계를 쉽게 표현할 수 있도록 해준다.
- Key와 Value라는 것을 한 쌍으로 가진다.
- 순차적으로 해당 요소 값을 구하지 않고 Key를 통하여 Value를 얻는다.

##### List
- ArrayList와 LinkedList가 있다.
    - ArrayList
        - 데이터 타입이 필요 → 제네릭 선언 : 코드의 일관성/사용 용이
        - 크기는 사용에 따라 증가
        - 삭제 시, 알아서 자리 채움
    - LinkedList
        - 인접데이터를 가르키는 식으로 구현
        - 이웃해서 저장하지 않고 참조하여 사용하여 연결되어 있다. 
        - 주소값으로 연결

##### Linked list 와 Array List를 어떤 상황에서 쓰는지..
- ArrayList : index값으로 저장 전체를 순서대로 출력에 용이
    - 추가 삭제에 용이하지 않은 이유 : 삭제시 어떠한 인덱스 값이 비게 되면 안됨으로 재배치 관련하여 메모리를 사용하게 된다. 
- LinkedList : 전후 노드의 주소값과 연결되어저장 리스트 추가 삭제에 용이
<br><br>

#### query에서 group by절 사용 시 조건문에 붙여야하는 문법
- HAVING

### [NoticeProject](https://cyr9210.github.io/2019/05/24/Project/Notice-Project/) 관련 질문

#### 패키지명
- 앞에 대문자를 썼는데 패키지명은 모두 소문자여야한다.

#### 페이징 처리 ..
- 해당 페이지의 시작 인덱스를 구해보세요.
    - (해당페이지 -1) * 페이지사이즈
이걸 왜 대답못했는지 모르겠다..

#### SessionContants
```
public interface SessionConstants {
    String LOGIN_USER = "LOGIN_USER";
}
```
- 이렇게 구현한 이유가 뭐죠? interface인데 이거 다른위치에서 수정이 가능하지 않나요?
    - 수정이 불가능하다.
        - interface의 모든 멤버 변수는 public static final 이어야한다. 단, 이를 생략할 수 있다.
        - interface의 모든 메소드는 모두 public abstract 이어야한다. 단, 이를 생략할 수 있다.
            - static 메소드 와 default 메소드는 제외한다.(선언 시)
    - class로 public static final로도 구현 할 수 있다.

#### @EnableJdbcHttpSession
```
@EnableJdbcHttpSession
public class HttpSessionConfig {
}
```
- 해당 설정을 한 이유는?
    - Session값을 DB에 저장하기 위해..
몰랐다.. 굳이 구글 로그인 유저를 Account에 저장할 필요가 없었던거 같다..

#### @Autowired , 생성자 주입
- @Autowired 및 생성자로 빈을 주입하는 방식 둘 다 썼는데 코드의 통일성을 위해 하나로 통일하는 것이 좋다.
- 생성자를 사용하여 주입하는 것이 스프링 권장사항이다. (스프링 4부터..)
<br><br>

---
쉬운 질문에 대해서도 대답을 잘하지 못해서 .. 너무 아쉬웠습니다.
알고 있다고 다 대답할 수 있는것은 아니란것을 .. 깨달았습니다..😂