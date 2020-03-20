---
title: section, article, css, 선택자, float, 박스모델, 포지셔닝
date: 2018-12-10 01:09:51
tags: Web
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
![html](/images/html_logo.jpeg)
# KOSTA DAY29
## Web
<!-- more -->
### section
#### HTML을 만들때 주의할점
- 웹표준을 지켜야 한다.
- 시멘틱한 웹이어야 한다. (메타태그 및 헤드태그를 사용)

#### 시멘틱태그
- 좀 더 문서의 의미를 강조
- div태그 만으로 분류하는 것은 의미 부여가 약함
![web](/images/web/Web02-01.png)

![web](/images/web/Web02-02.png)
- section : 제목과 단락으로 나누어질 수 있는 경우 사용
- article : 독립적인 내용을 가지고 있을 경우 사용 (예를들어 댓글, 기사 등)
- 주관적으로 나누어질 수 있기 때문에 사용자의 마음
- <div\>는 여전히 많이 사용됨

#### 예제
![web](/images/web/Web02-03.png)
- section태그를 사용하여 분류하였다.

![web](/images/web/Web02-04.png)
- article 태그 사용하여 분류
<br><br>

### CSS
- 인라인 스타일 적용
![web](/images/web/Web02-05.png)

- 내부스타일 적용
![web](/images/web/Web02-06.png)

- 외부링크 스타일적용(가장많이쓰임)
![web](/images/web/Web02-07.png)

- 외부 스타일 적용 (import)
![web](/images/web/Web02-08.png)
<br><br>

### 선택자
#### 일반선택자/복합선택자
![web](/images/web/Web02-09.png)
- 클래스 선택자, ID선택자 많이 활용됨
- 하위선택자, 자식선택자 많이 활용됨
(하위는 자손까지/자식선택자 자식까지만 선택된다.)
- 빨간박스의 내용은 JQUERY에서도 사용됨으로 잘 숙지하는것이 좋다.

#### 속성선택자
![web](/images/web/Web02-11.png)

#### font속성
![web](/images/web/Web02-12.png)

#### text속성
![web](/images/web/Web02-13.png)

#### 인터페이스 속성
![web](/images/web/Web02-14.png)
- display : none; → 화면에서 안보이도록 한다.
<br><br>

### float
박스가 화면의 어느 위치에 배치할 것인지 설정하기 위해 사용한다.

![web](/images/web/Web02-15.png)
- 이미지를 인라인 형식처럼 한쪽으로 보냄
- clear : 제외시킴
- margin등의 조건 시, 주의(float대상 크기도 고려해야한다.)
<br><br>

### 박스모델
![web](/images/web/Web02-16.png)
- box : width, height
- 여백 : padding, margin
- 성질 : display, float, position
<br><br>

### 포지셔닝
- static : 기본값으로 일반적인 내용물의 흐름 상단과 좌측에서의 거리를 지정할 수 없다.
- relative : 상대적인 top, left 거리를 지정(**원래있어야할 위치 기준**)
![web](/images/web/Web02-17.png)

- absolute : **자신의 상위 box속에서** top, left, right, bottom등의 절대적인 위치를 지정(전체를 기준)
![web](/images/web/Web02-18.png)

- relative는 다른 박스에 영향을 끼치지 않고, absolute는 영향을 끼친다.

#### 예제
![web](/images/web/Web02-19.png)
![web](/images/web/Web02-20.png)
<br>