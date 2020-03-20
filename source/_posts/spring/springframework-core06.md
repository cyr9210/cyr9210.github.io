---
title: 스프링 핵심기술06 - Null-safety
date: 2019-03-27 15:17:40
tags: Springframework
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
 
![springf](/images/springframwork-logo.png)
# 스프링 프레임워크 핵심기술06(inflearn) - 백기선 
## Springframework
<!-- more -->
### Null-safety
- 스프링 프레임워크 5에 추가된 Null 관련 애노테이션
    - @NonNull
    - @Nullable
    - @NonNullApi (패키지 레벨 설정)
    - @NonNullFields (패키지 레벨 설정)

#### 목적
- (툴의 지원을 받아) 컴파일 시점에 최대한 NullPointerException을 방지하는 것

#### IDE설정 (IntelliJ)
- preference → Compiler → Configure annotation → annotation추가
![springcore](/images/springc/springcore05-11.png)
    - NonNull에 Nonnull Nullable에 Nullable 추

- IDE 재시작
![springcore](/images/springc/springcore05-12.png)

#### @패키지 레벨 설정
![springcore](/images/springc/springcore05-13.png)
- package-info 파일생성
- @NonNullapi or NonNullFields 어노테이션 추가
- 해당 어노테이션을 사용하면 패키지 전체 NonNull적용, 널이 필요한 경우 Nullable 어노테이션 사용하여 null값 가능하도록 활용할 수 있다.
<br><br>

***
 SpringFramework 핵심기술 강좌를 모두 수강하였습니다.
강의 내용들은 대부분 해당 어노테이션 및 기능들이 어떻게 구현되는지 설명이었습니다.
대부분 기능에 대해서 알고는 있었지만, 어떻게 구현되는지 몰랐던 저에게는 매우 알찬 강의였으며 만족스러웠습니다.😆

 학원에서 한번 배운 것이 이해하는데 큰 도움이 된 것 같습니다. 
해당 강좌를 들으시려는 분들에게 먼저 책 등을 이용하여 스프링을 사용해보고 수강하는것을 추천드립니다!😀
<br><br>
    




