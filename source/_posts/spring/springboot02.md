---
title: 스프링 부트 원리02 - 의존성 관리
date: 2019-03-28 17:01:13
tags: SpringBoot
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

![springboot](/images/springboot_logo.png)
# 스프릥 부트 개념과 활용02(inflearn) - 백기선 
## Spring boot

<!-- more -->

### 의존성 관리 이해
- pom.xml에서 spring-boot-parent로 들어가본다.
![springboot](/images/springboot/springboot02-1.png)
- spring-boot-parent도 spring-boot-dependencies라는 parent계층을 가지고 있다.
![springboot](/images/springboot/springboot02-2.png)
- spring-boot-dependencies에는 스프링과 호환되는 버전들이 설정되어있다.
![springboot](/images/springboot/springboot02-3.png)
- 또한, dependencyManagement에 각 버전에 해당하는 의존성이 다 정의되어있다.
![springboot](/images/springboot/springboot02-4.png)

- 정의된 의존성을 사용하게되면, 버전에 대한 정보는 없이 사용할 수 있다.
dependencyManagement에 정의된 버전을 가져온다.
- **starter 기반으로 추가하면 된다.**
- 장점
    - 관리해야 할 의존성이 줄어든다.
    - 제3의 라이브러리 사용 시, 스프링 버전과의 호환성 확인작업 줄어든다.
- dependencyManagement에 있더라도 버전을 명시하면, 명시 된 버전을 가져온다.

- 부모 pom 없이 스프링부트 사용하기
    - spring-boot-starter의 부모로 spring-boot-parent를 넣어줘도 의존성 관리를 받을 수 있다.
    - 그마저도 원하지 않는다면 spring-boot-depencies를 설정 해주어야한다.
    ([레퍼런스 문서](https://github.com/Hanope/spring-boot-reference-KR/blob/6329f8a9d1fa4053b5370eb3112e3e2f0119ffe1/part3.adoc)를 참고하자.)
    - 그러나 parent pom은 의존성뿐만 아니라 인코딩,자바버전등 여러 설정을 포함하기 때문에 parent를 사용하는것을 추천한다.
<br><br>

### 의존성 관리 응용
#### 스프링 의존성 추가
- pom.xml 안에 <dependencies>에 추가해준다.
    - 메이븐 의존성 검색 : https://mvnrepository.com
    ![springboot](/images/springboot/springboot02-6.png)
    - 스프링 부트 버전관리 지원 의존성 : 버전정보를 기입하지 않아도 된다.
    (인텔리제이에서는 좌측에 아이콘이 생성되며, 클릭하면 해당 dependencyManagement로 이됭된다.)
    ![springboot](/images/springboot/springboot02-5.png)
    - 스프링 부트 버전관리 지원하지 않는 의존성 : 버전정보도 같이 기입
    ![springboot](/images/springboot/springboot02-7.png)

#### 기존 의존성 버전 변경하기
- spring-boot-dependencies 또는 parent properties 값 복사
![springboot](/images/springboot/springboot02-8.png)
- pom.xml에 properties를 직접 재정의한다.
![springboot](/images/springboot/springboot02-9.png)
<br>
