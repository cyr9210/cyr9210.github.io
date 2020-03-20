---
title: 정규표현식, 자료구조 클래스
date: 2018-11-14 21:22:14
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
# KOSTA DAY12
## Java
<!-- more -->
### 정규표현식
특정한 규칙(패턴)을 가진 문자열의 집합을 표현하는데 사용하는 표현식 언어
![Java](/images/java/java12-01.png)
![Java](/images/java/java12-02.png)
![Java](/images/java/java12-03.png)

#### 예제
![Java](/images/java/java12-04.png)
<br><br>

### 자료구조 클래스
자료구조 : 데이터를 효율적으로 사용할 수 있도록 구조를 만들어서 저장해둔 것
#### 종류
![Java](/images/java/java12-06.png)
- list(리스트)
- stack(스택) : 제일 처음에 넣은것이 제일 나중에 나옴
- queue(큐) : 처음넣은것이 처음 나옴
- hashtabel(해쉬테이블) : 맵 자료구조
- set(집합) : 엄밀히 말하면 자료구조가 아
 
####  ArrayList
배열을 list형으로 구현하는 것
![Java](/images/java/java12-05.png)
- 데이터 타입이 필요 → 제네릭 선언 : 코드의 일관성/사용 용이
- 크기는 사용에 따라 증가
- 삭제 시, 알아서 자리 채움
- 전체를 순서대로 출력할 때 가장 용이 

##### 명령어
- 추가 : .add('') / .add(index, '')
- 크기 : .size() 
- 검색 : indexof('')

![Java](/images/java/java12-07.png)
- 정렬 : Collections.sort(list)
    - Comparable : 기본 정렬기준 구현
    - Comparator : 다른 기준을 정의하고 구현
    
#### LinkedList
인접데이터를 가르키는 식으로 구현
- 이웃해서 저장하지 않고 참조하여 사용하여 연결되어 있다. 
- 주소값으로 연결
- 자료의 수정 삭제 시, 용이

#### Stack
- '후입선출'방식으로 나중에 저장된 데이터가 가장 빨리 인출된다.
```
Linkedlist<Integer> stack = new LinkedList<>();
stack.addLast(new Integer(10));
stack.addLast(new Integer(20));
stack.addLast(new Integer(30));

while(!stack.isEmpty()){
    Integer num = stack.removeLast();
    System.out.prinln(num);
}
// 결과 : 30 → 20 → 10
```

#### Queue
- '선입선출'방식으로 가장 먼저 삽입된 데이터가 가장 먼저 제거된다.
```
LinkedList<Integer> queue = new LinkedList<>();
queue.offer(10);
queue.offer(20);
queue.offer(30);

while(!stack.isEmpty()){
    Integer num = queue.poll();
    System.out.prinln(num);
}
// 결과 : 10 → 20 → 30
```
- poll 대신 peak을 사용하면 Linkedlist의 인자는 그대로 남아있는다.

#### Hash(Map)
자료형으로서 대응관계를 쉽게 표현할 수 있도록 해준다.
- Key와 Value라는 것을 한 쌍으로 가진다.
- 순차적으로 해당 요소 값을 구하지 않고 Key를 통하여 Value를 얻는다.
```
Map<String, String> map = new HashMap<>();

map.put(1, "SK");
map.put(2, "두산");
map.put(3, "넥센");

//크기
System.out.println("요소 개수 : " + map.size());

//제거
if(map.containValue("두산"){
    map.remove(2);
}

//전체출력 (Map → Set → Iterator)
Set set = map.entrySet();
Iterator iter = set.iterator();

while(iter.hasNext()){
    Map.Entry<String, String> e = (Map.Entry<String, String>)iter.next(); //Map.Entry : 내부인터페이스
    System.out.prinln(e.getKey() + e.getValue());
}
```

#### 참고자료
- [생활코딩 - Java Collections Framework](https://opentutorials.org/course/1223/6446)
- [공대인들이 직접쓰는 컴퓨터 공부방 - Collections 이해](https://hackersstudy.tistory.com/26)
<br><br>