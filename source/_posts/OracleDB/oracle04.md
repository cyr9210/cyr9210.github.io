---
title: 다중컬럼 다중로우, FROM절 서브쿼리, 인덱스, 시퀀스, 뷰, PLSQL
date: 2018-11-29 19:58:06
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
# KOSTA DAY23
## DataBase
<!-- more -->
### 다중컬럼 다중로우 서브쿼리
서브쿼리의 결과가 여러 컬럼 및 여러 행인 서브쿼리

#### 예제
1. 직무별 최대 급여자의 사원내역을 출력하라.
![DB](/images/database/DB04-01.png)
- 상기 이미지와 같이 IN을 사용하여 직무별 최대급여를 포함하는 모든 사원의 정보를 호출하였다.   
그러나, 타직무의 최대값과 같은 급여를 받는 사람도 호출되었다.
- 그 문제를 해결하기위해 job_id라는 컬럼을 추가로 호출하고 메인쿼리에서도 받을 수 있도록 하였다.

2. 01번 부서원들과 보너스가 같은 사원을 검색하라.
![DB](/images/database/DB04-02.png)

3. 부서번호 30번 최대급여자 보다 높은 사원을 출력하라.
![DB](/images/database/DB04-03.png)

#### IN, ANY, ALL
- IN : 검색된 값 중에 하나만 일치하면 참
- ANY : 검색된 값 중에 조건에 맞는 것이 하나 이상 있으면 참
- ALL : 검색된 값과 조건에 모두 일치해야함
- 컬럼 > ANY(서브쿼리) : 컬럼 > MAX : 가장큰값 보다 크다.
- 컬럼 < ALL(서브쿼리) : 컬럼 < MIN : 가장 작은값 보다 작다.
- 컬럼 > ANY(서브쿼리) : 컬럼 > MIN : 가장 작은값 보다 크다.
- 컬럼 < ANY(서브쿼리) : 컬럼 < MIN : 가장 큰값 보다 작다.

#### 예제
- 10번 부서에 가장 작은 급여자보다 작게 받는 급여자를 출력하라
![DB](/images/database/DB04-04.png)
<br>

### FROM절 서브쿼리(TOP-N SQL)
FROM절에서 사용되는 서브쿼리를 **인라인 뷰(Inline View)** 라고 한다.

#### 예제
![DB](/images/database/DB04-05.png)
- 서브쿼리안에서 정렬시킨 후, ROWNUM 값으로 호출
- ROWNUM값으로 정렬된 서브쿼리의 값 중, 중간에 위치한 값을 호출하기 위해서는 ROWNUM으로 정렬 된 서브쿼리가 필요하다.   
이해를 돕기 위해 아래 예시를 보자.

![DB](/images/database/DB04-06.png)
- BETWEEN AND 함수를 써서 5~9 번째 행을 호출하려 했지만 아무값도 나오지 않았다.

![DB](/images/database/DB04-07.png)
- ROWNUM 값과 함께 호출하는 서브쿼리를 만든후에 BETWEEN AND 함수를 사용하여 호출할 수 있다.

<br><br>

### 인덱스
어떤 데이터가 어디에 있다는 위치정보를 가진 주소록

- 인덱스 생성 → 테이블의 데이터에 대한 Indexing → B-tree구조로 형성

> **B-tree 란?**
B-트리(B-tree)는 데이터베이스와 파일 시스템에서 널리 사용되는 트리 자료구조의 일종으로, 이진 트리를 확장해 하나의 노드가 가질 수 있는 자식 노드의 최대 숫자가 2보다 큰 트리 구조이다.

![DB](/images/database/DB04-08.png)

#### 인덱스를 사용하는 이유
- 일정한 검색 속도를 유지 할 수 있다.
- 데이터량이 많은 경우, 검색속도를 향상 시킬 수 있다.
- WHERE절이나 JOIN 조건으로 자주 되는 컬럼에 사용 됨

#### 인덱스 생성방법
![DB](/images/database/DB04-09.png)
- Index의 이름은 관례적으로 talbe명_컬럼명_idx 로 생성한다.
- B_tree형성의 기준이되는 컬럼값은 보통 PRIMARY KEY값을 사용
<br><br>

### 시퀀스(SEQUENCE)
테이블 내의 유일한 숫자를 자동으로 생성하는 자동번호 발생기

#### 시퀀스를 사용하는 이유
- 오라클에서 데이터 베이스의 무결성을 위해 제약 조건을 설정하고, 그 중 행을 구분하기 위해 기본 키(PRIMARY KEY)를 설정한다.    
이때 데이터가 입력되는 순서를 기본키로 지정한다고 하였을 때, 사용자가 직접 입력해야 한다면 어려움이 있을 것이다.    
이 때, 시퀀스를 사용하요 키값을 설정한다면 사용자는 보다 편하게 작업이 가능하다.

#### 시퀀스 사용 시, 주의사항
- 시퀀스는 이전단계로 돌아올 방법이 없음. 생성에 실수가 있어도 돌이킬 수 없다.
- 때문에 개발단계에서는 일단생성을 하고 추후 수정을 위해서는 DROP 후, 다시 생성해야한다.

#### 시퀀스 사용방법
![DB](/images/database/DB04-10.png)
```
CREATE SEQUENCE 시퀀스 이름;
/*sequence.CURRVAL => dual테이블.=>sequence로 생성된 번호가 몇 번까지 있는지 알려줌*/

/*시퀀스 사용*/
INSERT INTO board VALUES (seq_seq.NEXTVAL, ‘a1’, ‘a’, ‘a’, sysdate, 0);
```
<br>

### 뷰
반복적으로 쓰이는 문을 뷰로 생성하여 테이블처럼 사용 가능하게 만든 것

- 보안적인 내용을 다룸   
ex) 부서에 테이블을 제공할때, 급여 및 주민등록번호 등 보안관련 컬럼은 빼고 뷰로 생성하여 뷰를 제공

- 생성방법
```
CREATE OR REPLACE VIEW [뷰명] AS 쿼리;
```
![DB](/images/database/DB04-11.png)
<br>

### PL/SQL
Procedural Language Extension to Structured Query Language
SQL에 프로그래밍 언어기능을 위해 절차적으로 확장한 언어
![DB](/images/database/DB04-12.png)

- output에서 출력하기 위해 set serveroutput on; 설정필요

```
set serveroutput on;
DECLARE
/*변수선언( — : 주석처리)*/
v_no NUMBER(3) := 10;
v_hiredate VARCHAR(30) := TO_CHAR(sysdate, ‘YYYY/MM/DD’);
/*상수선언*/
c_message CONSTANT VARCHAR(50) := ‘안녕하세요. PL/SQL’;
/*실행부*/
BEGIN
/*DBMS_OUTPUT패키지 PUT_LINE 프로시저를 이용하여 결과 출력*/
dbms_output.put_line(‘오늘부터 pl/sql 수업’);
dbms_output.put_line(c_message);
dbms_output.put_line(v_hiredate);
END;
```

#### 예제
- 사원번호 100번에 해당하는 사원이름과 부서명을 출력하세요.
![DB](/images/database/DB04-13.png)
<br>
