---
title: 스프링 프레임워크 입문01 - IOC, IOC컨테이너
date: 2019-03-05 02:19:06
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
# 스프링 프레임워크 입문01(inflearn) - 백기선 
## Springframework
<!-- more -->
kosta에서 스프링 프레임워크에 대해서 배웠지만.. 저는 스프링 프레임워크가 정확히 무엇인지 잘 모르고 있다는 생각이 들었습니다.
책을 사서 공부해야 하나, 인터넷 강의를 들어야하나 고민을 했는데 책보다는 시청각 자료와 친했던 저는 강을 선택했습니다..<br>
여러 강좌들을 고민하다가 유투브에서 종종 보았던 백기선님의 강의가 눈에 들어왔습니다.
입문강좌를 비롯해 핵심기술, 웹MVC, 스프링 부트의 개념과 활용, Rest API개발 까지 여러 스프링에대한 여러 강의들이 있는것이 마음에 들었고, 저는 충동적으로 강의를 모두 구매하였습니다...😂<br>
기억보단 기록을 남기기 위해 강의를 듣고 정리하며 초보 개발자의 시선에서 기록을 남겨보려 합니다. 😀
예제는 spring-projects/spring-petclinic 으로 진행하였습니다. ([petclinic](https://github.com/spring-projects/spring-petclinic))

### IoC(Inversion of Control)
IOC란 Inversion of Control 즉, 제어의 뒤밖임이라고 직역할 수 있다.
의존성에 관하여 말하자면 기존에는 자신이 필요한 객체에 대하여 자신이 만들어서 사용하였다.
그러나 스프링을 사용하면 보다 간단하게 필요한 객체를 생성자에 매개변수로 주었다고 생각하고 사용할 수있다.

기존에 스프링 사용전에는 다음과 같은 코드를 사용했다면...
```
class OwnerController { 
    private OwnerRepository repo; 
    
    class OwnerController { 
    private OwnerRepository repository = new OwnerRepository(); 
    }
}
```
스프링에서는 누군가 repository를 주었다고 생각하고 다음과 같이 작성할 수 있다.
```
class OwnerController { 
    private OwnerRepository repo; 

    public OwnerController(OwnerRepository repo) { 
    this.repo = repo; 
    } // repo를 사용합니다. 
}
```

#### IoC 컨테이너
- 빈(Bean)을 만들어주고 엮어주고 제공해준다.

이 부분을 설명하실 때, 충격받았던것이 아주 오래전에는 web.xml을 사용하여 설정해주었으나 sevlet3.5부터 Java설정을 지원하면서 xml이 사라졌고 스프링부트가 나오면서 그마저도 기본설정으로 감춰줘있다고 한 부분이다.   
kosta에서 교육을 받을 때, xml에 이것저것 Bean설정 및 여러 설정을 해주었는데... 아주 오래된 구식방법을 사용하고 있었던것 같다.😂
<br><br>

### Bean
- 스프링 IoC 컨테이너가 관리하는 객체

#### Bean 등록
- Componnent Scanning(@Repository, @Service, @Controller)
- 직접등록(xml or Java)

>Componnent 아래 Repository, Service, Controller 모두 있다.

- 직접등록방법

```
@Bean
public String young(){
    return "Young Rack";
}
```

#### Bean 사용
```
@Autowired // or @Inject
String young
```
- @Autowired 해당이름의 Bean 객체를 찾아서 넣어준다.
- @Inject

> **@Autowired 와 Inject의 차이점**
![spring](/images/springi/springi01-01.png)@Autowired와 @Inject의 경우에도 @Qualifier 어노테이션을 사용하면, 타입 이외의 방법으로도 연결 할 수 있다.
```
@Autowired
@Qualifier("chicken")
pirvate Brid penguin; //chicken타입으로 연결 
```
<br><br>

### 의존성 주입(Dependency Injection)
- @Autowired, @Injection을 붙이는 우치
    - 생성자
    - 필드
    - Setter 
- 최근버전은 어떤빈이되는 클래스의 생성자가 1개만 존재하며 생성자안의 매개변수 타입이 빈으로 등록되어있다면 Autowired를 생략할 수 있다.
- DI의 우선순위 생성자 → Setter(있다면) → 필드
    1. 반드시 필요하다면 생성자를 만들어서 의존성 주입
    2. Setter가 있다면 Setter에 의존성 주입(필요하지 않은 Setter를 만들어 의존성을 주입하면 Setter에 의해 의존성 변경의 여지를 주는것으로 위험한 코딩이다.)
    3. Setter가 없다면 필드
 <br><br>