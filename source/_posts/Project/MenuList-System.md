---
title: MenuList System(Java Mini Project)
date: 2018-11-23 15:06:23
tags: 프로젝트_회고
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

# Project
## Java Mini Project
<!-- more -->
### MenuList_System
**Project github : https://github.com/cyr9210/Menu-List-System**
#### 구현내용
![MenuList](/images/Project/MenuList/MenuList_System01.png)
![MenuList](/images/Project/MenuList/MenuList_System02.png)
![MenuList](/images/Project/MenuList/MenuList_System03.png)
![MenuList](/images/Project/MenuList/MenuList_System04.png)
![MenuList](/images/Project/MenuList/MenuList_System05.png)
![MenuList](/images/Project/MenuList/MenuList_System06.png)
![MenuList](/images/Project/MenuList/MenuList_System07.png)
![MenuList](/images/Project/MenuList/MenuList_System08.png)
![MenuList](/images/Project/MenuList/MenuList_System09.png)
![MenuList](/images/Project/MenuList/MenuList_System10.png)
![MenuList](/images/Project/MenuList/MenuList_System11.png)
![MenuList](/images/Project/MenuList/MenuList_System12.png)
![MenuList](/images/Project/MenuList/MenuList_System13.png)
![MenuList](/images/Project/MenuList/MenuList_System14.png)
![MenuList](/images/Project/MenuList/MenuList_System15.png)
![MenuList](/images/Project/MenuList/MenuList_System16.png)
![MenuList](/images/Project/MenuList/MenuList_System17.png)

#### 상세설명
![MenuList](/images/Project/MenuList/MenuList_System18.png)
- Customer(고객) 와 Admin(관리자)는 추상 클래스 User를 상속받는다.

![MenuList](/images/Project/MenuList/MenuList_System19.png)
![MenuList](/images/Project/MenuList/MenuList_System20.png)
- User는 추상메소드 showMenu를 가지고, Customer 및 Admin은 showMenu를 오버라이딩 한다.
- Customer 와 Admin에는 각각의 기능들을 구현할 수 있는 일반 메소드를 가진다.

![MenuList](/images/Project/MenuList/MenuList_System21.png)
- MenuManager 클래스는 Menu객체를 ArrayList<menu\>를 담는다.
- Menu는 4개의 String 변수를 갖는다.

![MenuList](/images/Project/MenuList/MenuList_System23.png)
- Manager 클래스는 생성한 클래스들을 객체화한다.
- 생성한 객체들을 상호작용하여 요구하는 기능들을 구현하는 메소드를 가진다.

![MenuList](/images/Project/MenuList/MenuList_System24.png)
- Main클래스는 Manager클래스에서 구현한 메소드 기능들을 불러온다.
<br><br>

### 느낀점
우리조도 나름 잘 구현했다고 생각했는데, 다른 조들의 발표내용들을 보고 너무 잘해서 놀랐다.
이번 프로젝트의 가장 아쉬운점은 접근제어자를 사용하지 않고 대부분 default 상태로 접근을 제어하였다는것이다. 
get /set 메소드를 많이 사용한만큼 해당변수들에 대해 private 접근제어자를 설정했어야 됬다고 생각한다. 
시간이 많이 없었던 탓인지 정신없이 하다보니 놓친것 같다. 
이번 프로젝트를 통해 정말 많이 부족함을 느끼고 더 열심히 배우고 많은 반복 복습이 필요할 것 같다.
<br><br>


