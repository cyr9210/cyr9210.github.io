---
title: Sheduled
date: 2019-10-26 15:35:24
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


## @Scheduled 스케줄러
<!-- more -->
### 설정

- `@EnableScheduling` 을 사용하여 스케쥴링을 사용하는것을 알린다.

  ```java
  @SpringBootApplication
  @EnableScheduling
  public class ApiApplication {
      public static void main(String[] args) {
          SpringApplication.run(ApiApplication.class, args);
      }
  }
  ```

- SpringBoot 2.0 에서는 auto-configuration으로 스케줄러를 생성해준다.
  그러나 이 스케줄러의 경우, **쓰레드풀이 아니기 때문에** 많은 작업이 있을 때 효율적이지 못하다.

  ![sceduled02](/images/springboot/scheduled/sceduled02.png)

- 많은 작업이 필요한 경우, 쓰레드 풀을 사용할 수 있도록  설정할 수 있다.

  - yml 파일 설정

    ```yaml 
    spring:
    	task:
    		scheduling:
    			pool:
    				size: 8
    			thread-name-prefix: my-scheduler
    ```

    이미지

  - configuration 설정

    ```java
    @Configuration
    public class SchedulerConfiguration {
    
      @Bean
    	public TaskScheduler poolScheduler() {
    		ThreadPoolTaskScheduler threadpool = new ThreadPoolTaskScheduler();
    		threadpool.setPoolSize(Runtime.getRuntime().availableProcessors() * 2);
    		threadpool.setThreadNamePrefix("my-scheduler");
    		return threadpool;
    	} 
    }
    ```

    - Runtime.getRuntime().availableProcessors() = 4

  ![sceduled03](/images/springboot/scheduled/sceduled03.png)

#### 사용법

- 주기적으로 실행을 요하는 메소드 위에서 `@Scheduled` 를 사용한다.(빈으로 등록된 클래스의 메소드여야 한다.)

  ```java
  @Scheduled(fixedRateString = "5", initialDelay = 3000) 
  private void scheduleTest() {
  	logger.error("hello jeong-pro"); 
  }
  ```

  - initialDelay : 대기시간
  - fixedRateString : 이전 작업이 끝난 시점으로 부터 고정된 시간을 문자열로 설정 (milli seconds 단위)

#### 속성

| 속성이름           | 설명                                                         |
| ------------------ | ------------------------------------------------------------ |
| cron               | 크론 표현식을 지원, "초 분 시 일 월 주 (년)"으로 표현한다.   |
| fixedDelay         | 이전 작업이 끝난 시점으로 부터 고정된 시간을 설정 (milli seconds 단위) |
| fixedDelayString   | fixedDelay의 value값을 문자열로 설정                         |
| fixedRate          | 이전 작업이 수행되기 시작한 시점으로 부터 고정된 시간을 설정 (milli seconds 단위) |
| fixedRateString    | fixedRate의 value값을 문자열로 설정                          |
| initialDelay       | 스케줄러에서 메소드가 등록되자마자 수행하는것이 아닌 초기 지연시간을 설정 |
| initialDelayString | initialDelay의 value값을 문자열로 설정                       |
| zone               | cron 표현식을 사용햇을 때 사용할 time zone, default = 서버의 time zone |

> **cron 표현식**
>
> | 필드명 | 값의 허용 범위       | 허용된 특수분자 |
> | ------ | -------------------- | --------------- |
> | 초     | 0 ~ 59               | , - * /         |
> | 분     | 0 ~ 59               | , - * /         |
> | 시     | 0 ~ 23               | , - * /         |
> | 일     | 1 ~ 31               | , - * / ? L W   |
> | 월     | 1 ~ 12 or JAN ~ DEC  | , - * /         |
> | 요일   | 1 ~ 7 or SUN ~ SAT   | , - * / ? L #   |
> | 연도   | empty or 1970 ~ 2099 | , - * /         |
>
> - \* : 모든값
>
> - ? : 특정한 값이 없음
>
> - \- : 범위 (ex> MON-WED)
>
> - , : 특별한 값일 때만 동작
>
> - / : 시작시간/단위 (ex> 0/5 매 5분)
>
> - L : 일에서 사용하면 마지막 일, 요일에서는 마지막 요일 (토요일)
>
> - W : 가장 가까운 평일 (ex> 15W 15일에서 가장 가까운 평일)
>
> - \# : 몇째주의 무슨 요일을 표현, 요일#주 (ex> 4#2 2번째주 수요일 )

- fixedDelay vs fixedRate

  ![sceduled01](/images/springboot/scheduled/sceduled01.png)

#### 커스터마이징

1. ThreadPoolTaskScheduler를 상속받는 클래스를 생성

   ![sceduled04](/images/springboot/scheduled/sceduled04.png)

   - 커스터마이징을 원하는 메소드를 오버라이드 한다.
     - 상기 예제는 fixedRate가 0이면 아무일도 일어나지 않도록 커스터마이징
       본래는 fixedRate가 0이면 exception 발생

2. 생성한 커스터마이징 클래스를 사용하도록 설정한다.

   ![sceduled05](/images/springboot/scheduled/sceduled05.png)

#### 외부설정으로 주기 설정

- yml, properties파일에 정의한 값으로 적용가능하다.

  ```java
  @Scheduled(fixedRateString = "${myscheduler.period}", initialDelay = 2000) 
  private void scheduleTest() { 
    logger.error("hello jeong-pro"); 
  }
  ```

#### 참고

- [정아마추어 코딩블로그 - @Scheduled 사용법](https://jeong-pro.tistory.com/186)

<br><br>