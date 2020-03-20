---
title: reference타입, instanceof, 추상클래스, 객체와 인스턴스의 차이
date: 2018-11-09 19:04:23
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
# KOSTA DAY09
## Java
<!-- more -->
### reference타입
메모리상에 할당되는 각각의 공간에 참조를 위한 주소값이 들어가는 타입
![Java](/images/java/java09-01.png)
- String 은 reference타입
- 아무값도 가지지 않는 reference 와 null 값을 가지는 reference는 다르다.
- call by reference : 값에 의한 호출이 아닌 주소값에 의한 호출
<br><br>

### 추상클래스
abstract 키워드가 붙은 클래스

![Java](/images/java/java09-02.png)
- 인스턴스 화를 할 수 없다. (생성자가 없음 → 객체생성의 목적이 없음)
- 추상 클래스는 상속 시, 자식에게 반드시 오버라이딩 하도록 한다.
- 추상 메소드 : 메소드 본체가 없는 메소드 
- 메소드 호출의 목적이 아닌 오버라이딩을 하기위해서 생성한다.
>인스턴스   
객체가 실질적으로 메모리를 차지하는것
객체 생성 → 인스턴스화 한다.

<br>

### instanceof
![Java](/images/java/java09-03.png)
- 인스턴스의 실제 타입을 알아보기 위해서 사용된다.
- 형변환 가능성을 검사하는 키워드
- 주로 조건문에 사용된다.
<br><br>

### 객체와 인스턴스의 차이
![Java](/images/java/java09-04.png)
<br><br>

