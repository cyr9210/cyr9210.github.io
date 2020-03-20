---
title: HAVING, 함수, DDL, DML, 제약조건, JOIN
date: 2018-11-27 18:02:10
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
# KOSTA DAY21
## DataBase
<!-- more -->
### HAVING
- 조건
    - 전체 그룹에서 일부의 그룹만 추출하기 위해 사용된다.
    - WHERE와 비슷하게 사용된다.

#### 예제
![DB](/images/database/DB02-01.png)
- WHERE절 사용
```
SELECT department_id, avg(salary) FROM employees
GROUP BY department_id
WHERE avg(salary) < 5000;
```

- HAVING절 사용
```
SELECT department_id, avg(salary) FROM employees
GROUP BY department_id
HAVING avg(salary) < 5000;
```
<br><br>

### 함수
#### 문자함수
![DB](/images/database/DB02-02.png)
- LOWER() : 소문자 문자 변환
- UPPER() : 대문자 문자 변환
- SUBSTR(index, n) : 부분 문자열 추출(INDEX 1부터시작, 문자갯수)

#### 예제
![DB](/images/database/DB02-03.png)

#### 숫자함수
- MOD : 나머지값 변환
- ROUND(n1, n2) : 반올림 값 리턴 (숫자, 소수자리수 -1 = 1의자리)
- CEIL() : 올림
- FLOOR() : 내림

#### 날짜함수
- SYSDATE : 현재 날짜를 출력하는 함수
```
SELECT sysdate-1 “어제”, sysdate “오늘”, sysdate+1 “내일” FROM dual;
```

- <퀴즈>신입사원의 근속년을 출력하라.
```
SELECT first_name, last_name, hire_date “입사일”, round((SYSDATE-HIRE_DATE)/365) “근속년수” FROM employees;
```

- <퀴즈> 07년에 입사한 사원의 목록을 출력하라. TO_CHAR 사용
```
//(LIKE 함수 사용)
SELECT hire_date FROM employees
WHERE hire_date LIKE ‘07%’;

//(TO_CHAR사용)
SELECT hire_date FROM employees
WHERE TO_CHAR(hire_date, ‘YYYY’) = ‘2007’;
```

#### NVL()
NULL을 0 또는 다른 값으로 반환

- 퀴즈> 사원의 연봉을 출력하라.(월급여 *12) + (월급여*12*커미션)
```
SELECT last_name, salary, nvl(commission_pct, 0), salary+salary*nvl(commission_pct, 0) “실연봉” from employees;
```

#### DECODE()
괄호 안의 값을 변환
```
SELECT job_id, DECODE(job_id, ‘SH_CLERK’, ‘Sales Dept’,’SA_MAN’, ‘Sales Dept’, ‘Another’) FROM employees;
```

#### CASEWHEN
WHEN뒤의 값을 THEN뒤의 값으로 변환
- DECODE와 비슷한 역할을 한다.
![DB](/images/database/DB02-04.png)
```
SELECT job_id, 
CASE job_id
WHEN ‘SA_MAN’ THEN ‘Sales Dept’
WHEN ‘SH_CLERK’ THEN ‘Sales Dept’ 
ELSE ‘Another2’
END
From employees;
```
<br>

### DDL
Data Definition Language 데이터 정의어

- 테이블 생성
```
CREATE TABLE 테이블명(
컬럼명 데이터형,
컬러명 데이터형
)
```

>**데이터형**
문자형 : CHAR(size), VARCHAR2(size) 가변형(사이즈 알아서 수정)
숫자형 : NUMBER
날짜형 : DATE, TIMESTAMP(좀 더 디테일한 시간)
대용량(이미지, 파일) : LOB(문자데이터), BLOB(이진형데이터, ~4GB)

- 테이블복사
```
CREATE TALBE emp01
AS SELECT * FROM employees;
```

- 테이블 구조 복사
```
CREATE TABLE emp02
AS SELECT * FROM employees WHERE 1=0;
```

