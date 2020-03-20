---
title: 스프링 부트 활용11 - SpringBoot_Devtools
date: 2019-04-11 00:26:47
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
# 스프릥 부트 개념과 활용11(inflearn) - 백기선 
## Spring boot

<!-- more -->

### SpringBoot_Devtools
- devtools 의존성 추가가 필요하다.
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
</dependency>
```

- 캐시설정을 개발 환경에 맞게 변경
    - 캐시 적용 시, 변경사항을 바로바로 반영하지 않음.(캐시를 꺼준다.)
    - **devtools 사용보다는 설정만 복사하여 [property 설정](https://github.com/spring-projects/spring-boot/blob/v2.1.4.RELEASE/spring-boot-project/spring-boot-devtools/src/main/java/org/springframework/boot/devtools/env/DevToolsPropertyDefaultsPostProcessor.java)을 하는것을 추천한다.**
    (restart, 라이브 릴로드 등의 기능은 오류가 많음) 
    ![springboot](/images/springboot/springboot11-1.png)
    
- 클래스패스에 있는 파일이 변경 될 때마다 자동으로 재시작
    - 직접 껐다 켜는거 (cold starts)보다 빠르다.
        - 스프링 부트는 클래스 로더를 2개 사용한다.(devtools에서는 restart만 재시작하기 때문에 더 빠르다.)
            - base classloader : 우리가 변경하지 않는 의존성을 읽는다.
            - restart classloader : 애플리케이션을 읽는다.
            
    - 릴로딩 보다는 느리다. (JRebel 같은건 아님)
    - 리스타트 하고 싶지 않은 리소스는? 
        - spring.devtools.restart.exclude
    - 리스타트 기능 끄려면? 
        - spring.devtools.restart.enabled = false
    - 파일 변경 후, 빌드하면 저절로 재시작된다.
    - 에러가 많다. (추천하지 않음)      

- 라이브릴로드
    - 리스타트했을때브라우저자동리프레시하는기능
    - 브라우저 플러그인 설치해야 함.
    - 라이브 릴로드 서버 끄려면? 
        - spring.devtools.liveload.enabled = false
- 글로벌설정
    - ~/.spring-boot-devtools.properties (우선순위 1등)

- 리모트 애플리케이션
    - **과정이 복잡하고 위험함으로 운영용에서 사용하지 않는것을 추천한다.**
    
 

