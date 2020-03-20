---
title: 인터넷, IP, 도메인, DNS, 포트, 포트포워딩, 절대경로, 상대경로, 유동IP, DDNS
date: 2019-03-16 19:45:26
tags: Web
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
# 생활코딩 - 인터넷
## web
<!-- more -->
저는 네트워크에 대해서 http완벽가이드라는 교재로 공부할 예정이지만..
그전에 AWS를 공부하기위해 선행학습으로 인터넷과 네트워크를 간단하게 정리해보려고 합니다.🙂<br>
관련강의 : [생활코딩 - 인터넷](https://opentutorials.org/course/1688/9483) 

***

### 인터넷
- 웹애플리케이션에 모든 사람들이 접근할 수 있도록 해주는 것이다.

#### IP
![internet](/images/web/internet01-01.png)
- Internet Protocol Address
- 접근하기 위한 주소
- 단점
    - 기억하기 어렵다.

    
>myip를 검색하면 자신의 ip를 알 수 있는 여러 서비스가 있다.

#### 도메인
- 소유지
- 서버컴퓨터의 이름
- 하나의 IP주소를 대응하는 관계이다.
- 기억하기 쉽게하기 위해
- 도메인을 통해 서버에 접속할 수 없다. 
    - 사람이 보기 편하것일 뿐
- IP주소를 통해서만 접속할 수 있다.
- 도메인을 구입하여 네임서버에 등록해야한다.

#### DNS
- Domain Name System
- 도메인 네임이 동작하는 체계

##### 동작원리
![internet](/images/web/internet01-02.png)
- Name Server에 접속하여 해당 IP주소를 요청하고 응답받는다.
- 응답받은 IP주소로 접속한다.
<br><br>

### IPv6
- IP는 0.0.0.0 ~ 255.255.255.255 의 주소체계를 가진다. 
    - 42억개의 IP가 만들어질 수 있다.
    - 부족하다.
    - 해결책이 필요하다.

![internet](/images/web/internet01-03.png)
- IPv4 
    - 기존체계
- IPv6
    - 해결책으로 엄청나게 많은 양의 IP주소 사용할 수 있는 주소체계
    - 아직은 IPv4와 공존하고 있다. 언젠가는 사라질것...
<br><br>

### 포트
![internet](/images/web/internet01-05.png)

#### 공유기 
![internet](/images/web/internet01-04.png)
- 하나의 공인IP로 여러개의 컴퓨터에서 인터넷을 사용할 수 있도록 하는것

#### 포트포워딩
![internet](/images/web/internet01-06.png)
- 웹서버가 설치되어있는 IP로 토스해준다.

##### 공유기에서 포트포워딩 설정 (iptime기준)
![internet](/images/web/internet01-07.png)
- 192.168.0.1 접속 (대부분의 공유기가 해당 IP를 사용한다. 아닐수 있음.)
- 포트포워딩 설정 탭에서 토스하려는 IP주소를 입력한다.
![internet](/images/web/internet01-08.png)
<br><br>

### 절대경로, 상대경로
![internet](/images/web/internet01-09.png)
#### 절대경로
나의 위치와 무관
#### 상대경로
나의 위치에 따라서 달라짐

- / 가 맨앞에 한개 있으면 최상위 디렉토리를 의미한다.
- ../ 는 현재 디렉토리의 상위 디렉토리
- ./ 는 현재 디렉토리 (아무것도 없을때도 현재 디렉토리)
<br><br>

### 유동IP와 DDNS

#### 유동IP
- 오랫동안 사용하지 않은 IP를 회수
- 새로운 가입자 및 사용자에게 준다.
- 다시 사용 시, 새로운 IP부여
- Dynamic Address 라고 부른다.
- IP가 한정적이기 때문에 고갈문제를 해결하기 위해 사용한다.

#### DDNS
- Dynamic DNS(Domain Name System)
- 주기적으로 네임서버에 접속하여 자신의 IP주소를 알려준다.
- 사용자는 도메인 네임으로 접속
- DDNS는 도메인과 호스트의 IP를 지속적으로 동기화 시키고 있다.

#### iptime DDNS설정
![internet](/images/web/internet01-10.png)
- iptime 펌웨어 버전이 9.58이상이어야 한다.
- 해당 PC와 연결되어 있는 공인IP가 바뀌면 도메인 이름과 동기화 시켜준다.
- 도메인이름은 Domain_name.iptime.org 로 설정한다.
- id와 pw 설정
- 정상등록이라고 표시되면 DDNS설정 완료

**도메인을 구입하여 DDNS서비스 이용 시, 무료 서비스 : [FreeDNS](http://freedns.afraid.org/)**
<br><br>

