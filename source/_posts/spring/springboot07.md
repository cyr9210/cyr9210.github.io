---
title: 스프링 부트 활용07 - 외부설정
date: 2019-04-05 14:22:16
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
# 스프릥 부트 개념과 활용07(inflearn) - 백기선 
## Spring boot

<!-- more -->

### 외부 설정
#### 사용할 수 있는 외부 설정
- properties
    - 스프링 부트가 애플리케이션을 구동할 때, 자동으로 로딩 
    - key=value로 정의
    - 애플리케이션에서 참조해서 사용할 수 있다.
        
- YAML
- 환경변수
- 커맨드 라인 아규먼트

#### 프로퍼티 우선 순위
1. 유저 홈 디렉토리에 있는 spring-boot-dev-tools.properties
2. 테스트에 있는 @TestPropertySource
    ![springboot](/images/springboot/springboot07-4.png)
    - 파일로도 정의 가능하다.
    ![springboot](/images/springboot/springboot07-6.png)
3. @SpringBootTest 애노테이션의 properties 애트리뷰트
    ![springboot](/images/springboot/springboot07-3.png)
    
4. 커맨드 라인 아규먼트
    ```
    java -jar target/jar_name --bong.name=bong
    ```
5. SPRING_APPLICATION_JSON (환경 변수 또는 시스템 프로티) 에 들어있는 프로퍼티
6. ServletConfig 파라미터
7. ServletContext 파라미터
8. java:comp/env JNDI 애트리뷰트
9. System.getProperties() 자바 시스템 프로퍼티
10. OS 환경 변수
11. RandomValuePropertySource
12. JAR 밖에 있는 특정 프로파일용 application properties
13. JAR 안에 있는 특정 프로파일용 application properties
14. JAR 밖에 있는 application properties
15. JAR 안에 있는 application properties
    ![springboot](/images/springboot/springboot07-1.png)
    - test resources가 더 늦게 빌드되어 덮어씌어진다.
    - main properties에 있던 속성이 test에 없다면 에러가 발생한다.
    ![springboot](/images/springboot/springboot07-2.png)
16. @PropertySource
17. 기본 프로퍼티 (SpringApplication.setDefaultProperties)

#### application.properties 우선 순위 
- 높은게 낮은걸 덮어 씁니다. 
    1. file:./config/
    2. file:./
    3. classpath:/config/
    4. classpath:/

**단, application.properties는 프로퍼티 우선순위 14,15이기 때문에 상위 우선순위가 있을 시, 적용되지 않는다.**
<br>
##### 예제
![springboot](/images/springboot/springboot07-7.png)
- 2순위, 4순위에 해당하는 위치에 application.properties를 만들고 각각 다른값을 정의한다.
- 2순위의 application.properties가 적용된다.

#### 랜덤값 설정하기
- ${random.*}
- ${random.int(1,100)}
    - 1~ 100까지중 랜덤
    - **공백문자 주의(없어야 한다.)**

#### 플레이스 홀더
- name = keesun
fullName = ${name} baik

#### 타입-세이프 프로퍼티 @ConfigurationProperties
- 여러 프로퍼티를 묶어서 읽어올 수 있음
- 빈으로등록해서다른빈에주입할수있음
    - @EnableConfigurationProperties
        ![springboot](/images/springboot/springboot07-10.png)
        - **이렇게 설정을 빈으로 등록해야 하지만 기본적으로 되어있기 때문에 빈으로 등록만 해주면 된다.**
    - **@Component**
    - @Bean
<br>

##### 예제
다음과 같은 properties값이 있을 때
![springboot](/images/springboot/springboot07-11.png)

- properties값에 맞추어 필드값 및 getter, setter를 생성한다.
![springboot](/images/springboot/springboot07-9.png)

- @ConfigurationPropertiess("properties_name")
    - 필요한 의존성 설정이 안되어 있어 의존성 추가가 필요하다.
    ![springboot](/images/springboot/springboot07-8.png)
    ```
    <dependency>
    	<groupId>org.springframework.boot</groupId>
    	<artifactId>spring-boot-configuration-processor</artifactId>
    	<optional>true</optional>
    </dependency>
    ```
- 앞서 설명했듯이 @EnableConfigurationProperies 자동으로 설정되기 때문에 빈으로 등록하여 사용한다.
![springboot](/images/springboot/springboot07-12.png)

- @Autowired로 빈을 주입받아 사용한다.
![springboot](/images/springboot/springboot07-13.png)
---
    
- 융통성 있는 바인딩
    - 대소문자, -, _를 융통성 있게 바인딩한다.
        - context-path (케밥)
        - context_path (언드스코어)
        - contextPath (캐멀)
        - CONTEXTPATH

- 프로퍼티 타입 컨버전
    - ConversionService통해서 타입 Conversion이 일어난다.
        - 참고 : [데이터바인딩](https://cyr9210.github.io/2019/03/22/Spring/springframework-core03/)
    - @DurationUnit
        - Duration객체로 타입 Conversion해준다.
        ![springboot](/images/springboot/springboot07-14.png)
        - 따로 지정하지 않으면 30
        - 지정하면 지정한값
        ![springboot](/images/springboot/springboot07-15.png)
        - 해당 어노테이션을 쓰지않아도 아래와 같은 suffix를 잘쓰면 자동으로 타입 변한을 해준다.
        ![springboot](/images/springboot/springboot07-16.png)![springboot](/images/springboot/springboot07-17.png)
    
- 프로퍼티 값 검증
    ![springboot](/images/springboot/springboot07-19.png)
    - @Validated
    - JSR-303 (@NotNull, ...)

@Value 어노테이션을 사용하는것은 
- SpEL 을 사용할 수 있지만...
- 위에 있는 기능들은 전부 사용 불가
- **따라서 properties값을 사용할 때는 위와같이 사용하는 것을 추천**

