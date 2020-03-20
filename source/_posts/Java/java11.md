---
title: Calendar, GregorianCalandar, Random
date: 2018-11-13 17:21:55
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
# KOSTA DAY11
## Java
<!-- more -->
### Calendar 클래스
- 날짜와 시간에 관련된 클래스로서, 시스템 시계로 부터 시간을 가져오는 클래스
- GregorianCalendar도 사용되지만 보편적으로는 Calendar가 사용된다.
- 생성자가 proteted로 new를 통해 객체 생성이 불가(**Calendar = 추상클래스**) → getInstance() 메소드를 통해 객체를 생성한다. 
- 주로 상수와 메소드를 활용하여 다양한 기능을 사용한다.

#### Gregorian Calendar 클래스
- Calendar 클래스의 후손 클래스로 년, 월, 일, 시, 분, 초 정보를 필드를 통해 사용가능
- Calendar class와 차이점

![Java](/images/java/java11-01.png)

#### Date 클래스
![Java](/images/java/java11-02.png)
- 시스템으로부터 현재 날짜, 시간 정보를 가져와서 다루도록 만들어진 클래스 
- 생성자 2개만 사용 가능

![Java](/images/java/java11-03.png)
- Date(long n)클래스 정수값을 가지고 날짜 및 시간을 계산

#### 내장 메소드
![Java](/images/java/java11-04.png)
![Java](/images/java/java11-05.png)
<br><br>

### Random
난수를 보다 편하게 사용할 수 있도록 한다.
```
Random r = new Random();

int num = r.nextInt(9); // 0 <= numm < 9 난수 추출
int num2 = r.nextInt(9)+1 // 1 <= num < 9 난수 추
```
- Random의 객체를 new로 생성한 후 생성된 객체를 nextInt(); 로 사용한다.
<br><br>




