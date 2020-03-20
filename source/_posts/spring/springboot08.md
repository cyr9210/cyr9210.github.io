---
title: 스프링 부트 활용08 - 프로파일
date: 2019-04-08 16:15:30
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
# 스프릥 부트 개념과 활용08(inflearn) - 백기선 
## Spring boot

<!-- more -->

### 프로파일
참고 : [IoC컨테이너와 빈 - profile](https://cyr9210.github.io/2019/03/20/Spring/springframework-core01/)
#### @Profile 애노테이션은 어디에?
- @Configuration
![springboot](/images/springboot/springboot08-6.png)

- @Component
- @Bean(메소드)

#### 어떤 프로파일을 활성화 할 것인가?
- spring.profiles.active
![springboot](/images/springboot/springboot08-1.png)![springboot](/images/springboot/springboot08-2.png)
- 커맨드라인 arguments가 우선순위가 높다.

#### 어떤 프로파일을 추가할 것인가?
- spring.profiles.include
![springboot](/images/springboot/springboot08-5.png)

#### 프로파일용 프로퍼티
- application-{profile}.properties
- 프로파일용 프로퍼티 파일이 기본적으로 application.properties보다 우선순위가 높다.
![springboot](/images/springboot/springboot08-3.png)