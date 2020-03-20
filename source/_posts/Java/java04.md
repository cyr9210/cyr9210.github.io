---
title: 배열, 정렬, 배열복사
date: 2018-11-02 17:21:33
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
# KOSTA DAY04
## Java
<!-- more -->
### 배열
- 열의 생성과 선언에 관련하여 수강하였다.
- 향상된 for 문

```
for(데이터타입 변수명 : 배열이름){   
실행문 변수명 일력시 배열값 순서대로 호출(배열의 크기만큼)   
}
```
![Java](/images/java/java04-01.png)
- 향상된 for문 구현방법
- length메소드 사용방법
- 배열의 index값은 0 부터 시작됨
<br><br>

### 정렬
![Java](/images/java/java04-02.png)
- swap 메소드를 생성하여 배열의 순서를 정렬

![Java](/images/java/java04-03.png)
- 2차, 3차 배열도 생성이 가능하다.
- 3차이상의 경우 사용자가 사용하기 힘들어 거의 사용하지 않는다.
<br><br>

### 배열복사
![Java](/images/java/java04-04.png)
- System.arraycopy(복사할 배열, 복사시작index, 붙여넣기 배열, 붙여넣기 시작 index, 붙여넣기 끝 index)   
또는 for문 사용하여 복사
<br><br>