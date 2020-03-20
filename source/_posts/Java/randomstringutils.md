---
title: 난수 생성
date: 2019-10-26 15:40:23
tags: Java
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

# Java

## 난수생성
<!-- more -->
### RandomStringUtils

- org.apache.commons.lang3.RandomStringUtils

#### 임의의 문자 생성

```java
RandomStringUtils.random(int count);
```

- 임의의 문자를 count 만큼 랜덤으로 생성해준다.
- count : 문자열의 길이

```java
RandomStringsUtils.random(int count, int start, int end, boolean letters, boolean number)
```

- start부터 end까지의 아스키코드 문자를 사용하여 count만큼 랜덤으로 생성해준다.
- count : 문자열의 길이
- start : 아스키코드 처음
- end :  아스키코드 마지막
- letters : true 문자만 생성
- numbers : true 숫자만 생성

####  영문 대소문자 랜덤 문자열 생성

```java
RandomStringUtils.randomAlphabetic(int count);
```

- 영문 대소문자를 count만큼 랜덤으로 생성한다.

#### 숫자 랜덤 문자열 생성

```java
RandomStringUtils.randomNumeric(int count);
```

- 숫자를 count 만큼 랜덤으로 생성한다.

#### 영문 대소문자 + 숫자 랜덤 문자열 생성

```java
RandomStringUtils.randomAlphanumeric(int count);
```

- 대소문자, 숫자를 사용하여 count만큼 랜덤으로 생성한다.

#### 아스키코드 문자열 생성

```java
RandomStringUtils.randomAscii(int count);
```

- 아스키코드 32("") ~ 126(~)사이의 코드를 count만큼 랜덤으로 생성한다.

<br><br>