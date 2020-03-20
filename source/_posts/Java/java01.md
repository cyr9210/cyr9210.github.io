---
title: 변수, 데이터 종류, 연산자, if/else, switch
date: 2018-10-29 09:00:00
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
# KOSTA DAY01
## Java
<!-- more -->
### 변수
#### 변수란?
변수란 값을 저장할 수 있는 메모리 공간을 의미한다.
(데이터를 담는 그릇)
#### 변수의 선언
데이터 타입 선언 후, 변수명 지정(ex) int n, String name, Double pi
#### java 식별자 명명규칙
명명규칙 : 변수의 이름을 정할 때, 자바언어에서 정해놓은 규칙
식별자(identifier) : 클래스, 메소드, 변수 등 다양한 대상에 대해 이름이 붙어지는 경우, 그 이름을 식별자라고 한다.
하나 이상의 글자로 이루어져야함.
첫번째 글자는 문자이거나 ‘\’ , ‘_’ 이어야함.
두번째 이후의 글자는 숫자, 문자, ‘\’, ‘_’ 이어야함.
길이제한이 없음.
키워드는 식별자로 사용할 수 없음.
상수 값을 표현하는 단어 ‘true’, ‘false’, ‘null’은 식별자로 사용할 수 없음
변수 명명 관례
첫번째 문자가 소문자인 명사로 정함
여러 단어로 구성된 이름의 경우 두번째 단어부터 첫글자를 대문자로 함
‘_’을 쓰지 않음
※관례는 지키지 않아도 컴파일에러가 발생하지 않으나, 약속이기 때문에 지 켜주는 것이 좋다.
<br><br>

### 데이터 종류
#### 기본형타입
가장 기본이 되는 데이터 타입으로써 정수형, 실수형, 문자형, 불린형을 의미

- 정수형 : Byte, Short, int(4 byte), long(8 byte)
- 실수형 : Float(4 byte), double(8 byte)
- 문자형 : char (2 byte)
- boolean형 : true or false 중 한가지 값을 가질 수 있다. ( 1 byte)
**※String은 레퍼런스 타입**

- Byte → Short → int →long → float → double 
- 우측으로 갈수록 큰범위를 가짐
- 형변환(Casting)을 통해 데이터 타입의 변경이 가능하다.
- 크기가 더 큰 타입을 작은 타입으로 바꿀 때에는 명시적으로 변환해야한다. 이것을 ‘강제형변환’ 이라고 한다.
<br><br>


### 연산자
#### 산술연산자
산술을 연산할수 있는 연산자

+, -, *, /, %
#### 증감연산자
- 1씩 증가 or 감소 시키는 연산자

![증감연산자](/images/java/java01-01.png)
- 위의 예제와 같이 전치, 후치 에 따라서 값이 달라질 수 있다.

#### 삼항연산자

![삼항연산자](/images/java/java01-02.png)
<br><br>

### if/else
조건식의 연산 결과에 따라 블록 내부 문자의 실행 여부를 결정 할 수 있다.

#### if문
- 조건식이 true일 경우에만 실행문이 실행
- {}를 생략가능하나, if문 다음 한 줄만 포함된다.

#### if/else문
- 조건식이 true일 경우, if 블록의 실행문이 실행되고, false일 경우 else블록의 실행문이 실행된다.

#### if/else if/else문
![if/esle](/images/java/java01-03.png)
- 처음 if 문의 조건식의 조건문이 true일 경우 if문의 실행문이 실행
- false일 경우 다음 조건식의 결과에 따라 실행블록이 달라진다.
- else if 문의 수는 제한이없으나, 많은 else if문은 속도를 저하시킴
- 마지막 else 블록은 생략가능
<br><br>

### switch
- 어떤변수의 값에 따라서 문장을 실행할 수 있도록 하는 제어문

![switch](/images/java/java01-03.png)
<br><br>



