---
title: KOSTA 필기시험(Oracle DB)
date: 2018-12-04 21:09:07
tags: Interview
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
# KOSTA DAY28
## DataBase
<!-- more -->
DB진도를 마치며 Java때와 마찬가지로 필기시험을 진행하였다.

### 필기시험
![DB](/images/database/OracleTest01.png)
- 체크를 한 부분이 틀리거나, 정답은 썼지만 확실하지 않은 부분

#### 정리
10. 인덱스를 사용하는 목적과 인덱스 생성과정을 서술하시오.
- 목적 
    - 일정한 검색 속도 유지
    - 데이터량이 많은 경우 검색속도 향상 (WHERE절 및 JOIN조건이 많이 들어가는 경우에 사용)

- 생성과정
    - 인덱스 생성 → 테이블의 데이터에 대한 Indexing → B-tree구조로 형성

- 생성방법
```
CREATE INDEX index_name
ON table_name(column_name);
```

12. 뷰를 사용하는 목적과 용도를 서술하시오.
- 목적
    - 반본적으로 사용되는 문을 뷰로 생성하여 테이블처럼 사용가능하게 만듬

- 용도
    - 보안적인 내용을 다룰때 사용   
    ex) 부서에 테이블 제공시 보안사항을 제외한 뷰를 생성하여 제공

- 생성방법
```
CREATE OR REPLACE VIEW view_name AS
뷰내용;

DESC view_name; → 뷰의 컬럼 내용보기
```

13. 정규화 3가지 서술하시오.
    - 1차 정규화 : 복수의 속성값을 가진 속성을 분리
    - 2차 정규화 : 주식별자에 종속적이지 않은 속성을 분리 
    - 3차 정규화 : 일반속성에 종속적인 속성을 분리

14. emp_empno_seq 시퀀스를 생성하였다. 시퀀스를 증가시키기 위한 sql을 작성하여라.
- 시퀀스 사용 목적
    - PRIMARY KEY 값을 입력 시, 시퀀스를 입력하면 사용자가 보다 편하게 작업이 가능하다.

- 시퀀스 사용방법
```
CREATE SEQUENCE seq_name
START WITH 시작번호
INCREMENT BY 증가량;
```

- 사용
```
/*seq_name.nextval*/
INSERT INTO board VALUES (seq_seq.NEXTVAL, ‘a1’, ‘a’, ‘a’, sysdate, 0);
```

15. 엔티티타입 추출 원칙
- 업무에 필요/관리항목
- 식별자에의해 식별가능
- 영속적으로 존재하는 엔티티의 집합(엔티티(행?)가 두개이상)
- 업무 프로세스에 이용
- 반드시 속성이 있어야한다.
- 최소 한개이상의 관계필요 (예외가 있을 수 있음 )
<br><br>