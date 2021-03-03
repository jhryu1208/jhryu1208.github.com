---
layout: post
title:  "[ORACLE] 1.User and Schema"
subtitle: "[ORACLE] 1.User and Schema"
categories: devlang
tags: (dbms)oracle
mathjax: true
comments: true
---
- 계정과 스키마
- 오라클 데이터베이스 접속 및 SCOTT 계정 설정
- 사용자 계정

---

### [ 계정과 스키마 ]

#### <U>Schema</U>

- Schema(스키마) : <b>스키마 오브젝트</b>들의 집합
- Schema Obeject(스키마 오브젝트) : <b> 테이블, 뷰, 인덱스</b>와 같은 구조를 포함하는 논리적인 데이터 저장 구조를 가진 객체


- 하나의 스키마는 데이터베이스 user에 의해 소유된다.
- user를 생성할때 스키마도 따라 생성되며, 하나의 user는 1개 이상의 스키마를 소유할 수 있다.
- 다른 스키마안에 있는 오브젝트는 이름이 같아도 다른 오브젝트로 취급한다.  
  >  ex)  
  >  A user의 A schema( SCHOOL테이블 / JOB테이블 )  
  >  B user의 B schema( SCHOOL테이블 / JOB테이블 )  
  >  -> A schema의 SCHOOL테이블과 JOB테이블 ≠ B schema의 SCHOOL테이블과 JOB테이블

#### <U>User</U>

- 계정 정보는 '데이터 딕셔너리'에 저장되므로, 일반 데이터를 백업하는 것처럼 백업할 수 없다.
- 계정을 생성할때 사용했던 DDL문을 백업해두거나 DDL문을 추출해야한다.

---
### [ 오라클 데이터베이스 접속 및 SCOTT 계정 설정 ]
- 참조 : https://docs.oracle.com/cd/B19306_01/server.102/b14200/statements_8003.htm


- <b>system계정 로그인</b>
    - 최고 권한을 가진 SYS 계정의 데이터베이스 관리 권한을 위임받는 계정
    - system은 id를 의미하며, oracle은 처음 설치시 입력한 비밀번호를 의미한다.
    ```
    sqlplus system/oracle
    ```


- <b>SCOTT 계정 설정</b>
    - 오라클 데이터베이스에서는 기본적인 학습을 위한 테이블과 데이터가 미리 구현되어 있는 SCOTT계정을 제공한다.
    - 이 계정은 오라클 데이터베이스 설치 직후에는 잠겨 있는 상태이므로 사용 가능 상태로 전환해야한다.
    - 주의) <U>비밀번호</U>는 <U>대/소문자를 구별</U>하므로 반드시 정확히 확인하고 지정한 대로 입력해야한다.  
    - 참조) 하지만, SQL문은 대/소문자를 구별하지 않는다.  
    그래서 대/소문자가 섞여 있는 프로그래밍 언어와 SQL문을 구분하고 가독성을 높이기 위해 실무에서는 SQL문 전체를 대문자로 사용하는 경우를 흔하게 볼 수 있다.
    ```
    ALTER USER SCOTT     # SCOTT 계정을 변경하겠다는 의미
    IDENTIFIED BY TIGER  # 접속 비밀번호를 TIGER로 지정한다는 의미
    ACCOUNT UNLOCK;      # 계정을 잠기지 않은 상태로 전환한다는 의미
    ```
    
    
- <b>SCOTT계정으로 재접속</b>
    
    ```
    CONN scott/TIGER;
    ```

- <b>sqlplus종료</b>
    ```
    EXIT
    ```

- <b>SCOTT 계정 로그인</b>
    ```
    sqlplus scott/TIGER
    ```

---
### [ 사용자 계정 ]

- <b>사용자 생성</b>
    - 생성된 사용자로 로그인하기 위해서는 권한을 부여해야한다.
    - 오라클 12c로 넘어오면서 계정이름 앞에 `c##`을 붙여줘야 공통사용자가 생성가능하다.
   
    ```
    CREATE USER c##계정이름 IDENTIFIED BY 계정 비번
    ```

- <b>사용자 권한 부여</b>
    - 권한은 관리자계정을 통해 부여 가능하다.
    - 새로 생성된 user는 권한을 부여해야만 로그인 가능하다.
    - 부여 권한 중 `DBA`는 최상위 권한이다. (업계에서는 DBA로 권한을 주는경우가 드물다.)

    ```
    GRANT 사용자등급 TO c##계정이름
    ```
    
    - object 권한 종류
    
    
|object권한|허가된 내용|
|---|---|
|<b>`ALTER`</b>|객체에 대해 변경할 수 있는 권한|
|<b>`COMMENT`</b>|객체에 대해 COMMENT할 수 있는 권한|
|<b>`DELETE`</b>|객체에 대해 자료를 삭제할 수 있는 권한|
|<b>`GRANT`</b>|객체에 대해 GRANT할 수 있도록 하는 권한|
|<b>`INDEX`</b>|인덱스를 생성할 수 있는 권한|
|<b>`INSERT`</b>|데이터를 삽입할 수 있는 권한|
|<b>`RENAME`</b>|이름을 변경할 수 있는 권한|
|<b>`SELECT`</b>|데이터를 조회할 수 있는 권한|
|<b>`UPDATE`</b>|데이터를 갱신할 수 있는 권한|
|<b>`REFERENCE`</b>|데이터를 참조할 수 있는 권한|
|<b>`EXECUTE`</b>|프로시저, 함수, 패키지를 사용할 수 있는 권한|

- <b>사용자 권한 취소</b>
   ```
   REVOKE 사용자등급 FROM c##계정이름
   ```


<br>

---

### References

- 이지훈, 『Do it! 오라클로 배우는 데이터베이스』, 이지스퍼블리싱 (2018)

