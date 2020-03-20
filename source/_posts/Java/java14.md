---
title: File Class, Thread
date: 2018-11-16 13:19:05
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
# KOSTA DAY14
## Java
<!-- more -->
### File Class
![Java](/images/java/java13-01.png)
- File 클래스
    - (파일의 내용이 아니라) 파일 자체를 관리하는 클래스 
    - 파일 정보를 가져오는 메소드 
    - 파일 정보를 수정하는 메소드 
    - 파일을 생성/삭제하는 메소드 
    - 디렉토리 관리에도 사용됨(디렉토리도 파일 객체)
    - 배열로 저장 가능
#### 예제
- File
![Java](/images/java/java14-01.png)
![Java](/images/java/java14-02.png)
![Java](/images/java/java14-03.png)

- 디렉토리 복사
![Java](/images/java/java14-04.png)
![Java](/images/java/java14-05.png)

<br><br>

### Thread(스레드)
![Java](/images/java/java14-06.png)
- 프로그램의 실행 흐름 
- 싱글스레드 : 스레드가 하나뿐인 프로그램 
- 멀티스레드 프로그램 : 스레드가 둘 이상인 프로그램
(멀티스레드프로그램은 어떤 스레드를 먼저 실행할지 모른다. )

#### Thread 클래스를 이용한 멀티스레드 프로그램 
##### 스레드로 실행 할 클래스의 선언방법 
- 스레드 클래스를 상속받아 run 메소드를 오버라이딩한다. 
- 스레드를 시작하기 위해서 start 메소드를 호출 (thread.start();) 
- 멀티스레드 프로그램은 실행은 하나씩 실행된다.(아주짧은시간)  
- sleep메소드 : 일정시간이 경과 되기를 기다리는 메소드    
(예외발생이 가능하여, **반드시 예외처리르 해줘야한다.**)
- Critical section : 스레드 실행 중 다른 스레드로 제어가 넘어가면 문제를 일으킨다.   
 그래서 동기화를 진행하여 공유데이터를 사용하지 않게끔 해줘야 한다.
- **implement runnable 로도 thread를 생성할 수 있다. **   
(자바에서 상속은 하나밖에 안되기때문에 다른 클래스를 상속하는 스레드를 만들기위해서) 

#### 프로세스와 스레드의 차이점
- 프로세스 : 프로그램의 단위
- 스레드 : 프로그램 내의 실행 흐름
<br><br>