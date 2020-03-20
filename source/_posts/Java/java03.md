---
title: Method, 배열
date: 2018-10-31 17:08:40
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
# KOSTA DAY03
## Java
<!-- more -->
### Method
필드가 물체의 상태라면, 물체의 행동에 해당하는게 메소드이다.

![Java](/images/java/java03-01.png)
![Java](/images/java/java03-02.png)
- 메소드는 입력값이 있고, 그 입력값을 받아서 무언가 한 다음 결과를 도출해 내는 수학의 함수와 비슷한 개념
- 이 때, 입력값을 매개변수(Parameter), 결과값을 리턴값 이라고 한다.
- 메소드란 클래스 가지고 있는 기능(클래스 안에서 선언됨.)
- 메소드화를 사용하는 이유는 중복 코드를 최소화 하고, 유지보수를 보다 용이하게 하기 위해서 이다.

#### 배열
배열은 같은 데이터 타입을 가진 연속된 메모리 공간으로 이루어진 자료구조이다.

![Java](/images/java/java03-03.png)
![Java](/images/java/java03-04.png)
- 주소값 사용(reference 타입)
- 같은 데이터 타입을 가진 여러개의 변수가 필요할 때 사용한다.
- 한 번 생성하면 배열의 크기 변경 불가
- 배열선언 및 생성 (상기이미지)
- 배열 선언과 생성을 동시
- int[] arr = new int[5];
- 배열 선언과 생성, 초기화를 동시 진행
- int[] arr = {1, 2, 3, 4, 5};