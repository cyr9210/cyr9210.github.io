---
title: 접근제어자, Exception
date: 2018-11-07 17:49:08
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
# KOSTA DAY07
## Java
<!-- more -->
### 접근제어자
멤버 또는 클래스에 사용되어 해당 멤버 및 클래스를 외부에서 접근하지 못하도록 제한하는 역할을 함.

- 객체지향언어의 주요 특성중 하나인 캡슐화 및 정보숨김을 구현
- public : 모든 클래스에 의해 접근 가능
- protected : 동일 패키지 및 하위클래스 관계 접근 가능
- private : 자기 자신 클래스 내에서만 접근가능
- (default) : 동일 패키지 접근가능 
- 접근제어자 설정X
<br><br>

### Exception
자바에서 에러를 지칭하는 용어

![Java](/images/java/java07-01.png)
- try문 : Exception 처리에 사용되는 명령문

![Java](/images/java/java07-02.png)
- throw문 : 예외를 사용자가 고의로 발생시킨다.   
( ex) throw new Exception)
- throws문 : 발생된 예외를 처리하지 않고 상위 메소드로 전달


<br><br>



