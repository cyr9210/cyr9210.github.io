---
title: 배열정렬, 스트림
date: 2018-11-15 11:20:17
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
# KOSTA DAY13
## Java
<!-- more -->
### 배열정렬
- 대부분은 데이터베이스에서 정렬된 데이터를 조회하여 ArrayList에 담아서 사용하므로 ArrayList 자체를 정렬할 일이 잘 없지만 ]가끔씩 리스트를 직접 정렬해야 할일이 생긴다.
- 정렬하는데에는 Collections.sort(); 메소드를 사용한다.
- Comparable : 최초 정렬기준 된 내용을 작성한다. compareTo()를 오버라이딩한다.
- Comparator : 정렬된 내용을 변경하기 위해 사용한다. compare()을 오버라이딩한다.

#### 예제
```
public Class Person implements Comparable<Person>{
    private String name;
    private int age;
    
    //생성자, set, get 생략
    
    @Override
    public int compareTo(Person p){
        //p가 이후에 들어온 person
        if(age > p.age){
        return 1; 자리변경
        }else if(age < p.age){
        return -1;
        }
        return 0;
    }
}
```
- Person객체에 최초 정렬에 대해 오버라이딩하여 정의한다.
- 해당 Person객체는 오름차순으로 정렬된다.

```
List<Person> list = new ArrayList<>();

list.add(new Person("김자바", 10));
list.add(new Person("이자바", 20));
list.add(new Person("박자바", 30));
list.add(new Person("최자바", 40));

Collections.sort(list, Comparator<person>(){
    @Override
    public int Compare(Person o1, Person o2){
        if(o1.getAge() < o2.getAge()){
            return 1; // 자리 변경
        }else if(o1.getAge() > o2.getAge()){
            return -1; // 자리 유지
        }
        return 0;
    }
}); 
```
- 나이 내림차순으로 재정렬 한다.
- 정석은 또 하나의 클래스를 생성하여 implements comparator 해야하지만 더 쉬운 익명내부클래스로 생성이 가능하다.
<br><br>

### Stream
- 일차원적인 데이터의 흐름
- 입출력 스트림의 메소드 종류가 많은 이유 →  스트림의 종류가 여러가지이기 때문에 ( 데이터 타입 , 객체 등등)
- 데이터 형태에 따른 분류 
    - 문자 스트림(character stream)  : reader / writer 
    - 바이트 스트림(byte stream) : inputstream / outputstream
- 파일 입출력의 과정 파일열기 – 파일의 데이터 읽기,쓰기 – 파일을 닫는다.
- java.io는 예외처리를 하지 않으면 무조건 예외 발생 시킴
- node Stream : outputstream, inputstream, reader, writer 등 기초 스트림
- filterstream : 입출력 기능 성능을 향상시키위해 노드스트림에 연결

- 직렬화 : 객체를 스트림으로 만드는 작업
- 역 직렬화 : 스트림을 객체로 만드는 작업
- java.io.serializable 인터페이스 구현하는 클래스만 직렬화가능 (직렬화 시, 관련 객체 또한 serializable 되야한다.)
- transient : 직렬화해도 null값으로 연결됨
- PrintStream 클래스 : 데이터를 포맷해서 파일로 출력하는 클래스

#### 예제
- reader
![Java](/images/java/java13-02.png)

- writer
![Java](/images/java/java13-03.png)

- Bufferd
![Java](/images/java/java13-04.png)

- Serializable
![Java](/images/java/java13-05.png)
![Java](/images/java/java13-06.png)
