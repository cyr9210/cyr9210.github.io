---
title: Request 로그 찍기 - CommonsRequestLoggingFilter
date: 2019-12-23 08:03:21
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

# Spring

## API Logging

### CommonsRequestLggingFilter

<!-- more -->

- REST API를 만들고 서버에서 어떤 url로 요청을 하는지 로그를 남기고 싶을 때 사용한다.

#### 사용법

1. CommonsLoggingFilter 빈등록

   ```java
   @Bean
   public CommonsRequestLoggingFilter commonsRequestLoggingFilter() {
       CommonsRequestLoggingFilter filter = new CommonsRequestLoggingFilter();
       filter.setIncludeClientInfo(true);
       filter.setIncludeHeaders(true);
       filter.setIncludePayload(true);
       filter.setIncludeQueryString(true);
       filter.setMaxPayloadLength(1000);
       return filter;
   }
   ```

   - `setIncludeClientInfo(true)`: 클라이언트 주소와 세션 ID를 로그 메세지에 포함한다.
   - `filter.setIncludeHeaders(true)` : 헤더정보를 로그에 포함한다.
   - `filter.setIncludePayload(true)` : request내용을 로그에 포함한다.
   - `filter.setIncludeQueryString(true)` : 쿼리 문자열을 로그 메세지에 포함한다.
   - `filter.setMaxPayloadLength(1000)` : 로그의 최대 길이을 설정한다.

2. logger의 debug level을 enable해준다.

   ```java
   public class CommonsRequestLoggingFilter extends AbstractRequestLoggingFilter {
   	@Override
   	protected boolean shouldLog(HttpServletRequest request) {
   		return logger.isDebugEnabled();
   	}
   	/**
   	 * Writes a log message before the request is processed.
   	 */
   	@Override
   	protected void beforeRequest(HttpServletRequest request, String message) {
   		logger.debug(message);
   	}
   	/**
   	 * Writes a log message after the request is processed.
   	 */
   	@Override
   	protected void afterRequest(HttpServletRequest request, String message) {
   		logger.debug(message);
   	}
   }
   ```

   - SpringBoot를 사용할 경우 아래와 같이 간편한게 Property or yml 파일 수정으로 설정이 가능하다.

     ```properties
     logging.level.org.springframework.web.filter=debug
     ```

#### 결과

![CommonsLoggingFilter01](/images/spring/CommonsLoggingFIlter/CommonsLoggingFilter01.png)

### 참고

- [홍이의 개발 노트 - 스프링에서 웹 또는 API 개발시 요청정보 로깅 방법](https://sbcoba.tistory.com/40)
- [https://www.popit.kr/spring%EC%97%90%EC%84%9C%EC%9D%98-api-logging/](https://www.popit.kr/spring에서의-api-logging/)

<br><br>