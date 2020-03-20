---
title: 객체지향, 생성자, 객체 내 배열
date: 2018-11-06 17:43:45
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
# KOSTA DAY06
## Java
<!-- more -->
### 객체지향
객체들간의 상호작용을 하는 현상

#### 특징
- 확장성
- 상호작용
- class가 필요하다.
- class는 객체의 모양을 갖추고 있어야 한다.
- new 연산자를 통하여 객체를 생성한다.
<br><br>
### 생성자
- new 연산자와 함께 자동으로 호출
- 객체를 초기화 하는데 사용
- 객체를 생성하는 가이드라인이 될 수 있다.
#### 특징
- return, void를 사용하지 않는다.
- 자신의 class이름과 반드시 동일해야한다.
- public class_name () {} 를 사용하여 default오류를 대비
<br><br>

### 객체 내 배열
객체 메소드 내에 배열을 생성 하는것

#### 생성방법
```
class_name arr[] = {new class2_name(parameter1, parameter2)…};
```

<br><br>
