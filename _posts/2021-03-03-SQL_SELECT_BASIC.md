---
layout: post
title:  "[ORACLE] 2.SELECT Basic"
subtitle: "[ORACLE] 2.SELECT Basic"
categories: devlang
tags: (dbms)oracle
mathjax: true
comments: true
---
-  데이터를 조회하는 3가지 방법
- SELECT 절과 FROM 절
- 중복 데이터를 제거하는 DISTINCT

---

### [ 1. 데이터를 조회하는 3가지 방법 ]

#### (1) 행 단위로 조회하는 SELECTION
- `SELECTION`은 `행 단위`로 원하는 데이터를 조회하는 방식이다.
- 테이블 전체 데이터 중 몇몇 가로줄의 데이터만 선택할 때 사용한다.

#### (2) 열 단위로 조회하는 PROJECTION
- `PROJECTION`은 `열 단위`로 원하는 데이터를 조회하는 방식이다.

#### (3) 두 개 이상의 테이블을 사용하여 조회하는 JOIN
- `JOIN`은 두 개 이상의 테이블을 양옆에 연결하여 마치 `하나의 테이블인 것`처럼 데이터를 조회하는 방식이다.

---
### [ 2. SELECT 절과 FROM 절 ]
- SELECT문 = SELECT 절 + FROM 절
- `FROM`절 : 조회할 데이터가 저장된 테이블 이름을 명시
- `SELECT`절 : FROM절에 명시한 테이블에서 조회할 열이나 여러 열에 저장된 데이터의 조합 또는 연산식을 지정할 수 있다.

- 기본형식
```
SELECT [조회할 열1 이름], [열2 이름], ..., [열N 이름]
FROM   [조회할 테이블 이름];
```


#### (1)  `*` 로 테이블 전체 열 출력하기


```python
SELECT * FROM EMP;
```

