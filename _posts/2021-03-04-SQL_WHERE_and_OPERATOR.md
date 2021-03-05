---
layout: post
title:  "[ORACLE] 3.WHERE and OPERATOR"
subtitle: "[ORACLE] 3.WHERE and OPERATOR"
categories: devlang
tags: (dbms)oracle
mathjax: true
comments: true
---
- WHERE 절
- 여러 개 조건식을 사용하는 AND, OR연산자
- 연산자 종류와 활용 방법
- 연산자 우선순위

---

### [ 1. WHERE 절 ]
- `WHERE` : `SELECT`문으로 데이터를 조회할 때 특정 조건을 기준으로 원하는 행을 출력하는데 사용
- `WHERE` 절이 포함된 `SELECT`문을 실행하면 조회할 테이블의 각 행에 WHERE절의 조건식을 대입하여 `TRUE`인 경우에만 출력된다. (거짓인 경우 `FALSE`)


- 기본형식
```
SELECT [조회할 열1 이름], [열2 이름], ..., [열N 이름]
FROM   [조회할 테이블 이름]
WHERE  [조회할 행을 선별하기 위한 조건식];
```

#### (1) WHERE절을 이용하여 DEPTNO가 30인 데이터만 출력하기


```python
SELECT * 
    FROM EMP
    WHERE DEPTNO = 30;
```

