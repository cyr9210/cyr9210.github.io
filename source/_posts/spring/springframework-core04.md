---
title: 스프링 핵심기술04 - SpEL
date: 2019-03-26 14:57:24
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
# 스프링 프레임워크 핵심기술04(inflearn) - 백기선 
## Springframework
<!-- more -->
### [SpEL](https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#expressions)(스프링 Expression Language)

#### 스프링 EL이란?
- 객체 그래프를 조회하고 조작하는 기능을 제공한다.
- [Unified EL](https://docs.oracle.com/javaee/5/tutorial/doc/bnahq.html)과 비슷하지만, 메소드 호출을 지원하며, 문자열 템플릿 기능도 제공한다.
- OGNL, MVEL, JBOss EL 등 자바에서 사용할 수 있는 여러 EL이 있지만, SpEL은 모든 스프링 프로젝트 전반에 걸쳐 사용할 EL로 만들었다.
- 스프링 3.0 부터 지원.

#### 문법
- \#{“표현식"}
- ${“프로퍼티"}
- 표현식은 프로퍼티를 가질 수 있지만, 반대는 안 됨.
    - \#{${my.data} + 1}
- 레퍼런스 참고

#### 실제로 어디서 쓰나?
- @Value 애노테이션
- @ConditionalOnExpression 애노테이션
- 스프링 시큐리티
    - 메소드 시큐리티, @PreAuthorize, @PostAuthorize, @PreFilter, @PostFilter
    - XML 인터셉터 URL 설정
    - ...
- 스프링 데이터
    - @Query 애노테이션
- [Thymeleaf](https://blog.outsider.ne.kr/997)
- ...

#### SpEL 구성
- ExpressionParser parser = new SpelExpressionParser()
    - 예제에서 확인
- StandardEvaluationContext context = new StandardEvaluationContext(bean)
- Expression expression = parser.parseExpression(“SpEL 표현식”)
- String value = expression.getvalue(context, String.class)

#### 예제
![springcore](/images/springc/springcore04-01.png)

![springcore](/images/springc/springcore04-02.png)
<br>