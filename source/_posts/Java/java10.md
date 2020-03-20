---
title: 인터페이스, Object class, Wrapper class, Inner class, Anonymous class, String API, Boxing/Unboxing
date: 2018-11-12 19:24:18
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
# KOSTA DAY10
## Java
<!-- more -->
### 인터페이스
개발코드와 객체가 서로 통신하는 접점

- 인터페이스 안의 모든 메소드는 추상메소드이다.(굳이 abstarct 키워드를 사용하지 않아도 된다.)
- 인터페이스를 implements하는 클래스는 인터페이스 안의 추상메소드들을 모두 오버라이딩 해야한다.
- 추상클래스와 유사한 기능을 하며 자식메소드를 구현하는데 사용된다.
- 인터페이스를 implements한 클래스는 인터페이스에 선언한 메소드를 오버라이딩하여 사용가능하다.
- 인터페이스 끼리 상속가능 → 실제로는 거의 사용하지 않으나 Java API중 이러한 형택가 있다.

#### 인터페이스를 사용하는 이유
- 자바에서는 다중 상속을 허용하지 않는다.
- 상속할 수 없는 상황에서 공통된 기능을 사용하기위해
- 코드의 독립성

#### 추상클래스와 인터페이스의 공통점과 차이점
- 공통점
    - 코드의 독립성
- 차이점
    - 추상클래스는 일반 메소드가 있을 수 있기 때문에 추상메소드에 abstract 키워드를 사용한다.
<br><br>

### Object class
- 최상위 클래스
- Object class의 toString 메소드는 주소값을 호출한다. → toString 메소드를 생성하여 오버라이딩 가능
- Object class의 equals 메소드는 주소값을 비교한다. → 원하느 값을 비교하기 위해서는 equals메소드를 오버라이딩하여 재정의 해야한다.
<br><br>

### Wrapper class
Wrapper class란 상호전환 시 , 사용하는 클래스

![Java](/images/java/java10-03.png)
- 위의 기본형 data외에는 모두 reference 타입
- 기본형 ← → reference 타입 상호전환이 필요할 수 있다.

#### Boxing 
기본 자료형을 Wrapper 클래스의 객체로 변경하는 과정
```
Integer age = new Integer(30);
```
#### Unboxing
각각의 객체를 기본 자료형으로 변경하여 사용하는 과정 
```
int age2 = age.intValue();
```
<br><br>

### Nested Class
- 클래스안의 클래스라는 의미
- 소스의 가독성을 높이고 유지보수를 용이하게 하기위해 사용

#### Inner Class(내부 클래스)
![Java](/images/java/java10-01.png)
- 클래스안에 클래스를 생성하는 것

#### Anonymous Class(익명 내부 클래스)
![Java](/images/java/java10-02.png)
- 클래스를 한번만 사용하고 구현할 때, 추상클래스 및 상속없이 일회성으로 구현하기위해서 사용한다.
<br><br>

### String API
![Java](/images/java/java10-04.png)
- 불변성을 가진다.
- 연산자를 통해 추가가능
- StringBuffer 클래스는 append로 추가가능
- .indexof(“*”) : *이 몇번째인지 찾는다. (시작 = 0)
- .substring(n1, n2) : n1 ~ n2 까지 추출
- trim() : 앞뒤 공백제거
- split(“*”) : *이 들어간 부분을 찾아서 *로 나누어 배열에 저장
- 숫자를 String으로 변환 → n + “”;
- endsWith() / startWith() : ~시작 or 끝나는지 체크
- valueof : 형변환 시, 사용
<br><br>