결과 : 
- DEPTNO 열 값이 모두 30임을 확인 할 수 있다.
![1-1](https://user-images.githubusercontent.com/53929665/92996980-bcac1280-f54a-11ea-9122-c29d6e466d90.JPG)

---
### [ 2. 여러 개 조건식을 사용하는 AND, OR연산자 ] 
- `WHERE`절에 사용할 수 있는 조건식의 개수는 사실상 제한이 없다

#### (1) AND 연산자로 여러 개의 조건식 사용하기
- 조건식 모두가 TRUE인 데이터의 행만 출력한다.


```python
SELECT * 
    FROM EMP
    WHERE DEPTNO = 30
    AND JOB = 'SALESMAN';
```

결과 :
![2-1](https://user-images.githubusercontent.com/53929665/92996981-bd44a900-f54a-11ea-8c15-d40d53a62f7d.JPG)

#### (2) OR 연산자로 여러 개의 출력 조건 사용하기


```python
SELECT * 
    FROM EMP
    WHERE DEPTNO = 30
    OR JOB = 'CLERK';
```

결과 :
![2-2](https://user-images.githubusercontent.com/53929665/92996982-bddd3f80-f54a-11ea-8ecd-4b0f8f5b26ee.JPG)

---
### [ 3. 연산자 종류와 활용 방법 ]

#### (1) 기본 연산자
|연산자|기호|
|:------|:----:|
|산술 연산자| `+`, `-`, `*`, `/`|
|비교 연산자| `>`, `>=`, `<`, `<=`|
|등가 비교 연산자|`=`, `!=`( 동일 연산자 `<>`, `^=`)|

#### (2) IN 연산자
- JOB이 SALESMAN이거나 MANAGER 또는 CLERK중 하나인 데이터를 조회하려면 다음과 같이 논리 연산자 `OR`을 사용해서 출력할 수 있다.


```python
SELECT * 
    FROM EMP
    WHERE JOB = 'SALESMAN'
        OR JOB = 'MANAGER'
        OR JOB = 'CLERK';
```

- 위와 같이 열의 조건이 여러 가지일 때 `OR`연산자로 여러 조건식을 묶어주는 것도 하나의 방법이지만, 조건이 늘어날수록 조건식을 많이 작성해야 하기 때문에 번거롭다.


- <U>따라서, `IN`연산자를 사용하면 특정 열에 해당하는 조건을 여러 개 지정할 수 있다.</U>


- 기본형식
```
SELECT [조회할 열1 이름], [열2 이름], ..., [열N 이름]
FROM   [조회할 테이블 이름]
WHERE  열 이름 IN (데이터1, 데이터2, ..., 데이터N);
```

- 따라서, 위의 작성법을 `IN`연산자로 표현하면 다음과 같다.


```python
SELECT * 
    FROM EMP
    WHERE JOB IN ('SALESMAN', 'MANAGER', 'CLERK');
```

결과 :
![3-2](https://user-images.githubusercontent.com/53929665/92996983-bddd3f80-f54a-11ea-944f-5e52892f0867.JPG)

#### (3) NOT IN 연산자
- JOB이 SALESMAN도 아니고 MANAGER, CLERK도 아닌 데이터를 찾을 때, 앞에서 배운 등가 비교 연산자와 AND 연산자를 사용하여 <U>세 값 모두가 '아닌' 데이터를 찾을 수 있다.</U>


```python
SELECT * 
    FROM EMP
    WHERE JOB != 'SALESMAN'
    AND JOB <> 'MANAGER'
    AND JOB ^= 'CLERK';
```

- 위와 같이 나타낼 수 있지만, `NOT IN`연산자를 이용하면 간단하게 표현할 수 있다.
- `IN` 연산자 앞에 `NOT`을 적용 : ( OR, = -> AND, != )


- 따라서, 위의 작성법을 `NOT IN`연산자로 표현하면 다음과 같다.


```python
SELECT *
    FROM EMP
    WHERE JOB NOT IN ('SALESMAN', 'MANAGER', 'CLERK'); 
```

결과 :
![3-3](https://user-images.githubusercontent.com/53929665/92996984-be75d600-f54a-11ea-863e-acb71401bf7b.JPG)

#### (4) BETWEEN A AND B 연산자
- 급여 열 값이 2000 이상 3000 이하인 사원의 데이터를 조회해야할 때, 대소비교연산자와 AND연산자를 사용하면 다음과 같이 SELECT문을 만들 수 있다.


```python
SELECT *
    FROM EMP
    WHERE SAL >= 2000
      AND SAL <= 3000;
```

- 하지만, 특정 열 값의 최소/최고 범위를 지정하여 해당 범위 내의 데이터만 조회할 경우, 대소 비교연산자 대신 `BETWEEN A AND B`연산자를 사용하면 더 간단하게 표현할 수 있다.

- 기본형식
```
SELECT [조회할 열1 이름], [열2 이름], ..., [열N 이름]
FROM   [조회할 테이블 이름]
WHERE  열 이름 BETWEEN 최솟값 AND 최댓값;
```


- 따라서, 위의 작성법을 `BETWEEN A AND B`연산자로 표현하면 다음과 같다.


```python
SELECT *
    FROM EMP
    WHERE SAL BETWEEN 2000 AND 3000;
```

결과 :
![3-4](https://user-images.githubusercontent.com/53929665/92996986-bf0e6c80-f54a-11ea-866c-f966ebc595f4.JPG)

- 또한, `NOT IN`과 마찬가지로, `BETWEEN A AND B`연산자 앞에 `NOT`연산자를 쓸 수 있다.  
    ex) NO BETWEEN 2000 AND 3000 : 2000~3000 사이 외의 값


```python
SELECT *
    FROM EMP
    WHERE SAL NOT BETWEEN 2000 AND 3000;
```

결과 :
![3-4(2)](https://user-images.githubusercontent.com/53929665/92996985-be75d600-f54a-11ea-9752-22ae60fff8e9.JPG)

#### (5) LIKE연산자와 와일드 카드
- `LIKE`연산자 : 이메일이나 게시판 제목 또는 내용 검색 기능처럼 일부 문자열이 포함된 데이터를 조회할 때 사용한다.

- `와일드 카드` : 특정 문자 또는 문자열을 대체하거나 문자열 데이터의 패턴을 표기하는 특수 문자.

|종류|의미|
|:--:|:---|
|`_`|어떤 값이든 상관없이 <U>한 개</U>의 문자 데이터를 의미|
|`%`|길이와 상관없이 <U>모든</U> 문자 데이터를 의미|

-  ex) 사원이름이 S로 시작하는 사원 데이터를 조회하고 싶은 경우 


```python
SELECT *
    FROM EMP
    WHERE ENAME LIKE 'S%';  # LIKE S%는 대문자 S로 시작하는 데이터를 조회 
```

결과 :
![3-5(1)](https://user-images.githubusercontent.com/53929665/92996987-bfa70300-f54a-11ea-925b-e3c2e867da0c.JPG)

- ex) 사원이름의 두 번째 글자가 L인 사원 데이터를 조회하고 싶을 경우 


```python
SELECT *
    FROM EMP
    WHERE ENAME LIKE '_L%';
```

결과 :
![3-5(2)](https://user-images.githubusercontent.com/53929665/92996988-bfa70300-f54a-11ea-972c-8d115ff12ebb.JPG)

- ex) 사원 이름에 AM이 포함되어 있는 사원 데이터만 출력하기


```python
SELECT *
    FROM EMP
    WHERE ENAME LIKE '%AM%';
```

결과 : 
![3-5(3)](https://user-images.githubusercontent.com/53929665/92996989-c03f9980-f54a-11ea-92e7-63889dacb29f.JPG)

- ex) 사원 이름에 AM이 포함되어 있지 않은 사원 데이터 출력하기


```python
SELECT *
    FROM EMP
    WHERE ENAME NOT LIKE '%AM%'; # NOT 연산자를 앞에 작성
```

결과 :
![3-5(4)](https://user-images.githubusercontent.com/53929665/92996990-c03f9980-f54a-11ea-81d4-93a8e783a1fd.JPG)

#### (6) 와일드카드 문자가 데이터 일부일 경우
- 데이터에 와일드 카드 기호로 사용되는 `_`/`%` 문자가 데이터로 포함된 경우가 있다.
- 이 경우 데이터를 조회하기 위해 와일드 카드 문자를 쓰는 것은 애매하다.


- 하지만, `ESCAPE` 절을 사용하면 와일드 카드 기호가 아닌 데이터로서의 문자로 다루는 것이 가능하다.


- 예를들어, `LIKE`문을 사용하여 데이터 앞에 'A_A' 문자를 가지고있는 데이터를 찾으려면 다음과 같이 작성한다. 


```python
SELECT *
    FROM SOME_TABLE
    WHERE SOME_COLUMN LIKE 'A\_A%' ESCAPE '\';
```

- `A\_A%`에서 `\`는 바로 뒤에 있는 `_`는 와일드 카드 기호로서가 아닌 데이터에 포함된 문자로 인식하라는 의미이다.


- `ESCAPE`문자 `\`는 ESCAPE 절에서 지정할 수 있다.
    - 그리고, `\`외 다른 문자도 지정하여 사용할 수 있다.

#### (7) IS NULL 연산자
- `NULL` : 현재 무슨 값인지 확정되지 않은 상태, 값 자체가 존재하지 않는 상태


- `NULL`은 산술 연산자와 비교 연산자로 비교해도 결과 값이 NULL이기 때문에, 열의 값이 NULL인 데이터를 찾기는 쉽지 않다.
    - WHERE절은 조건식의 결과 값이 TRUE인 행만 출력하는데 이처럼 연산 결과 값이 NULL이 되어 버리면 조건식의 결과 값이 FALSE도 TRUE도 아니므로 출력대상에서 제외된다.
    - 따라서, 지금까지 살펴본 연산자로는 특정 열의 데이터가 NULL인 경우를 구별할 수 없다.


- 따라서, 특정 열 또는 연산의 결과 값이 NULL인지 여부를 확인하려면 `IS NULL`연산자를 사용해야한다.


```python
SELECT *
    FROM EMP
    WHERE COMM IS NULL;
```

결과 : 
![3-7(1)](https://user-images.githubusercontent.com/53929665/92996992-c0d83000-f54a-11ea-8d33-06eef17d004c.JPG)

- 또한, `NOT`연산자를 이용하여 <U>NULL이 아닌 데이터</U>만을 조회하려면 `IS NOT NULL`을 사용하면 된다.


```python
SELECT *
    FROM EMP
    WHERE COMM IS NOT NULL;
```

 결과 :
 ![3-7(2)](https://user-images.githubusercontent.com/53929665/92996993-c170c680-f54a-11ea-816e-9e3ba3df09c9.JPG)

- <B>`IS NULL`연산자 와 `AND`/`OR` 연산자와의 관계</B>
   
   
    - AND 연산자
    
| -  |TRUE|FALSE|NULL|
|:--:|:--:|:--:|:--:|
|<B>NULL</B>|NULL|<U>FALSE</U>|NULL|
    
    - OR 연산자
    
| -  |TRUE|FALSE|NULL|
|:--:|:--:|:--:|:--:|
|<B>NULL</B>|TRUE|<U>NULL</U>|NULL|



#### (8-1) 집합 연산자 UNION (1)
- `UNION` : 두 개 이상의 SELECT문의 결과 값을 연결할 때 사용
- (주의!!!) 집합 연산자로 두 개의 SELECT문의 결과 값을 연결할 때 각 SELECT문이 출력하려는 <U>열의 개수와 각 열의 자료형이 순서별로 일치해야 한다는 것이다.</U>

ex) 집합 연산자 UNION을 사용하여 출력


```python
SELECT EMPNO, ENAME, SAL, DEPTNO
    FROM EMP
    WHERE DEPTNO = 10
    UNION    
SELECT EMPNO, ENAME, SAL, DEPTNO
    FROM EMP
    WHERE DEPTNO = 20
    ORDER BY DEPTNO ASC;
```

결과 : 
![3-8(1)](https://user-images.githubusercontent.com/53929665/92996994-c170c680-f54a-11ea-91a1-e9daf7fac252.JPG)

ex) UNION이 안될때 예시


```python
# 출력 열 개수가 다를 경우
SELECT EMPNO, ENAME, SAL, DEPTNO
    FROM EMP
    WHERE DEPTNO = 10
    UNION    
SELECT EMPNO, ENAME, SAL
    FROM EMP
    WHERE DEPTNO = 20;
```


```python
# 출력 열의 자료형이 다를 경우
SELECT EMPNO, ENAME, SAL, DEPTNO
    FROM EMP
    WHERE DEPTNO = 10
    UNION    
SELECT ENAME, SAL, DEPTNO, EMPNO
    FROM EMP
    WHERE DEPTNO = 20;
```

ex) 출력 열 개수와 자료형이 같을 때


```python
# 열 출력 순서가 달라도, 개수와 열의 자료형이 같다면 출력이 되긴한다.
SELECT EMPNO, ENAME, SAL, DEPTNO
    FROM EMP
    WHERE DEPTNO = 10
    UNION    
SELECT SAL, JOB, DEPTNO, SAL
    FROM EMP
    WHERE DEPTNO = 20;
```

결과 :

- 결과를 보면 완전히 뒤죽박죽이라 보기에도 불편하고 뭐가뭔지 구분이안간다.
- 어지간하면, SELECT에서 열의순서를 맞추고, 같은 자료형에서만 쓰도록하자.
![3-8(2)](https://user-images.githubusercontent.com/53929665/92996995-c2095d00-f54a-11ea-8e34-f13142658fde.JPG)

#### (8-2) 집합 연산자 UNION
- 집합 연산자의 종류

|종류|설명|
|:---:|:---|
|`UNION`|연결된 SELECT문의 결과 값을 합집합으로 묶어준다. <U>결과 값의 중복은 제거</U>|
|`UNION ALL`|연결된 SELECT문의 결과 값을 합집합으로 묶어준다. <U>중복된 결과 값도 제거 없이 모두 출력된다.</U>|
|`MINUS`|먼저 작성 SELECT문의 결과 값에서 다음 SELECT문의 결과 값을 차집합 처리한다. <U>먼저 작성한 SELECT문의 결과 값 중 다음 SELECT문에 존재하지 않는 데이터만 출력된다.</U>|
|`INTERSECT`|먼저 작성한 SELECT문과 다음 SELECT문의 결과 값이 같은 데이터만 출력된다.<U>교집합과 같은 의미</U>|

- `UNION` (출력 결과 데이터가 같을 때)


```python
SELECT EMPNO, ENAME, SAL, DEPTNO
    FROM EMP
    WHERE DEPTNO = 10
    UNION    
SELECT EMPNO, ENAME, SAL, DEPTNO
    FROM EMP
    WHERE DEPTNO = 10;
```

결과 :
![3-8-1](https://user-images.githubusercontent.com/53929665/92996996-c2095d00-f54a-11ea-83ff-ea6e01771b82.JPG)

- `UNION ALL` (출력 결과 데이터가 같을 때)


```python
SELECT EMPNO, ENAME, SAL, DEPTNO
    FROM EMP
    WHERE DEPTNO = 10
    UNION ALL  
SELECT EMPNO, ENAME, SAL, DEPTNO
    FROM EMP
    WHERE DEPTNO = 10;
```

결과 :
![3-8-2](https://user-images.githubusercontent.com/53929665/92996997-c2a1f380-f54a-11ea-902c-536f4ad32ffd.JPG)

- `MINUS`


```python
SELECT EMPNO, ENAME, SAL, DEPTNO
    FROM EMP
    MINUS  
SELECT EMPNO, ENAME, SAL, DEPTNO
    FROM EMP
    WHERE DEPTNO = 10;
```

결과 : DEPTNO가 10인 데이터만 제외된 것을 확인할 수 있다.
![3-8-3](https://user-images.githubusercontent.com/53929665/92996998-c2a1f380-f54a-11ea-8566-f8fc116b13e6.JPG)

- `INTERSECT`


```python
SELECT EMPNO, ENAME, SAL, DEPTNO
    FROM EMP
    INTERSECT  
SELECT EMPNO, ENAME, SAL, DEPTNO
    FROM EMP
    WHERE DEPTNO = 10;
```

결과 : 두 SELECT문의 교집합인 DEPTNO가 10일때 데이터만 조회되는 것을 확인할 수 있다.
![3-8-4](https://user-images.githubusercontent.com/53929665/92996999-c33a8a00-f54a-11ea-83b7-8323e9e91079.JPG)

### [ 4. 연산자 우선순위 ]

![4](https://user-images.githubusercontent.com/53929665/92997000-c33a8a00-f54a-11ea-892a-332d15742976.png)


- 연산식을 소괄호()로 묶어 주면 연산자의 기본 우선순위와는 별개로 괄호 안의 연산식을 먼저 수행할 수 있다.



<br>

---

### References

- 이지훈, 『Do it! 오라클로 배우는 데이터베이스』, 이지스퍼블리싱 (2018)
