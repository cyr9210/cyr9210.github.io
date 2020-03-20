---
title: Async
date: 2019-10-26 14:51:57
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

## @Async
<!-- more -->
- 스프링 부트에서 개발자에게 비동기 처리를 하기 위해 제공하는 방법중 하나이다.
  - Reactive stack, CompletableFuture등이 있으나 가장 쉬운 방법이 @Async 적용이다.

### 사용법

1. @EnableAsync로 @Async를 스프링에게 알린다.

   ```java
   @SpringBootApplication
   @EnableAsync
   public class Application {
   
       public static void main(String[] args) {
           SpringApplication.run(Application.class, args);
       }
   
   }
   ```

2. 비동기로 수행되었으면 하는 메소드위에 @Async를 적용한다.

   - Controller

     ```java
     @RestController
     @RequiredArgsConstructor
     public class SampleController {
     
         private final SampleService sampleService;
     
         @GetMapping("/async")
         public void async() {
             for (int i = 0; i < 1000; i++) {
                 sampleService.async(i);
             }
         }
     }
     ```

   - Service

     ```java
     @Service
     @Transactional
     @Slf4j
     public class SampleService {
     
         @Async
         public void async(int i) {
             log.info("async test - " + i);
         }
     }
     ```

3. Thread Pool 설정

   - 별도의 설정이 없으면 비동기 작업을 스레드 플에서 처리하는것이 아니고, 새로운 스레드를 매번 생성해서 작업을 수행시키는것이 default 설정이다.

   - Thread Pool 빈 등록

     ```java
     //import java.util.concurrent.Executor;
     
     @Configuration
     public class ThreadConfig {
         
       	@Bean
         public Executor myPool() {
             ThreadPoolTaskExecutor threadPoolTaskExecutor = new ThreadPoolTaskExecutor();
             threadPoolTaskExecutor.setCorePoolSize(8);
             threadPoolTaskExecutor.setMaxPoolSize(8);
             threadPoolTaskExecutor.setThreadNamePrefix("task");
             return threadPoolTaskExecutor;
         }
     }
     ```

   - SpringBoot 2.0 이상이라면 **auto configuration**으로 Excutor(스레드 풀)를 빈으로 등록해준다.

     - yml, properties 파일을 통해 관련 설정을 변경할 수 있다.

       ```yml
       spring:
         task:
           execution:
             pool:
               max-size: 8
               core-size: 8
       ```

   > **Thread Pool**
   >
   > 병렬 작업 처리가 많아지면 스레드 개수가 증가되고 스레드 생성과 스케쥴링으로 인해 CPU가 바빠져 메모리 사용량이 늘어난다.
   > 이러한 갑작스런 병렬작업의 극대화로 인한 스레드의 증폭으 맞기위해서 사용한다.
   > 작업처리에 사용되는 스레드를 제한된 개수만큼 정해놓고 작업 큐(Queue)에 들어오는 작업들을 하나씩 스레드가 맡아서 처리한다. 처리가 끝난 스레드는 다시 작업 큐에서 작업을 가져와 처리한다.

   

4. 결과

   - Async 적용

     ![async-1](/images/springboot/Async/async-1.png)

   - 미적용

     ![async-2](/images/springboot/Async/async-2.png)

### 심화

#### 주의사항

- public 메소드에만 적용이 가능하다.
- Self-Invocation(자가호출) 메소드는 적용 불가능하다. (= 같은 클래스 내부의 메소드를 호출하는것은 안됨)
- 해당 메소드의 리턴값은 **void** 또는 **CompletableFuture<>**

#### 이유

- AOP가 적용되어 springcontext에 등록되어 있는 빈 객체의 메소드가 호출될 때, Async가 적용되어 있는 메소드를 스프링이 가로채서 스레드풀에서 실행시켜 준다.
  - public이어야 프록시가 될수 있고, 가로챈 스프링의 다른 클래스에서 호출이 가능
  - 셀프호출은 프록시를 우회하고 해당 메소드를 직접 호출하기 때문에 작동하지 않는다.
- https://dzone.com/articles/effective-advice-on-spring-async-part-1

#### 문제

- service의 메소드는 동기로 호출되기를 바라지만, 내부에서 하는 일부의 기능에 대해 비동기로 실행하기를 바란다면 @Async동작이 불가능 하다. (내부 메소드를 호출은 @Async적용이 불가하여..)

  ![async-3](/images/springboot/Async/async-3.png)

#### 해결

1. AsyncService를 하나 만들고 유틸클래스 처럼 사용

   - AsyncService

     ```java
     @Service
     public class AsyncService {
         @Async
         public void asyncRun(Runnable runnable) {
             runnable.run();
         }
     }
     ```

   - 사용

     ```java
     @Service
     @Transactional
     @RequiredArgsConstructor
     @Slf4j
     public class SampleService {
     
         private final AsyncService asyncService;
     
         public void asyncCall(int i) {
             asyncService.asyncRun(() -> async(i));
         }
     
         public void async(int i) {
             log.info("async test - " + i);
         }
     }
     ```

   - 결과

     ![async-4](/images/springboot/Async/async-4.png)

2. @Async 사용하지 않고 CompletableFuture를 쓰되, 쓰레드 풀을 주입받아 해당 쓰레드 풀에서 실행

   - CompletableFuture 사용

     ```java
     @Service
     @Transactional
     @RequiredArgsConstructor
     @Slf4j
     public class SampleService {
     
         private final Executor myPool;
     
         public void asyncCall(int i) {
             CompletableFuture.runAsync(() -> async(i), myPool);
         }
     
         public void async(int i) {
             log.info("async test - " + i);
         }
     }
     ```

   - 결과

     ![async-5](/images/springboot/Async/async-5.png)

### More

- 비동기로 작업(다른 Thread)시, Authentication 전달 안됨
- Exception 발생 시, Test Class 에서는 잡히지 않는다... (메인 스레드를 보고 있음)

### 참고

- [정아무추어 코딩블로그 - How does @Async work?](https://jeong-pro.tistory.com/187)
- [Limky 삽질블로그 - Java Thread Pool(스레드 풀)](https://limkydev.tistory.com/55)
- [허원철의 개발 블로그 - SpringBoot Async 제대로 사용하기](https://heowc.tistory.com/68)
- [spring guide](https://spring.io/guides/gs/async-method/)

<br><br>