결과 : 
![3](https://user-images.githubusercontent.com/53929665/92938332-5f9b5880-f487-11ea-834f-0a933c9bd524.JPG)

#### (2) 테이블 부분 열 출력하기


```python
SELECT EMPNO, ENAME, DEPTNO FROM EMP;
```

결과 :
![4](https://user-images.githubusercontent.com/53929665/92938333-60cc8580-f487-11ea-95bd-a9f88841bfc4.JPG)

---
### [ 3. 중복 데이터를 제거하는 DISTINCT ] 
- `SELECT`문으로 데이터를 조회한 후 `DISTINCT`를 사용하여 중복을 제거한다.

#### (1) DISTINCT로 열 중복 제거하기 (열이 한 개인 경우)


```python
SELECT DISTINCT DEPTNO FROM EMP;
```

결과 : 
![3-1](https://user-images.githubusercontent.com/53929665/92939291-8e65fe80-f488-11ea-8783-354f6c502154.JPG)

#### (2) DISTINCT로 열 중복 제거하기 (열이 여러 개인 경우)
- 두 열의 값이 동시에 동일한 경우의 중복을 제거한다.


```python
SELECT DISTINCT JOB,DEPTNO FROM EMP;
```

결과 : 
![3-2](https://user-images.githubusercontent.com/53929665/92939296-902fc200-f488-11ea-946a-1df40e510264.JPG)


#### (3) ALL로 중복되는 열 제거 없이 그대로 출력하기
- `ALL`은 `DISTINCT`와 반대로 데이터 중복을 제거하지 않고 그대로 출력한다.


```python
SELECT ALL JOB,DEPTNO FROM EMP;
```

---
### [ 4. 별칭 설정 ]
- SQL문에서는 최종 출력되는 열 이름을 임의로 지정할 수 있다.
- 이렇게 본래 열 이름 대신 붙이는 이름을 `별칭(alias)`라고 한다.

#### (1) 열과 연산식
- SAL*12+COMM또한 ENAME,SAL,COMM과 같이 명시한 열이 위쪽이름에 그대로 출력된다.
- (참조) SAL*12+COMM열에 모든 값이 출력되지 않는 이유는 COMM열의 값이 NULL인 사원들이 있기 때문이다.


```python
SELECT ENAME, SAL, SAL*12+COMM,COMM FROM EMP;
```

결과 :
![4-1](https://user-images.githubusercontent.com/53929665/92938334-60cc8580-f487-11ea-92b5-2d78a06f7eca.JPG)

#### (2) 별칭 지정
- 오라클에서 별칭을 지정하려면 다음과 같이 4가지 방식중 하나를 선택하여 SELECT절에 사용해야한다.
- (참조) 일반적으로는 3번째 방법이 선호되지만,  
프로그래밍언어에서 문자열 대부분은 `" "`을사용하기 때문에, 문자열에서 SQL문을 사용할 경우 마지막 방법이 선호된다. 

|사용방법|설명|
|:-------|:----|
|SAL*12+COMM ANNSAL|연산 및 가공된 문장 이후 한 칸 띄우고 별칭 지정|
|SAL*12+COMM "ANNSAL"|연산 및 가공된 문장 이후 한 칸 띄우고 별칭을 큰따옴표(")로 묶어 지정|
|(선호)SAL*12+COMM  AS ANNSAL|연산 및 가공된 문장 이후 한 칸 띄운 후 `AS`, 한 칸 뒤에 별칭 지정|
|SAL*12+COMM AS "ANNSAL"|연산 및 가공된 문장 이후 한 칸 띄운 후 `AS`, 한 칸 뒤에 별칭을 큰따옴표(")로 묶어 지정|


```python
SELECT ENAME, SAL, SAL*12+COMM AS ANNSAL,COMM FROM EMP;
```

결과 :
![4-2](https://user-images.githubusercontent.com/53929665/92938336-61651c00-f487-11ea-94aa-16ea43c546ff.JPG)

---
### [ 5. 원하는 순서로 출력 데이터를 정렬하는 ORDER BY ]
- `ORDER BY` : 데이터를 정렬된 상태로 출력하기 위해 사용
- SELECT문을 작성할 때 사용할 수 있는 여러 절 중 가장 마지막 부분에 쓰인다.
- 정렬 옵션
    - `ASC` : 오름차순  (default value)
    - `DESC`: 내림차순
    
    
- (주의) 가급적으로 정렬이 꼭 필요한 순간이 아니면 ORDER BY절을 넣지 않도록한다.  
이유 : 데이터의 크기에 비례하여 시간이 오래 걸리기 때문에


- 기본형식
```
SELECT [조회할 열1 이름], [열2 이름], ..., [열N 이름]
FROM   [조회할 테이블 이름]
.
.  (그 밖의 절)
.
ORDER BY [정렬하려는 열 이름(여러 열 지정 가능)] [정렬 옵션];
```


#### (1) 오름차순(ASC) 사용


```python
SELECT * FROM EMP ORDER BY SAL;
```

결과 :
![5-1(찐)](https://user-images.githubusercontent.com/53929665/92938339-61fdb280-f487-11ea-9a23-0b261bc6a5c7.JPG)


#### (2) 내림차순(DESC) 사용


```python
SELECT * FROM EMP ORDER BY SAL DESC;
```

결과 : 
![5-2(찐)](https://user-images.githubusercontent.com/53929665/92938342-61fdb280-f487-11ea-97f8-062650acda88.JPG)

#### (3) 각각의 열에 내림차순(DESC)와 오름차순(ASC) 동시에 사용하기


```python
SELECT * FROM EMP ORDER BY DEPTNO ASC, SAL DESC;
```

- ORDER BY절에 첫 번째로 명시된 DEPTNO 열을 기준으로 먼저 오름차순으로 정렬한 후에  
DEPNOT(부서번호)가 같은, 즉 같은 부서에서 근무하고 있는 사원들끼리는 급여가 높은 사원부터 낮은 사원으로 내림차순으로 정렬하여 출력되었다.

결과 : 
![5-3(찐)](https://user-images.githubusercontent.com/53929665/92938345-62964900-f487-11ea-9fa7-24f6bb54f9c2.JPG)


<br>

---

### References

- 이지훈, 『Do it! 오라클로 배우는 데이터베이스』, 이지스퍼블리싱 (2018)

