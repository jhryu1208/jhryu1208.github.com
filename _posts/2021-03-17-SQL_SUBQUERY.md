---
layout: post
title:  "[ORACLE] 7.SUBQUERY"
subtitle: "[ORACLE] 7.SUBQUERY"
categories: devlang
tags: (dbms)oracle
mathjax: true
comments: true
---
-  서브쿼리 
- 실행 결과가 하나인 단일행 서브쿼리
- 실행 결과가 여러 개인 다중행 서브쿼리
- 비교할 열이 여러 개인 다중열 서브쿼리
- FROM절에 사용하는 서브쿼리와 WITH절
- SELECT 절에 사용하는 서브쿼리

---

### [ 1. 서브쿼리 ]

#### (1) 서브쿼리란?
- `서브쿼리(subquery)` : SQL문을 실행하는 데 필요한 데이터를 추가로 조회하기 위해 SQL문 내부에서 사용하는 SELECT문을 의미한다.


- `메인쿼리(main query)` : 서브쿼리의 결과 값을 사용하여 기능을 수행하는 영역

#### (1-1) (기존) EMP 테이블에서 JONES보다 급여가 높은 사원 조회하기
- 단계1 : 사원 이름이 JONES인 사원의 급여 출력하기


```python
--- 9-1
SELECT SAL
    FROM EMP
    WHERE ENAME = 'JONES';
```

결과 :

