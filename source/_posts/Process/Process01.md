---
title: 개발프로세스
date: 2018-11-21 16:04:55
tags: Process
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
# KOSTA DAY17
## Process
<!-- more -->
### 개발 프로세스
![Process](/images/Process/Process01-01.png)
1.주제

2.Usecase

3.요구분석 정의서

4.요구분석 명세서

5.도메인 설계

6.구현

7.보고서

8.클래스 다이어그램

#### UseCase
![Process](/images/Process/Process01-02.png)
- 사용자의 상호작용을 다이어그램으로 표현한 것으로 사용자의 관점에서 시스템의 서비스 혹은 기능 및 그와 관련된 외부 요소를 보여주는것이다.
- 한마디로 사용자와 시스템 사이의 관계를 나타냄
<br>
##### 작성순서
- 액터 식별 → 유스케이스 식별 → 관계정의
    - 액터식별 : 정보제공자, 사용자, 삭제자, 유지보수자, 상호작용 하드웨어 or 소프트웨어
    - 유스케이스 식별 : 액터가 원하는 시스템 제공 기능, 모든 요구사항 만족 기능유무
    - 관계정의
        - 연관관계 : 액터 ↔ 유스케이스 (상호작용)
        - 포함관계 : 반드시 실행되어야 하는 유스케이스
        - 확장관계 : 실행 시, 선택적으로 실행되는 유스케이스
        - 일반화 관계 : 다른 액터 또는 유스케이스를
        
#### 요구분석 정의서
![Process](/images/Process/Process01-03.png)

#### 요구분석 명세서
![Process](/images/Process/Process01-04.png)

#### 도메인설계
![Process](/images/Process/Process01-05.png)
- 도메인 : 소프트웨어로 해결하고자 하는 문제영역
- 도메인을 이해하기 위한 개념 모델
- 주로 간소화 된 클래스 다이어그램으로 나타냄 (이해하기 좋은 형식으로 표현)

#### 클래스 다이어그램
![Process](/images/Process/Process01-06.png)
- 시간에 따라 변하지 않는 시스템의 정적인 면을 보여주는 대표적인 UML
- 시스템을 구성하는 클래스들 사이의 관계를 표현한다.
<br><br>

