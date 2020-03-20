---
title: while, Do~while, for, 반복문
date: 2018-10-30 16:54:16
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
# KOSTA DAY02
## Java
<!-- more -->
### while
- 조건문의 실행결과가 true일 동안 반복해서 실행

![Java](/images/java/java02-01.png)
<br><br>

### Do while
- 명령문을 먼저 실행 후, 조건비교 후 반복 실행

![Java](/images/java/java02-02.png)

- while 문의 경우 조건을 불만족 시, 실행되지 않을 수 있다.    
반면 Do~while문의 경우 무조건 한번을 실행된다는 차이가 있다.

![Java](/images/java/java02-03.png)

<br><br>

### for
- for반복문은 변수 초기화, 조건식, 증감식이 한줄에 모두 있다.

![Java](/images/java/java02-04.png)

- 초기화식은 최초 한번만 수행
- 조건식을 수행하여 수행결과 false 시, for문 빠져나감
- 증감식 수행

**※무한루프 for, while문**
![Java](/images/java/java02-05.png)
<br><br>

### 반복문
#### break문
![Java](/images/java/java02-06.png)
- while, for, do 문 안에서 사용되면 반복문을 빠져나가는 기능
- switch문 안에서 사용되면 switch 문을 빠져나가는 기능
- return 사용 시 메소드안을 나간다.
- for, while, Do 문 에서 사용시 반복문을 빠져나간다.

![Java](/images/java/java02-07.png)
- 반복문을 여러개 사용 시, 바깥쪽 반복문을 빠져나가기 위해서는 상기 이미지와 같이 사용 가능(반복문 영역에 이름지정후 break 이름;)

#### continue문
![Java](/images/java/java02-08.png)
- 다음번 반복과정을 진행
- continue문도 break문과 마찬가지로 중복되는 반복문에서 바깥쪽 반복문으로 진행을 원할 시, 하기 이미지와 같이 사용할 수 있다.
![Java](/images/java/java02-09.png)

<br><br>