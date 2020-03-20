---
title: TradeFruit_System(Web Mini Project)
date: 2018-12-19 16:39:13
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
## Web Mini Project
### TradeFruit_System
<!-- more -->
- 개선사항이 필요한 웹페이지를 찾아서 배운내용을 토대로 리뉴얼
(시간 관계상 전체 페이지가 아닌 3~4페이지)
- 대상 페이지 : [농산물직거래장터](http://www.guidecrops.com/index.htm)<br>

**Project github : https://github.com/cyr9210/Trade_Fruit_Project**
#### 구현내용
![TradeFruit](/images/Project/TradeFruit/TF01.png)
![TradeFruit](/images/Project/TradeFruit/TF02.png)
![TradeFruit](/images/Project/TradeFruit/TF03.png)
![TradeFruit](/images/Project/TradeFruit/TF04.png)
- 농산물 직거래 장터는 보다시피 오래전에 제작된 웹페이지로 모든 레이아웃이 테이블 단위로 되어있으며, 모든 기능들을 구현 시, 페이지 리로딩이 된다.
- 이 페이지를 ajax를 활용하여 비동기식으로 구현하고, 부트스트랩 레이아웃를 활용해 보다 한눈에 들어오도록 리뉴얼을 해보고자 하였다.

![TradeFruit](/images/Project/TradeFruit/TF05.png)
![TradeFruit](/images/Project/TradeFruit/TF06.png)
![TradeFruit](/images/Project/TradeFruit/TF07.png)
![TradeFruit](/images/Project/TradeFruit/TF08.png)
![TradeFruit](/images/Project/TradeFruit/TF09.png)
![TradeFruit](/images/Project/TradeFruit/TF10.png)
![TradeFruit](/images/Project/TradeFruit/TF11.png)
![TradeFruit](/images/Project/TradeFruit/TF12.png)
- 전체적인 레이아웃을 부트스트랩을 활용하여 구성하였다.
- 하기 프로젝트 설명 주제에서 부트스트랩 활용방법에 대해 알아보자.

![TradeFruit](/images/Project/TradeFruit/TF13.png)
- 상단에 위치한 네비게이션 바를 클릭하였을때, 해당 버튼에 대하여 css가 변경되고, 하단에 위치한 현재위치탭에 현재클릭한 메뉴의 이름이 적용되는 모습을 구현하였다.
- 부트스트랩의 ‘active’ 클래스를 활용하여 네비게이션 바 클릭 시, 전체 버튼에 active 클래스를 제거(removeClass), 클릭한 버튼에 active클래스를 추가(addClass)한다.

![TradeFruit](/images/Project/TradeFruit/TF14.png)
- 전체판매메뉴를 클릭 시, 홈화면의 메뉴들을 hide시키고, 전체판매의 메뉴들을 화면에 명시하도록 구성하였다.

![TradeFruit](/images/Project/TradeFruit/TF15.png)
- 아직 jsp 배우지는 않았지만 수업시간중 샘플로 보여주신 jsp파일을 활용하여 묻고답하기에 방명록형식으로 게시판을 구현하였다.
(Ajax함수 활용)
![TradeFruit](/images/Project/TradeFruit/TF16.png)
- JQuery plugin ui를 사용하여 로그인 버튼 및 회원가입 버튼을 클릭 시 , 흔들리는 이벤트를 구현하였다.

![TradeFruit](/images/Project/TradeFruit/TF17.png)
<br>

### 부트스트랩 활용
- [부트스트랩 공식홈폐이지](http://bootstrapk.com/) → 부트스트랩 다운
css/js파일 및 image 파일이 있다.(.min : 최소화 한것)
- 링크
다운받은 css파일 및 js 파일 연결
![TradeFruit](/images/Project/TradeFruit/TF18.png)
<br>

### 느낀점
이번 프로젝트가 여태껏한 프로젝트들 중 가장 힘들었다.
부트스트랩 사용방법을 배우지 않았기에 찾아보면서 하는게 쉽지 않앗다.
css가 맘처럼 구현되지 않는 답답함과 함께 이벤트들을 구현할 때 마다, 너무 많은 에러들 몸과 마음이 지쳤다.
기한내에 완성하기가 쉽지 않았고 발표 바로 직전까지 작업을 하였다.
이런 힘든사항들이 있었던 만큼 결과물이 만족스럽지 못했고, 다른조들의 발표를 보면서 많이 반성했다.

이번 프로젝트 중 내가 부족하다고 느낀 파트
JQuery 활용 Ajax구현 / Java → Json, Json → Java
<br><br>