---
title: 스프링 핵심기술03 - 데이터 바인딩
date: 2019-03-22 14:17:41
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
# 스프링 프레임워크 핵심기술03(inflearn) - 백기선 
## Springframework
<!-- more -->
### 데이터바인딩 추상화
- 기술적인 관점: 프로퍼티 값을 타겟 객체에 설정하는 기능
- 사용자 관점: 사용자 입력값을 애플리케이션 도메인 모델에 동적으로 변환해 넣어주는 기능.
- **해석하자면: 입력값은 대부분 “문자열”인데, 그 값을 객체가 가지고 있는 int, long, Boolean, Date 등 심지어 Event, Book 같은 도메인 타입으로도 변환해서 넣어주는 기능.**
- Spring web MVC에만 특화된것이 아니다.
- 여러 Interface로 추상화 했다.
 
#### PropertyEditor

- **스프링 3.0 이전까지 DataBinder가 변환 작업에 사용하던 인터페이스**
- 쓰레드-세이프 하지 않음 (상태 정보 저장 하고 있음, 따라서 **싱글톤 빈으로 등록하면 안된다.**)
    - 여러 쓰레드들이 상태정보를 공유한다.
    - 만약 빈을 사용한다면 Thread-Scope로 사용할 수 있겠지만, **빈으로 등록 안하는것이 좋다.**   
    - 그럼 어떻게 사용하는가   
    → InitBinder를 사용하는것이 좋다.
- Object와 String 간의 변환만 할 수 있어, 사용 범위가 제한적이다.

##### 예제
- 객체 생성
![springcore](/images/springc/springcore03-01.png)

- PropertyEditor를 implements 하는것은  오버라이딩할 메소드가 많다.      
PropertyEditorSupport를 상하여 필요에 맞도록 적용
![springcore](/images/springc/springcore03-02.png)

- 이벤트 컨트롤러 작성
![springcore](/images/springc/springcore03-03.png)

- 테스터 작성
매칭되는 에디터가 없어서 에러가 발생한다. 
![springcore](/images/springc/springcore03-04.png)

- 앞선 설명과 같이 빈으로 등록하지 않고 InitBinder사용하여 메소드 생성 및 테스트를 실행한다.
Evnet 클래스를 EventEditor를 사용하여 데이터바인딩하겠다는 뜻
![springcore](/images/springc/springcore03-05.png)
<br>

### Converter와 Formatter
#### Converter
![springcore](/images/springc/springcore03-06.png)
- S 타입을 T 타입으로 변환할 수 있는 매우 일반적인 변환기.
- 상태 정보 없음 = Stateless = 쓰레드세이프 (→ bean으로 등록이 가능하다.)
- ConverterRegistry에 등록해서 사용
![springcore](/images/springc/springcore03-07.png)(빈으롱 등록하지 않았을 때 사용방법이다.)
- 기본적인 Integer 등의 타입은 기본적으로 지원해준다.

#### Formatter
![springcore](/images/springc/springcore03-08.png)
- PropertyEditor 대체제
- Object와 String 간의 변환을 담당한다.
- 쓰레드 세이프하여 빈등록이 가능하다.
    - 즉, 다른빈들도 등록이 가능하다.(예시의 이미지의 MessageSource와 같은) 
- 문자열을 Locale에 따라 다국화하는 기능도 제공한다. (optional)
- FormatterRegistry에 등록해서 사용
![springcore](/images/springc/springcore03-09.png)(빈으롱 등록하지 않았을 때 사용방법이다.)

#### ConversionService
![springcore](/images/springc/springcore03-10.png)

- 실제 변환 작업은 이 인터페이스를 통해서 쓰레드-세이프하게 사용할 수 있음.
- 스프링 MVC, 빈 (value) 설정, SpEL에서 사용한다.
- DefaultFormattingConversionService
- FormatterRegistry는 **ConverterResgistry를 상속하여, Converter의 기능도 할 수 있다.**
따라서, Formatter를 쓰는것을 추천한다.
- ConversionService
- 여러 기본 컴버터와 포매터 등록 해 줌.

##### 스프링 부트
- 웹 애플리케이션인 경우에 **DefaultFormattingConversionSerivce를 상속하여 만든 WebConversionService를 빈으로 등록**해 준다.
    - WebConversionService의 경우 좀 더 많은 기능을 제공한다. (money, JODA타임 등)
- Formatter와 Converter 빈을 찾아 자동으로 등록해 준다.
![springcore](/images/springc/springcore03-11.png)

- 테스트방법
    - 계층형 테스트로 웹과 관련된 빈만 테스트로 등록해준다.   
    주로 Controller만.. 즉, Converter나 Formatter 제대로 등록이 안되면 오류발생
    직접등록이 필요하다.
    ![springcore](/images/springc/springcore03-12.png)
- 등록되어있는 ConversionService
    ![springcore](/images/springc/springcore03-13.png)
    - 많은 converter와 formatter가 기본적으로 등록되어있다.
<br><br>    