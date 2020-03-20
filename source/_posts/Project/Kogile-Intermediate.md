---
title: Project_Kogile_JSP
date: 2019-01-23 04:10:36
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
## KOSTA 중간 프로젝트
### Kogile
**Project github : https://github.com/cyr9210/JSP_Project_kogile**
<!-- more -->
#### 주제선정
우리 조의 중간 프로젝트 주제는 업무협업서비스를 선택하였다.
우리조가 생각한 협업서비스란 업무의 완료 여부 및 상태와 진행률등 여러 정보들을 한눈에 보기 쉽게 표현해주며 앞으로의 업무파악을 보다 쉽게해주기 위해서 제공하는 서비스이다.

주제를 협업서비스로 정한 이유는 많지는 않지만 미니 프로젝트를 진행하면서, 협업의 중요성을 깨달았으며 서로간의 커뮤니케이션 및 정보 전달들이 힘들었다는 생각이들었고 우리가 힘들었던 이 점을 우리가 프로젝트를 통해 해결 방안을 만들어보자는 생각에 선정하였다.

#### 요구분석
![KogileJSP](/images/Project/JSP_Kogile/Kogile_JSP_01.png)
![KogileJSP](/images/Project/JSP_Kogile/Kogile_JSP_02.png)

#### UseCase
![KogileJSP](/images/Project/JSP_Kogile/Kogile_JSP_03.png)

#### 명명법
![KogileJSP](/images/Project/JSP_Kogile/Kogile_JSP_04.png)
![KogileJSP](/images/Project/JSP_Kogile/Kogile_JSP_05.png)
![KogileJSP](/images/Project/JSP_Kogile/Kogile_JSP_06.png)

#### 레이아웃
![KogileJSP](/images/Project/JSP_Kogile/Kogile_JSP_07.png)
![KogileJSP](/images/Project/JSP_Kogile/Kogile_JSP_08.png)
![KogileJSP](/images/Project/JSP_Kogile/Kogile_JSP_09.png)

#### 아키텍쳐 구조
![KogileJSP](/images/Project/JSP_Kogile/Kogile_JSP_10.png)

#### ER다이어그램
![KogileJSP](/images/Project/JSP_Kogile/Kogile_JSP_11.png)

<br>

### 회고
#### ajax

![KogileJSP](/images/Project/JSP_Kogile/Kogile_JSP_12.png)
- success를 대신하여 promise 사용 
→ .then(function(res){ 성공시 실행내용})
res = response로 받아온 객체
→ .catch(function(err){ 실패시 실행내용})
err = error내용
- console.log(res)를 통해 데이터 타입을 확인하는것이 추 후 실행내용을 작성시 편리하였다.

![KogileJSP](/images/Project/JSP_Kogile/Kogile_JSP_13.png)
- const data = {} 으로 전달 data를 미리 선언해주는것이 파악하기 좋다.
- dataType 대소문자 주의

#### 다중 insert
insert all을 통해 데이터를 한번에 여러개를 삽입하였다.

![KogileJSP](/images/Project/JSP_Kogile/Kogile_JSP_14.png)
- 한번실행에 한번의 seq만 실행됨으로 INCREMENT BY 4; 를 설정

#### view에서 this를 사용하여 값 가져오기
![KogileJSP](/images/Project/JSP_Kogile/Kogile_JSP_15.png)
- 클릭한 태그의 자식 중 select_pno 클래스의 value 값을 가져온다.

#### form 데이터를 ajax로 넘기기
![KogileJSP](/images/Project/JSP_Kogile/Kogile_JSP_16.png)

![KogileJSP](/images/Project/JSP_Kogile/Kogile_JSP_17.png)
- 쌍따옴표 사용 $(“”)
- serialize(); 를 통해 json 객체화

#### 날짜 및 날짜계산
![KogileJSP](/images/Project/JSP_Kogile/Kogile_JSP_18.png)
![KogileJSP](/images/Project/JSP_Kogile/Kogile_JSP_19.png)
![KogileJSP](/images/Project/JSP_Kogile/Kogile_JSP_20.png)
![KogileJSP](/images/Project/JSP_Kogile/Kogile_JSP_21.png)
![KogileJSP](/images/Project/JSP_Kogile/Kogile_JSP_22.png)
![KogileJSP](/images/Project/JSP_Kogile/Kogile_JSP_23.png)
- 두가지 방법이 있다.
    - jsp → useBean 액션태그로 Date객체 생성/ formatDate 선택
    - js → new Date 객체생성 및 set
<br><br>

### 느낀점
너무 빠르고 힘든 2주일이 지났다.
Spring을 제외하고 배웠던 모든것들을 활용하여 프로젝트다운 프로젝트를 진행햐였다.
Git Master를 맡아 git을 통해 팀원들과 내용을 공유하고, 버전관리를 하였는데 잘 되지않아 작업내용을 날려서백업내용을 불러오기도 했고 git 때문에 정신적으로 너무나 힘들었다. 최종프로젝트에는 반드시 git을 제대로 배워서 관리해야겠다는 생각을 했다. (터미널로 하고싶다.)
조장으로서 팀원들과 의사소통이 부족했던것 같다.
전공자 또는 경력자들이 조장인 다른조에 비해 너무 부족했기 때문에, 팀원들이 많이 고생한 것 같다. 이런 프로젝트를 해본 경험이 없었기때문에 무엇을 해야할지 잘 몰랐고, 자신있게 의견을 제시하거나, 업무분담을 제대로 해주지 못한것 같고, 때문에 많이 진행이 늦었던 것 같다.
최종프로젝트에 들어가게 되면 spring을 적용하여 이번 중간프로젝트에 진행한 내용들을 완성시켜야 한다. 지금은 다른조들에 비해 많이 부족하지만 그때는 다른조들 보다 더 좋은 결과물들을 만들어 내고 싶다.
<br><br>