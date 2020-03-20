---
title: 데이터베이스, Oracle , SELECT
date: 2018-11-26 13:26:15
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
# KOSTA DAY20
## DataBase
<!-- more -->
### 데이터베이스
서로 연관성을 가지며, 중복없이 지속적으로 유지 관리해야 할 유용한 데이터들의 집합
- 데이터 베이스 이전에 데이터 저장을 위해서 파일시스템을 사용했으나, 많은 문제점이 있었다.
    - 중복 데이터 저장 문제 발생
    - 대용량 데이터 관리 역부족
    - 데이터 공유 어려움 (다중 사용자)
    - 보안 취약
- 상기와 같은 문제점을 극복하고 대량의 데이터를 체계적으로 저장 및 관리하기위해 만들어졌다.

#### DBMS
Database Management System(데이터베이스 관리시스템)
대용량 데이터를 쉽게 저장하고, 효율적으로 CRUD(Create Read Update Delete)할 수 있는 환경을 제공해주는 소프트웨어

- 대표적인 DBMS로는 Oracle, mySQL, MS-SQL, Informix, DB2가 있다.

#### 관계형 DBMS
![DB](/images/database/DB01-01.png)
- 가장대표적인 DBMS로 실세계 데이터들을 2차원적인 표(table)형식으로 표현
- 데이터 무결성, 트래잭션 처리 등 기본적 기능이 우수
- 질의어(Query Language)를 사용한 데이터 접근
- 정형화된 DATA를 다룸
- 비정형화된 DATA는 No-SQL에서 다룸(빅데이터에서 사용)
<br><br>

### Oracle
가장 대표적인 관계형 DBMS중의 하나이다.(회사명 과 DBMS 제품명 동일)

![DB](/images/database/DB01-02.png)

>KOSTA 교육 중에는 무료 버젼인 XE버젼을 사용 할 것이다.

#### Oracle xe 설치 및 데이터 접속/설
- windows : [medium 최용락](https://medium.com/@cyr9210/kosta-day20-%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4-oracle-select-b97fe4913f19)
- macOS기준 (참고블로그 : [맥(osx)에서 oracle DB 사용하기](https://banbanmumani.github.io/2018/01/05/osx%EC%97%90%EC%84%9CoracleDB%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0/))
    - docker 가입 및 설치
    - sql developer 다운로드
    - [도커허브](https://hub.docker.com/)를 통해서 원하는 프로그램을 검색한다.(oracle12c) 

    ![DB](/images/database/DB01-09.png)
    ```
    docker pull sath89/oracle-12c
    ```
    - 위의 명령어를 입력한다.
    
    ```
    docker run --name oracle12c -d -p 8080:8080 -p 1521:1521 -v ~/my/oracle/data:/u01/app/oracle sath89/oracle-12c
    ```
    - -v옵션을 주어 저장될 위치를 설정해준다. (-v옵션이 없을경우, 컨테이너를 중지하는순간 데이터가 삭제 된다.)      
    데이터는 ‘~/my/oracle/data'경로에 저장된다.
    
    ```
    docker rm oracle12c
    ```
    - 위의 명령어는 컨테이너를 삭제하는 명령어이다.(컨테이너 저장으로 인한 오류 발생 시, 삭제 후 다시 진행요함)
    
    ```
    docker logs -f  oracle12c
    ```
    - 위의 명령어를 입력할 시, 로그를 출력하여 초기화 진행상황을 알 수 있다. 100% 완료 될때까지 기다리기.
    - 터미널 재시작

- sql 접속 
    - docker exec -it oracle12c sqlplus
    ID : system
    PW : oracle
    
- sql developer 계정 생성
사용자이름 : system
pw : oracle

- kosta192 계정 생성
    ```
    create user kosta192 identified by 1234
    conn sys as sysdba (pw : oracle)
    grant connect, resource, dba to kosta192;
    conn kosta 192/1234
    ```
- scott 계정확인
    ```
    conn sys as sysdba; //(pw : oracle)
    SELECT username, account_status FROM dba_users WHERE username='SCOTT';
    ```
- scott계정 잠금해제 (참고블로그 : [Oracle 에서 scott 계정 락해제 방법](https://nabiro.tistory.com/160)) 
   
    ```
    alter user scott account unlock;
    ALTER user scott IDENTIFIED BY tiger;
    ```
<br><br>

### SELECT
![DB](/images/database/DB01-06.png)
- employees 테이블의 전체 데이터를 불러오기 
```
SELECT * FROM employees
```

- 중복된 데이터 제거
```
SELECT DISTINCT job_id FROM employees;
```

- 정렬 묶음 검색(ORDER BY 절)
```
SELECT ENO, ENAME, SAL FROM EMP
ORDER BY SAL DESC, ENO;
```

![DB](/images/database/DB01-07.png)
- 조건에 맞는 일부데이터(ROW) 불러오기 (WHERE절)
```
SELECT DEMPLOYEE_ID, LAST_NAME, HIRE_DATE FROM EMPLOYEES
WHERE LAST NAME = ‘King’;
```

- BETWEEN AND 연산자
```
SELECT employee_id, last_name, salary FROM employees
where salary BETWEEN 5000 AND 10000;
```

- OR 연산자
```
SELECT employee_id, last_name, job_id FROM employees
WHERE job_id = ‘FI_MGR’ OR job_id = ‘FI_ACCOUNT’;
```

- IN연산자
```
SELECT employee_id, last_name, job_id FROM employees
WHERE job_id IN (‘FI_MGR’, ‘FI_ACCOUNT’;
```

- NOT 연산자
```
SELECT department_id, department_name FROM departements
WHERE NOT department_id = 10;

SELECT department_id, department_name FROM departements
WHERE department_id <> 10;
```

- IS NOT NULL 연산자
```
SELECT employee_id, last_name, commission_pct FROM employees
WHERE commission_pct IS NOT NULL;
```

![DB](/images/database/DB01-08.png)
- LIKE 연산자 (%연산자와 함께 잘사용)
    - ‘김%’ = ‘김’으로 시작하는 모든 문자열
    - ‘%과% = ‘과’로끝나는 모든 문자열
    - ‘%김%’ = ‘김’이라는 문자를 포함하는 모든 문자열
    - ‘화_’ =’화’로 시작하는 2글자 문자열
    - ‘_등_’ = ‘등’이 가운데 들어간 3글자 문자열
- 그룹함수
    - sum(), avg(), max(), count()
    - SELECT sum(salary) FROM employees; =인건비 총합
- GROUP BY절
    - 부서별 평균 급여를 검색하라.
    ```
    SELECT department_id, avg(salary) FROM employees
    GROUP BY department_id;
    GROUP BY 절 사용시 컬럼값은 그룹함수 및 GROUP BY절 인자만 들어갈 수 있다.
    ```
