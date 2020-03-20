---
title: JSON, JunitTest
date: 2018-11-19 13:29:49
tags: Java
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

![Java](/images/javaimage.png)
# KOSTA DAY15
## Java
<!-- more -->
### JSON
Javascript 객체 문법을 따르는 문자 기반의 데이터 포맷
- JSON은 경량의 data 교환 형식
- Javascript에서 객체를 만들 때, 사용하는 표현식
- 문자열 형태로 존재
- JSON표현식은 사람과 기계 모두 이해하기 쉬우며 용량이 작아서, 최근에는 XML을 대체해서 데이터 전송등에 많이 쓰임
- 특정 언어에 종속되지 않으며, 대부분의 프로그래밍 언어에서 JSON포맷의 데이터를 핸들링 할 수 있는 라이브러리를 제공

#### JSON라이브러리 추가
![Java](/images/java/java15-01.png)
1. SON라이브러리 다운로드
2. 추가할 프로젝트에서 우클릭 → Build path → Configure Build Path…

![Java](/images/java/java15-02.png)
3. JavaBuildPath → Add Library ||Add External JARs → JSON라이러리추가

**❖Maven, gradle을 통해서도 라이브러리를 추가 할 수 있다.**

#### JSON 사용법
![Java](/images/java/java15-03.png)
- JSONObject 객체생성
- JSONArray에 JSONObject객체 담기
- JSONArray → JSONcode
- List → JSONcode
- JSONArray 생성 및 추가시 simple라이브러리를 사용했으나,
- List에서 JSONcode로 변환 시, net 라이브러리 사용

### Junit Test
>**xUnit이란?**   
자바만 단위 테스팅 프레임 워크인 JUnit만 있는게 아니다. 다른 언어도 단위 테스트를 위한 프레임워크가 존재하며 보통 이름을 xUnit이라 칭한다.
ex) JUnit → java, CUnit → C ,CppUnit →c++

![Java](/images/java/java15-05.png)
- 해당프로젝트 우클릭 → new Source Folder → 생성된 폴더 → new package → 생성된 패키지   
→ new JUNIT Test Case
![Java](/images/java/java15-04.png)
- assertNotNull(~) : ~ 는 null값이 나오면 비정상
- assertEquals(*, ~) : *과 ~의 값이 같지 않으면 비정상

![Java](/images/java/java15-06.png)
![Java](/images/java/java15-07.png)
- 정상 : 초록색   
- 비정상 : 빨간색
<br><br>