#### 테이블 구조 수정
- 컬럼추가
```
ALTER TABLE emp02
ADD(job VARCHAR2(50);
```

- 컬럼수정
```
ALTER TABLE emp02
MODIFY(job VARCHAR2(100));
```

- 컬럼삭제
```
ALTER TABLE emp02
DROP COLUMN job;
```

- 테이블 삭제
```
DROP TABLE emp02 PURGE;
```

- 테이블 이름 변경
```
RENAME emp01 TO emp00;
```

- 테이블 데이터만 삭제
```
TRUNCATE TABLE emp00; // → DCL(트랙잭션 적용 불가, 복구불가)
DELETE FROM emp00; // → DML(트랙잭션 적용 가능)
// ※트랜잭션 : 상호작용 단위
```
<br><br>

### DML
(INSERT, UPDATE, DELETE)

#### INSERT문
```
INSERT INTO dept01 VALUES(300, ‘DEVELOPER’, 100, 10);

INSERT INTO dept01(department_id, department_name)
VALUES(310, ‘Innovation’);
```

#### UPDATE문
```
UPDATE 테이블명 SET 컬럼명 = 수정값, 컬럼명=수정값
WHERE 수정대상;
```

#### DELETE문
```
DLETE FROM 테이블명
WHERE 삭제대상;
```
<br>

### 제약조건
데이터를 추가,삭제,수정 하는 가운데 데이터의 무결성을 유지하기위해 사용

#### NOT NULL
- NULL값 입력 시 에러발생

#### UNIQUE
- 같은값 입력 불가

#### PRIMARY KEY
- UNIQUE + NOT NULL
- 주키역할
- 하나 이상 존재해야 TABLE의 역할을 한다.

#### CHECK()
- 괄호안의 보기중 입력하지 않으면 에러발생
```
CREATE TABLE emp08(
empno NUMBER,
ename VARCHAR2(20) NOT NULL,
job VARCHAR2(20),
deptno NUMBER,
gender char(1) CHECK(gender IN(‘M’, ‘F’));
```

#### FOREIGN KEY
- 다른 테이블을 참조값으로 가져옴

#### 예제
- 테이블 생성 동시 조건
```
CREATE TABLE emp03(
empno NUMBER PRIMARY KEY,
ename VARCHAR2(20) NOT NULL,
job VARCHAR2(20),
deptno NUMBER REFERENCES departments(department_id));
```

- 테이블 레벨방식
```
CREATE TABLE emp06(
empno NUMBER,
ename VARCHAR2(20) NOT NULL,
job VARCHAR2(20),
deptno NUMBER,
CONSTRAINT emp06_empno_pk PRIMARY KEY(empno),
CONSTRAINT emp06_deptno_fk
FOREIGN KEY(deptno)
REFERENCES departments(department_id));
```

- 테이블 수정방식
```
CREATE TABLE emp07(
empno NUMBER,
ename VARCHAR2(20) NOT NULL,
job VARCHAR2(20),
deptno NUMBER);

ALTER TABLE emp07
ADD CONSTRAINT emp07_empno_pk PRIMARY KEY(empno);
```
<br>

### JOIN
2개 이상의 테이블에서 데이터를 검색하기 위해서 사용

- INNER JOIN : 같은 컬럼 값에 관하여
- JOIN 사용방법
    1. 내가 원하는 데이터가 무엇인가?(컬럼목록작성)
    2. 원하는 데이터가 어느 테이블에 있는가?
    3. 여러테이블에 있다면 각각의 테이블에 공통된 컬럼이 무엇인가?

#### 예제
- ‘송강’ 교수가 강의하는 과목을 검색해라
    1. 교수번호(pno), 교수이름(pname), 과목명(cname)
    2. professor, course
    3. pno
```
SELECT p.pno, p.pname, c.cname
FROM professor p,course c
WHERE p.pno = c.pno
AND pname = ‘송강’;
```
<br>
