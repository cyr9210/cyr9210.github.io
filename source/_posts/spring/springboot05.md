---
title: 스프링 부트 원리05 - JAR
date: 2019-04-03 12:08:49
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
# 스프릥 부트 개념과 활용05(inflearn) - 백기선 
## Spring boot

<!-- more -->

### [독립적으로 실행 가능한 JAR](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html)
- mvn package를 하면 실행 가능한 ​JAR 파일 “하나가"​ 생성 됨.
    - mvn clean : target아래 파일들 삭제
    - mvn package -DskipTests : test제외하고 실행 target아래 jar파일 **하나**를 생성한다.
    
- 만든 jar파일 샐행
    - java -jar <jar파일명\> -\-<arguments\>
    
- spring-maven-plugin이 해주는 일 (패키징)
    - 모든 의존성을 포함하는 jar파일 하나를 만들어준다.

- 과거 “uber” jar 를 사용
    - 모든 클래스 (의존성 및 애플리케이션)를 하나로 압축하는 방법
    - 뭐가어디에서온건지알수가없음
        - 무슨 라이브러리를 쓰는건지..
    - 내용은 다르지만 이름이 같은 파일은 또 어떻게?

- 스프링 부트의 전략
![springboot](/images/springboot/springboot05-1.png)
    - 내장 JAR : 기본적으로 자바에는 내장 JAR를 로딩하는 ​표준적인 방법이 없음​. 
    - 애플리케이션 클래스와 라이브러리 위치 구분
    - org.springframework.boot.loader.jar.JarFile을 사용해서 내장 JAR를 읽는다. 
    - org.springframework.boot.loader.Launcher를 사용해서 실행한다.
<br><br>