![1-1-1](https://user-images.githubusercontent.com/53929665/94003668-84bc8f00-fdd6-11ea-8687-5f8030b2e7fa.JPG)



- 단계2 : 급여가 2975 보다 높은 사원 정보 출력하기


```python
--- 9-2
SELECT *
    FROM EMP
    WHERE SAL > 2975;
```

결과 :
![1-1-1(2)](https://user-images.githubusercontent.com/53929665/94003666-838b6200-fdd6-11ea-95a5-1bd71ff53067.JPG)


#### (1-2) 서브쿼리로 JONES보다 급여가 높은 사원 조회하기


```python
--- 9-3
SELECT *
    FROM EMP
    WHERE SAL > (SELECT SAL
                    FROM EMP
                    WHERE ENAME = 'JONES');
```

결과 : 
- (1-1)에서의 단계1이 서브쿼리가 되고,
- (1-1)에서의 단계2가 메인쿼리가 되었다.

![1-1-2](https://user-images.githubusercontent.com/53929665/94003669-84bc8f00-fdd6-11ea-83aa-68b46b9117e5.JPG)

#### (2) 서브쿼리의 특징
- 서브쿼리는 연산자와 같은 비교 또는 조회 대상의 오른쪽에 놓이며,  
괄호 ()를 묶어서 사용한다.


- 특수한 몇몇 경우를 제외한 대부분의 서브쿼리에서는 `ORDER BY`절을 사용할 수 없다.


- 서브쿼리의 SELECT절에 명시한 열은 메인쿼리의 비교 대상과 같은 자료형과 같은 개수로 지정해야한다.  
( 예제 (1-2)의 WHERE절에서 메인쿼리의 비교대상 SAL과 서브쿼리의 SELECT절에 명시한 열은 자료형과 개수가 같다. )


- 서브쿼리에 있는 SELECT문의 결과 행 수는 함께 사용하는 메인쿼리의 연산자 종류와 호환 가능해야한다.

---
### [ 2. 실행 결과가 하나인 단일행 서브쿼리 ]
- `단일행 서브쿼리(single-row subquery)` : 실행 결과가 ☑(단 하나의 행)으로 나오는 서브쿼리
    > 서브쿼리에서 출력되는 결과가 하나이면,  
    > 메인쿼리와 서브쿼리 결과는 다음과 같이 `단일행 연산자`를 사용하여 비교한다.  
    > : `>`, `>=`, `=`, `<=`, `<`, `<>`, `^=`, `!=`


- `다중행 서브쿼리(multiple-row subquery)` : 실행 결과가 ☑(여러 행)을 반환하는 서브쿼리

#### (1) 단일행 서브쿼리와 날짜형 데이터
- `단일행 서브쿼리`는 결과 값이 `날짜(DATE)자료형`일 때도 사용할 수 있다.

#### (1-1) 서브쿼리의 결과 값이 날짜형인 경우
- EMP 테이블에서 SCOTT보다 빨리 입사한 사원 목록을 조회할 경우


```python
--- 9-4
SELECT *
    FROM EMP
    WHERE HIREDATE < (SELECT HIREDATE
                        FROM EMP
                        WHERE ENAME = 'SCOTT');
```

결과 :

![2-1-1](https://user-images.githubusercontent.com/53929665/94005278-044b5d80-fdd9-11ea-9bbc-2b4514e3efd3.JPG)

#### (2) 단일형 서브쿼리의 함수
- 서브쿼리에서 특정 함수를 사용한 결과 값이 하나일 때  
역시 `단일행 서브쿼리`로서 사용가능하다.

#### (2-1) 서브쿼리 안에서 함수를 사용하는 경우
- 20번 부서에 속한 사원 중 전체 사원의 평균 급여보다 높은 급여를 받는 사원 정보와 소속 부서 정보를 함께 조회하는 경우


```python
--- 9-5
SELECT  E.EMPNO, E.ENAME, E.JOB, E.SAL,
        D.DEPTNO, D.DNAME, D.LOC
    FROM EMP E, DEPT D
    WHERE E.DEPTNO = D.DEPTNO
        AND E.DEPTNO = 20 
        AND E.SAL > (SELECT AVG(SAL)
                        FROM EMP);
```

결과 :

![2-2-1](https://user-images.githubusercontent.com/53929665/94005279-057c8a80-fdd9-11ea-8370-cddaa2402e26.JPG)

---
### [ 3. 실행 결과가 여러 개인 다중행 서브쿼리 ]
- 단일행 서브쿼리와 달리 서브쿼리 결과가 여러 개이므로 단일행 연산자는 사용할 수 없고 `다중행 연산자`를 사용해야 메인쿼리와 비교할 수 있다.


- `다중행 연산자`

![3](https://user-images.githubusercontent.com/53929665/94031906-581a6e80-fdfa-11ea-8849-4693c9028636.JPG)

|다중행 연산자|설명|
|:------------:|:--|
|`IN`|메인쿼리의 데이터가 서브쿼리의 결과 중 하나라도 일치한 데이터가 있다면  ☑TRUE|
|`ANY`, `SOME`|메인쿼리의 조건식을 만족하는 서브쿼리의 결과가 하나 이상이면 ☑TRUE|
|`ALL`|메인쿼리의 조건식을 서브쿼리의 결과 모두 만족하면 ☑TRUE|
|`EXISTS`|서브쿼리의 결과가 존재하면(즉, 행이 1개 이상일 경우) ☑TRUE|

#### (1) IN 연산자

#### (1-1) `IN` 연산자 사용하기


```python
--- 9-6
SELECT *
    FROM EMP
    WHERE DEPTNO IN(20,30);
```

결과 :

![3-1-1](https://user-images.githubusercontent.com/53929665/94029953-40da8180-fdf8-11ea-9117-3b13433ba0bf.JPG)

#### (1-2) 다중행 서브쿼리의 데이터를 비교하는 다중행 연산자 `IN`
- 각 부서별 최고 급여와 동일한 급여를 받는 사원 정보 출력하기


```python
--- 9-7
SELECT *
    FROM EMP
    WHERE SAL IN(SELECT MAX(SAL)
                    FROM EMP
                    GROUP BY DEPTNO);
```

결과 :
- `IN` 연산자를 사용해 메인쿼리에서는 세 값 중 일치하는 값을 가진 행만 출력


![3-1-2](https://user-images.githubusercontent.com/53929665/94029955-41731800-fdf8-11ea-86ea-caf3295baf84.JPG)

#### (2) ANY, SOME 연산자
- `ANY`, `SOME` 연산자는 서브쿼리가 반환한 여러 결과 값 중  
메인쿼리와 조건식을 사용한 결과가 하나라도 TRUE이면  
메인쿼리 조건식을 TRUE로 반환해 주는 연산자이다.


- 조건식이 참이 되어 출력 대상이 된다는 점에서 `IN`연산자가 생각날 수 있다.  
실제로, `ANY` 및 `SOME` 연산자를 `등가 비교 연산자(=)`와 함께 사용하면 `IN`연산자와 정확히 같은 기능을 수행한다.


#### (2-1) `ANY`연산자 사용하기 (with 등가 비교 연산자)


```python
--- 9-8
SELECT *
    FROM EMP
    WHERE SAL = ANY(SELECT MAX(SAL)
                        FROM EMP
                        GROUP BY DEPTNO);
```

결과 :
- `ANY`연산자에 등가 비교 연산자를 함께 사용하니 `IN`연산자와 같은 결과가 출력되었다.


![3-2-1](https://user-images.githubusercontent.com/53929665/94029958-420bae80-fdf8-11ea-8e91-403c2488547d.JPG)

#### (2-2) `SOME`연산자 사용하기 (with 등가 비교 연산자)


```python
--- 9-9
SELECT *
    FROM EMP
    WHERE SAL = SOME(SELECT MAX(SAL)
                        FROM EMP
                        GROUP BY DEPTNO);
```

결과 :
- `SOME` 연산자에 등가 비교 연산자를 함께 사용하니 `IN`연산자와 같은 결과가 출력되었다.


![3-2-2](https://user-images.githubusercontent.com/53929665/94029960-420bae80-fdf8-11ea-90db-6004983b08e2.JPG)

#### (2-3) `ANY`연산자 사용하기(with 대소 비교 연산자)
- 단계 1 : 부서 번호가 30인 사원들의 급여 출력하기


```python
--- 9-11
SELECT SAL
    FROM EMP
    WHERE DEPTNO = 30;
```

결과 :


![3-2-3(1)](https://user-images.githubusercontent.com/53929665/94029964-42a44500-fdf8-11ea-9eb5-3fa91bbb1b59.JPG)

- 단계 2-1 :30번 부서 사원들의 최대 급여보다 적은 급여를 받는 사원 정보 출력하기


```python
--- 9-10
SELECT *
    FROM EMP
    WHERE SAL < ANY(SELECT SAL
                        FROM EMP
                        WHERE DEPTNO = 30)
    ORDER BY SAL, DEPTNO;
```

결과 :
> 단계1의 서브쿼리에서 가장 큰 값인 2850보다 적은 급여를 가진 메인쿼리행은 모두 TRUE가 된다.
>
> 따라서, 950, 1250, 1500, 1600보다 큰 값이더라도 2850 하나의 값보다만 작으면 한 가지 경우에 TRUE가 나온다.
>
> 즉, `< ANY` 연산자는 서브쿼리 결과 값 중 급여의 최댓값(SAL=2850)보다 작은 값은 모두 출력 대상이 된다.


![3-2-3(2)](https://user-images.githubusercontent.com/53929665/94029966-433cdb80-fdf8-11ea-8330-d00453df7b76.JPG)

- 단계 2-2 : 30번 부서 사원들의 최소 급여보다 많은 급여를 받은 사원 정보 출력하기


```python
--- 9-13
SELECT *
    FROM EMP
    WHERE SAL > ANY (SELECT SAL
                        FROM EMP
                        WHERE DEPTNO = 30);
```

결과 :
> 단계1의 서브쿼리에서 가장 작은 값인 950보다 큰 급여를 가지면 조건식이 TRUE가 되어 결과 값이 출력된다.


![3-2-3(3)](https://user-images.githubusercontent.com/53929665/94029967-433cdb80-fdf8-11ea-811f-25be1bf3ec85.JPG)

#### (3) ALL 연산자
- `ANY` 및 `SOME`과 달리 `ALL`연산자는  
서브쿼리의 ☑(모든 결과가 조건식에 맞아 떨어져야만)  
메인쿼리의 조건식이 TRUE가 되는 연산자이다.

#### (3-1) `ALL` 연산자 사용하기
- 단계 2-1 :부서 번호가 30번인 사원들의 최소 급여보다 더 적은 급여를 받는 사원 출력하기


```python
--- 9-14
SELECT *
    FROM EMP
    WHERE SAL < ALL (SELECT SAL
                        FROM EMP
                        WHERE DEPTNO = 30);
```

결과 :
> `ALL` 연산자가 의미하는 것과 같이 
> 서브쿼리의 모든 결과 값(950, 1250, 1500, 1600, 2850)보다 작은 값을 가진
> 메인쿼리의 행만 TRUE가 되어 출력된다.
>
> 즉, 메인쿼리 값 중 950(서브쿼리 결과 값 중 가장 작은 값)보다 작은 값만 출력된다.


![3-3-1(1)](https://user-images.githubusercontent.com/53929665/94029971-43d57200-fdf8-11ea-8b35-22cee8296940.JPG)

- 단계 2-2 :부서 번호가 30번인 사원들의 최대 급여보다 더 많은 급여를 받는 사원 출력하기


```python
--- 9-15
SELECT *
    FROM EMP
    WHERE SAL > ALL (SELECT SAL
                        FROM EMP
                        WHERE DEPTNO = 30);
```

결과 :
> 서브쿼리 결과 값(950, 1250, 1500, 1600, 2850)중  
> 가장 큰 값인 2850과 비교하여 큰 값을 가진 데이터만 출력된다.


![3-3-1(2)](https://user-images.githubusercontent.com/53929665/94029974-43d57200-fdf8-11ea-850b-8f1a35bba4d2.JPG)

#### (4) EXISTS 연산자
- 서브쿼리에 결과 값이  
하나 이상 존재하면 조건식이 모두 TRUE  
존재하지 않으면 모두 FALSE가 되는 연산자


- `EXIST`연산자는 다른 다중행 연산자에 비해 그리 자주 사용되는 편은 아니지만,  
특정 서브쿼리 결과 값의 존재 유무를 통해  
메인쿼리의 데이터 노출 여부를 결정해야 할 떄 간혹 사용한다.

#### (4-1) 서브쿼리 결과 값이 존재하는 경우(TRUE)


```python
--- 9-16
SELECT *
    FROM EMP
    WHERE EXISTS(SELECT DNAME
                    FROM DEPT
                    WHERE DEPTNO = 10);
```

결과 :
> 서브쿼리를 살펴보면 결과 값이 존재하기 때문에 EMP의 모든 행이 출력되었다.


![3-4-1](https://user-images.githubusercontent.com/53929665/94029975-446e0880-fdf8-11ea-9a6a-b79536ac0aa4.JPG)

#### (4-2) 서브쿼리 결과 값이 존재하지 않는 경우(FALSE)


```python
--- 9-17
SELECT *
    FROM EMP
    WHERE EXISTS(SELECT DNAME
                    FROM DEPT
                    WHERE DEPTNO = 50);
```

결과 :
> DEPT 테이블에 존재하지 않는 조건(DEPTNO=50)의 서브쿼리를 실행하면
> 결과 데이터로 아무 행도 출력되지 않는다



![3-4-2](https://user-images.githubusercontent.com/53929665/94029978-446e0880-fdf8-11ea-81bf-62fd452a9b8c.JPG)

---
### [ 4. 비교할 열이 여러 개인 다중열 서브쿼리 ]

- `다중열 서브쿼리(multiple-column subquery)` :  
서브쿼리의 SELECT절에 비교할 데이터를 여러 개 지정하는 방식


- 빈번하게 사용된다.


#### (1) 다중열 서브쿼리 사용하기
- 다음과 같이 메인쿼리에 비교할 열을 괄호로 묶어 명시하고  
서브쿼리에서는 괄호로 묶은 데이터와 같은 자료형 데이터를 SELECT절에 명시하여  
사용한다.


```python
--- 9-18
SELECT *
    FROM EMP
    WHERE (DEPTNO, SAL) IN (SELECT DEPTNO, MAX(SAL)
                                FROM EMP
                                GROUP BY DEPTNO);
```

결과 :

![4-1](https://user-images.githubusercontent.com/53929665/94033888-5e114f00-fdfc-11ea-924e-9d62ebf11c3c.JPG)


---
### [ 5. FROM절에 사용하는 서브쿼리와 WITH절 ]

- 지금 까지의 서브쿼리는 `WHERE`절에서 조건식 대상으로 사용했다.  
 하지만, ☑(`FROM`절에서도 서브쿼리를 사용할 수 있다.)
 
 
- `FROM`절에서 사용하는 서브쿼리는 `인라인 뷰(inline view)`라고도 부른다.
    - 인라인 뷰는 특정 테이블 전체 데이터가 아닌 SELECT문을 통해  
    일부 데이터를 먼저 추출해 온 후 ☑(별칭을 주어 사용할 수 있다.)

#### (1) `인라인 뷰` 사용하기


```python
--- 9-19
SELECT  E10.EMPNO, E10.ENAME, E10.DEPTNO, D.DNAME, D.LOC
    FROM (SELECT * FROM EMP WHERE DEPTNO = 10) E10,
         (SELECT * FROM DEPT) D
    WHERE E10.DEPTNO = D.DEPTNO;
```

결과 :

![5-1](https://user-images.githubusercontent.com/53929665/94035402-fbb94e00-fdfd-11ea-9de6-b06dd7a057c3.JPG)


- 하지만, `FROM`절에 너무 많은 서브쿼리를 지정하면  
가독성이나 성능이 떨어질 수 있기 떄문에 경우에 따라서 `WITH`절을 사용한다.


- 기본형식
```
WITH
[별칭 1] AS (SELECT문 1),
[별칭 2] AS (SELECT문 2),
...
[별칭 n] AS (SELECT문 n)
SELECT
    FROM 별칭1, 별칭2, 별칭3
...
```

#### (2) `WITH`절 사용하기


```python
--- 9-20
WITH
E10 AS (SELECT * FROM EMP WHERE DEPTNO = 10),
D   AS (SELECT * FROM DEPT)
SELECT  E10.EMPNO, E10.ENAME, E10.DEPTNO, D.DNAME, D.LOC
    FROM E10, D
    WHERE E10.DEPTNO = D.DEPTNO;  
```

결과 : (1)과 동일

---
### [ 6. SELECT 절에 사용하는 서브쿼리 ]

- 서브쿼리는 `SELECT`절에도 사용할 수 있다.  
흔히 `스칼라 서브쿼리(scalar subquery)`라고 부른다.


- ☑(`SELECT`절에 하나의 열 영역으로서 결과를 출력할 수 있다.)


- ☑(주의) `SELECT`절에 명시하는 서브쿼리는  
반드시 하나의 결과만 반환 가능하도록 작성해야한다.

#### (1) `SELECT`절에 서브쿼리 사용하기


```python
--- 9-21
SELECT  EMPNO, ENAME, JOB, SAL,
        (SELECT GRADE
            FROM SALGRADE
            WHERE E.SAL BETWEEN LOSAL AND HISAL) AS SALGRADE,
        DEPTNO,
        (SELECT DNAME
            FROM DEPT D
            WHERE E.DEPTNO = D.DEPTNO) AS DNAME
    FROM EMP E;
```

결과 :

![6-1](https://user-images.githubusercontent.com/53929665/94036072-dd078700-fdfe-11ea-9e17-4e0d27870554.JPG)


### References

- 이지훈, 『Do it! 오라클로 배우는 데이터베이스』, 이지스퍼블리싱 (2018)

