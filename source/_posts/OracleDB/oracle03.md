---
title: ANSI JOIN, INNER JOIN, OUTER JOIN, SELF JOIN, 서브쿼리
date: 2018-11-28 17:36:11
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
# KOSTA DAY22
## DataBase
<!-- more -->
### ANSI JOIN
- FROM절에서 INNER JOIN 구문 사용
- 조인조건은 ON절에 명시
- 조인조건 외 조건은 기존대로 WHERE절에 명시
- 3개이상 조인
```
테이블1 JOIN 테이블2
ON 공통컬럼1 = 공통컬럼1
JOIN 테이블3
ON 공통컬럼2 = 공통컬럼2
```

#### 예제
- 기존
```
SELECT e.employee_id, e.department_id, d.department_name
FROM employees e, departments d
WHERE e.department_id = d.department_id
AND last_name = ‘King’;
```

- ANSI JOIN
```
SELECT e.employee_id, e.department_id, d.department_name
FROM employees e INNER JOIN departments d
ON e.department_id = d.department_id
WHERE last_name = ‘King’;
```
<br>

### INNER JOIN
![DB](/images/database/DB03-01.png)
- 교집합에 해당한다고 볼 수 있다.
<br><br>

### SELF JOIN
TABLE을 자기 자신과 조인시키는 것

#### 예제
- ‘seattle’ (city)에 근무하는 사원의 이름, 부서번호 직종번호, 직종이름, 도시이름 출력
```
SELECT e.last_name || ‘의 매니저는’ || m.last_name || ‘이다.’
FROM employees e INNER JOIN employees m
ON e.manager_id = m.employee_id
WHERE e.last_name = ‘King’;
```
<br>

### OUTER JOIN
조인을 생성하려 하는 두개의 테이블의 두개 컬럼에서 공통된 값이 없을때 사용

#### 예제
```
SELECT * FROM employees; //=> 107row

SELECT e.employee_id, e.department_id, d.department_name
FROM employees e, departments d
WHERE e.department_id = d.department_id; //=> 106row
```

- 부서배치 받지 않은 사원이 있을 수 있다. 이럴 때, OUTER JOIN 사용

```
SELECT e.employee_id, e.department_id, d.department_name
FROM employees e, departments d
WHERE e.department_id = d.department_id(+);
/* 누락된 곳 반대편에 (+) */

OR

ANSI JOIN
SELECT e.employee_id, e.department_id, d.department_name
FROM employees e LEFT JOIN departments d
ON e.department_id = d.department_id
/* 누락된 곳으로 방향설정 */
```

![DB](/images/database/DB03-02.png)
<br>

### 서브쿼리
하나의 SQL문 안에 포함되어 있는 또 다른 SQL 문

#### 서브쿼리 작성 순서
- 서브쿼리문 먼저 작성
- 메인쿼리 작성
- 메인쿼리 필요 위치에 서브쿼리문 삽입

#### WHERE절 서브쿼리
- WHERE절에 서브 쿼리를 사용

#### 예제
- Chen’ 보다 salary를 많이 받는 직원의 내역을 출력하라.
```
SELECT last_name, salary
FROM employees
WHERE salary > (SELECT salary FROM employees WHERE last_name = ‘Chen’);
```
<br>

### 문제
1. 학생중 기말고사 성적이 가장 낮은 학생의 정보를 검색하라
```
SELECT s.sname, s.sno, s.major FROM student s, score r
WHERE s.SNO = r.SNO
GROUP BY s.sname, s.sno, s.major
HAVING sum(result) = (SELECT min(sum(result)) FROM score 
                        GROUP BY sno);
```

2. 화학과 1학년 학생중에 평점이 평균이하인 학생을 검색하라.
```
SELECT sname, sno, avr FROM student
WHERE major = ‘화학’
AND syear = 1
GROUP BY sname, sno, avr
HAVING avr <= (SELECT avg(sum(avr)) FROM student
                WHERE MAJOR = ‘화학’
                AND SYEAR = 1
                GROUP BY sno);
```
<br>


