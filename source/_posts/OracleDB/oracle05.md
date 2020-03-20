---
title: Reference 형 데이터, ROWTYPE, 제어문, 반복문, 예외처리, Cursor, for문, Procedure
date: 2018-12-03 20:24:51
tags: OracleDB
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
![DB](/images/oracledb_logo.png)
# KOSTA DAY24
## DataBase
<!-- more -->
### Reference형 데이터 타입
- 선언방법   
```
[변수명] [ ] [해당데이터가 받을 컬럼명]%TYPE;
```

![DB](/images/database/DB05-01.png)
- 상기 이미지와 같이 해당 데이터의 데이터타입을 컬럼으로 설정

#### 예제
1. 사원테이블에서 201번 사원의 이름과 이메일을 출력하라.(레퍼런스형)
![DB](/images/database/DB05-02.png)
- 찾을 사원번호를 v_search에 선언 후, 출력할 내용들을 reference 타입으로 선언 후, where문을 사용하여 각각의 내용들을 변수에 넣어 호출하였다.

2. employees 테이블에서 사원번호가 큰 사원을 찾은후 사원번호 +1 구해서 사원을 추가하라.(insert)   
사원명(last_name) = > hong gil dong   
이메일 => aa@aa.com   
입사일자 => sysdate   
job_id => ad_vp

![DB](/images/database/DB05-03.png)
- 사원번호가 가장 최근(가장높은)번호를 찾은 후, 최고값에 +1에 넣어 다른값들을 INSERT 하였다.
<br><br>

### ROWTYPE
- 1개의 행값을 갖는다.(선언)
- 선언방법
```
수명][ ][테이블명]%ROWTYPE
```

#### 예제
![DB](/images/database/DB05-04.png)
<br>

### 제어문

#### 예시
10~120 중 임의이 부서 버호를 받아서 해당부서의 평균 급여에 따라서 등급이 출력되도록 하자.      
1~3000 낮음   
3000~ 6000 적정   
6000이상 우수

- ROUND(DBMS_RANDOM.VALUE(10, 120), -1); 랜덤함수
- IF문
![DB](/images/database/DB05-05.png)

- IF, ELSIF, ELSE문
![DB](/images/database/DB05-06.png)

- CASE WHEN 문
![DB](/images/database/DB05-07.png)
<br>

### 반복문
- LOOP문
![DB](/images/database/DB05-08.png)

- WHILE문
![DB](/images/database/DB05-09.png)

- for문
![DB](/images/database/DB05-10.png)
<br>

### 예외처리
- PL/SQL의 오류를 예외라고 한다.   
(컴파일시 문접적 오류: 실행시 발생하는 오류)
- 미리 정의 된 오라클 서버 예외 : 선언할 필요도 없고, 예외 발생 시, 예외절로 자동이용
- 사용자 예외 강제 발생 : 선어부에서 예외를 정의 실행부에서 RAISE문 사용하여 예외발생

#### 예시
- 미리 정의 된 오라클 서버 예외
![DB](/images/database/DB05-11.png)

- 사용자 강제 예외 발생
![DB](/images/database/DB05-12.png)

#### 예제
신입사원 입력 시 잘못된 부서번호에 대해서 사용자 예외 처리를 하시오(오류남)
![DB](/images/database/DB05-13.png)
- 먼저 예외발생을 위해 예외를 선언(ex_invalid_dept_id)
- count 함수를 사용해 해당부서의 인원을 검색하여 그 값을 v_cnt에 대입
- v_cnt 값이 0이면 해당부서가 없는것이므로, RAISE문 사용하여 예외발생
<br><br>

### CURSOR
- SELECT문 결과 집합이 다중로우일 경우 반드시 커서를 이용하여 처리   
(RAWTYPE과 함께 사용)

#### 예시
![DB](/images/database/DB05-14.png)

#### 예제
서를 이용하여 사원의 정보를 출력하세요(사원번호, 사원이름, 급여, 급여누계)
![DB](/images/database/DB05-15.png)
- total salary 선언
- CURSOR 선언
- CURSOR를 받을 데이터 선언
- 루프문을 사용해 삽입(FETCH, INTO, EXIT WHEN)   
\#%NOTFOUND : 더이상 없을때
- LOOP문은 for문을 대체 가능 하기 이미지 참고

![DB](/images/database/DB05-16.png)
<br>

### 프로시저(Procedure)
자주 사용되는 PL/SQL 블록을 재사용하기 위해 모듈화 하는 것.

- 선언방법
```
CREATE OR REPLACE PROCEDURE [프로시저명][(변수명 IN 받을 데이터타입 컬럼명)]IS
```

- 실행방법
```
EXECUTE [프로시저명][입력값];
```

#### 예시
부서번호를 받아 사원리스트를 출력하라.
![DB](/images/database/DB05-17.png)
- 부서번호를 입력받는 프로시저 생성 (listbyDept)
- 부서사람이 여러명이기 때문에 다중행 입력받기위해 CURSOR사용
- EXECUTE 사용하여 실행
<br><br>
