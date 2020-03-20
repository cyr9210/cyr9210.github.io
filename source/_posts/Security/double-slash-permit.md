---
title: 스프링 시큐리티 - 더블 슬래쉬 허용
date: 2020-01-12 22:15:04
tags: [SpringSecurity, debug]
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
# Spring

## Security

### 더블 슬래쉬 허용
<!-- more -->

#### 문제점

- 기본 정책은 URL에 더블 슬래쉬를 허용하지 않는다.

  - 에러가 발생

    ```
    org.springframework.security.web.firewall.RequestRejectedException: The request was rejected because the URL was not normalized.
    	at org.springframework.security.web.firewall.StrictHttpFirewall.getFirewalledRequest(StrictHttpFirewall.java:296)
    	at org.springframework.security.web.FilterChainProxy.doFilterInternal(FilterChainProxy.java:194)
    	at org.springframework.security.web.FilterChainProxy.doFilter(FilterChainProxy.java:178)
    	at org.springframework.web.filter.DelegatingFilterProxy.invokeDelegate(DelegatingFilterProxy.java:357)
    ```

#### 허용

- SpringSecurityConfig 설정 클래스에 다음 빈을 추가해준다.

  ```java
  @Bean
  public HttpFirewall defaultHttpFirewall() {
    return new DefaultHttpFirewall();
  }
  ```

- WebSecurity 설정에 추가해준다.

  ```java
  @Override
  public void configure(WebSecurity web) throws Exception {
  	web.httpFirewall(defaultHttpFirewall());
  }
  ```

<br><br>