---
title: 스프링 프레임워크 입문02 - AOP
date: 2019-03-08 14:03:12
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
# 스프링 프레임워크 입문02(inflearn) - 백기선 
## Springframework
<!-- more -->
### AOP(Aspect Oriented Programming)
흩어져있는 코드를 한곳에 모으는 코딩 기법
- 바이트코드 조작
    - 클래스파일을 조작
- 프록시패턴
    ```
    class AProxy extends A {
    }
    ```
    - 내부적으로 위와같은 클래스를 생성한다.
    - **스프링 AOP는 프록시 패턴을 사용한다.**
    
#### 예제
어떤메소드가 실행됬을 때, 시간을 로그로 남기는 예제
- @LogExecutionTime 애노테이션 (어디에 적용할지 표시 해두는 용도) 생성
**annotation 자체는 어떠한 기능도 없다.**
```
@Target(ElementType.METHOD) //메소드에 사용할것이다. 
@Retention(RetentionPolicy.RUNTIME) //런타임동안 해당 annotation을 유지 할 것이다.
public @interface LogExecutionTime {
}
```
- 실제 Aspect (@LogExecutionTime 애노테이션 달린곳에 적용)
```
@Component
@Aspect
public class LogAspect {
    Logger logger = LoggerFactory.getLogger(LogAspect.class); // 로그를 찍기 위해 로거 객체 생성
    @Around("@annotation(LogExecutionTime)")
    public Object logExecutionTime(ProceedingJoinPoint joinPoint) throws Throwable {
        StopWatch stopWatch = new StopWatch();
        stopWatch.start();
        
        Object proceed = joinPoint.proceed(); // 어노테이션이 적용된 메소드를 실행
        
        stopWatch.stop();
        logger.info(stopWatch.prettyPrint());
        
        return proceed;
    }
}
```

<br><br>

### PSA(Portable Service Abstraction)
- 잘 만든 인터페이스
- 테스트에 유리하다.
- 변경하기 좋다.
- 스프링이 제공하는 API는 모두 PSA라고 본다.
    -  스프링프레임워크가 제공하는 API의 90%는 추상화된 인터페이스로 모든영역을 수용할 수 있다.

#### 예제
##### @Transactional
[PlatformTransactionManager](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/transaction/PlatformTransactionManager.html)

![spring](/images/springi/springi02-01.png)
- 구현체들중 하나는 또는 다수는 빈으로 등록이 된다.(Spring boot의 자동설정에 의해서)
- 트랜잭션을 처리하는 Aspect는 빈의 바뀌더라도 Aspect는 변화하지 않으므로, 코드의 변경이 없다.

##### @Controller | @RequestMapping
![spring](/images/springi/springi02-02.png)
- 해당코드만 봐서는 Servlet을 사용하는 것인지, Reactive를 사용하는 것인지 판단이 불가능하다. 즉, 추상화 (의존성을 봐야한다.)
- 추상화 되있으므로 기술이 변경되어도 대부분의 코드들은 변경없이 사용이 가능하다.
<br><br>

강의를 들으며 느낀것이 기초 개념 부분이지만 상당히 잘못알고있던 부분도 있었고, 부분적으로만 알고 있었던 부분도 있었습니다.
좋은 강의를 듣고 정리할 수 있는 시간이 되어서 좋았습니다. 😀
<br><br>

 



    


