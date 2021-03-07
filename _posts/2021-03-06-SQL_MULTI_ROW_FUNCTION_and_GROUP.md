---
layout: post
title:  "[ORACLE] 5.Multi Row Function and GROUP"
subtitle: "[ORACLE] 5.Multi Row Function and GROUP"
categories: devlang
tags: (dbms)oracle
mathjax: true
comments: true
---
- 다중행 함수
- GROUP BY 절
- HAVING 절
- 그룹화와 관련된 여러 함수

---
### [ 1. 다중행 함수 (Multi-Row-Function) ]
- <U>여러 행을 바탕으로 하나의 결과 값을 도출해내기 위해 사용하는 함수</U>
- 다중행 함수의 종류 : `SUM`, `COUNT`, `MAX`, `MIN`, `AVG`, ...


- ex) `SUM`함수를 사용한 예시


```python
SELECT SUM(SAL)
FROM EMP;
```

결과 : 
- `SUM`함수는 SELECT문으로 조회된 행에 지정한 열 값을 모두 더해준다.
- SAL 열 값을 모두 합한 결과가 다음과 같이 하나의 행으로 출력된다.
![1](https://user-images.githubusercontent.com/53929665/93601176-c080de80-f9fb-11ea-896c-d16e7a769c5a.JPG)

- 또한, <U>다중행 함수를 사용한 SELECT절에는 기본적으로 여러 행이 결과로 나올 수 있는 열을 함께 사용할 수 없다.</U>


- ex) 여러 행이 결과로 나올 수 있는 열과 같이 못 사용하는 다중행 함수


```python
SELECT ENAME, SUM(SAL)
FROM EMP;
```

결과 :
    ![1(2)](https://user-images.githubusercontent.com/53929665/93601172-bf4fb180-f9fb-11ea-97d6-2789d25b3023.JPG)


#### (1) SUM 함수
- 기본형태
```
SUM([DISTINCT, ALL 중 하나를 선택하거나 아무 값도 지정하지 않음(선택)]  
      [합계를 구할 열이나 연산자, 함수를 사용한 데이터(필수)]
```
- 분석을 위한 용도의 `SUM`함수 문법
```
SUM([DISTINCT, ALL 중 하나를 선택하거나 아무 값도 지정하지 않음(선택)]  
      [합계를 구할 열이나 연산자, 함수를 사용한 데이터(필수)]
OVER(분석을 위한 여러문법을 지정)(선택)
```

#### (1-1) 합계 구하기


```python
SELECT SUM(COMM)
FROM EMP;
```

결과 :

![1-1-1](https://user-images.githubusercontent.com/53929665/93601177-c080de80-f9fb-11ea-869c-3d0ec617e315.JPG)

#### (1-2) `SUM`함수와 `DISTINCT`, `ALL` 함께 사용하기


```python
SELECT  SUM(DISTINCT SAL),
        SUM(ALL SAL),
        SUM(SAL)
    FROM EMP;
```

결과 :
- `ALL`을 사용한 결과와 아무 옵션을 지정하지 않은 `SUM`함수의 결과는 같다.
- `DISTINCT`를 지정한 SUM함수와 `SUM`함수의 결과는 다르다.
    - `SUM`함수에 `DISTINCT`를 지정하면 <U>같은 결과 값을 가진 데이터는 합계에서 한번만 사용되기 때문이다.</U>

![1-1-2](https://user-images.githubusercontent.com/53929665/93601179-c1197500-f9fb-11ea-87cc-8d7cb8dcc2a1.JPG)

#### (2) COUNT 함수
- 데이터 개수를 출력하는데 사용
- `COUNT`함수에 `*`을 사용하면 SELECT문의 결과 값으로 나온 <U>행 데이터의 개수</U>를 반환해준다.


- 기본형식
```
COUNT([DISTINCT, ALL 중 하나를 선택하거나 아무 값도 지정하지 않음(선택)]
        [개수를 구할 열이나 연산자, 함수를 사용한 데이터(필수)]
OVER(분석을 위한 여러 문법 지정)(선택)
```

#### (2-1) EMP 테이블의 데이터 개수 출력하기


```python
SELECT COUNT(*)
FROM EMP;
```

결과 :

![1-2-1](https://user-images.githubusercontent.com/53929665/93601181-c1b20b80-f9fb-11ea-8268-b24d4395998c.JPG)

#### (2-2) `WHERE`조건절을 붙여 사용하기


```python
SELECT COUNT(*)
FROM EMP
WHERE DEPTNO = 30;
```

결과 :

![1-2-2](https://user-images.githubusercontent.com/53929665/93601182-c1b20b80-f9fb-11ea-84bf-fa3bfa1f7629.JPG)

#### (2-3) ` COUNT` : `DINSTICT`, `ALL` 옵션 사용 


```python
SELECT  COUNT(DISTINCT SAL),
        COUNT(ALL SAL),
        COUNT(SAL)
    FROM EMP
```

결과 :

![1-2-3](https://user-images.githubusercontent.com/53929665/93601183-c24aa200-f9fb-11ea-87fe-d221d4d6313d.JPG)

#### (2-4) `COUNT` : `NULL`데이터 반환 개수 제외 예제 


```python
SELECT  COUNT(COMM)
    FROM EMP;
```

결과 :

![1-2-4](https://user-images.githubusercontent.com/53929665/93601185-c2e33880-f9fb-11ea-94dc-acfdc6904bad.JPG)

#### (2-5) `COUNT` : NULL 이 아닌 데이터값의 개수 출력 예제


```python
SELECT  COUNT(COMM)
    FROM EMP
    WHERE COMM IS NOT NULL;
```

결과 :

![1-2-5](https://user-images.githubusercontent.com/53929665/93601186-c2e33880-f9fb-11ea-9342-cf077bee835b.JPG)

#### (3) MAX, MIN 함수
- 최댓값과 최솟값을 반환하는 함수


- 기본형식

```
MAX([DISTINCT, ALL 중 하나를 선택하거나 아무 값도 지정하지 않음(선택)]
      [개수를 구할 열이나 연산자, 함수를 사용한 데이터(필수)]
OVER(분석을 위한 여러 문법 지정)(선택)
```


```
MIN([DISTINCT, ALL 중 하나를 선택하거나 아무 값도 지정하지 않음(선택)]
      [개수를 구할 열이나 연산자, 함수를 사용한 데이터(필수)]
OVER(분석을 위한 여러 문법 지정)(선택)
```

#### (3-1) 숫자 데이터에 `MAX`, `MIN` 함수 사용하기
    - 부서 번호가 10번인 사원들의 최대 급여 출력하기


```python
SELECT MAX(SAL)
    FROM EMP
    WHERE DEPTNO = 10;
```

결과 :

![1-3-1(1)](https://user-images.githubusercontent.com/53929665/93601187-c37bcf00-f9fb-11ea-9d14-ce8a9701b85d.JPG)

    - 부서 번호가 10번인 사원들의 최소 급여 출력하기


```python
SELECT MIN(SAL)
    FROM EMP
    WHERE DEPTNO = 10;
```

결과 :

![1-3-1(2)](https://user-images.githubusercontent.com/53929665/93601189-c4146580-f9fb-11ea-9e09-7f5ca4d5444b.JPG)

#### (3-2) 날짜 데이터에 `MAX`, `MIN` 함수 사용하기
    - 부서 번호가 20인 사원의 입사일 중 제일 최근 입사일 출력하기


```python
SELECT MAX(HIREDATE)
    FROM EMP
    WHERE DEPTNO = 20;
```

결과 :

![1-3-2(1)](https://user-images.githubusercontent.com/53929665/93601191-c4146580-f9fb-11ea-9a48-7c43e1032eba.JPG)

    - 부서 번호가 20인 사원의 입사일 중 제일 오래된 입사일 출력하기


```python
SELECT MIN(HIREDATE)
    FROM EMP
    WHERE DEPTNO = 20;
```

결과 :

![1-3-2(2)](https://user-images.githubusercontent.com/53929665/93601192-c4acfc00-f9fb-11ea-9e15-2a05741d6f87.JPG)

#### (4) AVG 함수
- 입력 데이터의 평균 값을 구하는 함수
- 숫자 또는 숫자로 암시적 형 변환이 가능한 데이터만 사용할 수 있다.


- 기본형식
```
AVG([DISTINCT, ALL 중 하나를 선택하거나 아무 값도 지정하지 않음(선택)]
    [개수를 구할 열이나 연산자, 함수를 사용한 데이터(필수)]
OVER(분석을 위한 여러 문법 지정)(선택)
```

#### (4-1) `AVG` 함수 사용하기
    - 부서 번호가 30인 사원들의 평균 급여 출력하기 


```python
SELECT  AVG(SAL)
    FROM EMP
    WHERE DEPTNO = 30;
```

결과 :

![1-4-1](https://user-images.githubusercontent.com/53929665/93601193-c4acfc00-f9fb-11ea-9409-21eb80315864.JPG)

#### (4-2) `AVG` : `DISTINCT` 옵션 적용
    - `DISTINCT`로 중복을 제거한 급여 열의 평균 급여 구하기


```python
SELECT AVG(DISTINCT SAL)
    FROM EMP
    WHERE DEPTNO = 30;
```

결과 :

![1-4-2](https://user-images.githubusercontent.com/53929665/93601196-c5459280-f9fb-11ea-9401-c27969a0f505.JPG)


---
### [ 2. GROUP BY 절 ]
- SELECT문에서는 `GROUP BY`절을 작성하여 데이터를 그룹화할 수 있는데 다음과 같이 순서에 맞게 작성하며 그룹으로 묶을 기준 열을 지정한다.

- 기본형식
```
SELECT     [조회할 열1 이름], [열2 이름], ..., [열N 이름]
FROM       [조회할 테이블 이름]
WHERE      [조회할 행을 선별하는 조건식]
GROUP BY   [그룹화할 열을 지정(여러 개 지정 가능)]
ORDER BY   [정렬하려는 열 지정]
```

#### (1) `GROUP BY` 절 사용하기

- `GROUP BY`를 사용하여 부서별 평균 급여 출력하기


```python
SELECT AVG(SAL), DEPTNO
    FROM EMP
    GROUP BY DEPTNO;
```

결과 :
![2-1(1)](https://user-images.githubusercontent.com/53929665/93604694-b0b7c900-fa00-11ea-98d3-59212c4be97c.JPG)

- 부서 번호 및 직책별 평균 급여로 정렬하기


```python
SELECT DEPTNO, JOB, AVG(SAL)
    FROM EMP
    GROUP BY(DEPTNO, JOB);
```

결과 :

![2-1(2)](https://user-images.githubusercontent.com/53929665/93604697-b1505f80-fa00-11ea-9511-f96894d845a9.JPG)

#### (2) `GROUP BY`절을 사용할 때 유의점
- 다중 행 함수를 사용하지 않은 일반 열은 `GROUP BY`절에 명시하지 않으면 SELECT절에서 사용할 수 없다.


- ex) `GROUP BY`절에 없는 열을 SELECT절에 포함시킬 경우


```python
SELECT ENAME, DEPTNO, AVG(SAL)
    FROM EMP
    GROUP BY DEPTNO;
```

결과 :

![2-2](https://user-images.githubusercontent.com/53929665/93604700-b1e8f600-fa00-11ea-970d-45530790bfd6.JPG)


---
### [ 3. HAVING 절 ]
- SELECT문에 <U>`GROUP BY`절이 존재할 때만</U> 사용할 수 있다.
- `GROUP BY`절을 통해 그룹화된 결과 값의 <U>범위를 제한</U>하는데 사용


- 기본형식
```
SELECT     [조회할 열1 이름], [열2 이름], ..., [열N 이름]
FROM       [조회할 테이블 이름]
WHERE      [조회할 행을 선별하는 조건식]
GROUP BY   [그룹화할 열을 지정(여러 개 지정 가능)]
HAVING     [출력 그룹을 제한하는 조건식]
ORDER BY   [정렬하려는 열 지정]
```

#### (1) `GROUP BY`절과 `HAVING`절 사용하기


```python
SELECT DEPTNO, JOB, AVG(SAL)
    FROM EMP
    GROUP BY (DEPTNO, JOB)
        HAVING AVG(SAL) >= 2000
    ORDER BY DEPTNO, JOB;
```

결과 :
- `HAVING`절이 없을 경우
![3-1(1)](https://user-images.githubusercontent.com/53929665/93607380-34bf8000-fa04-11ea-9544-052ceb1628d9.JPG)

- `HAVING`절이 있을 경우
![3-1(2)](https://user-images.githubusercontent.com/53929665/93607381-34bf8000-fa04-11ea-99eb-80817d47aa68.JPG)

#### (2) `HAVING`절을 사용할 떄 유의점
- `WHERE`절과 `HAVING`절은 비슷하지만 다르다!!!
    - `WHERE` : 출력 대상 행을 제한
    - `HAVING` : 그룹화된 대상을 출력에서 제한


- 따라서, 출력결과를 제한하기 위해 `HAVING`절말고 `WHERE`절을 사용하려고하면 다음과 같은 오류가 발생한다.


```python
SELECT DEPTNO, JOB, AVG(SAL)
    FROM EMP
    WHERE AVG(SAL) >= 2000
    GROUP BY DEPTNO, JOB
    ORDER BY DEPTNO, JOB;
```

결과 :

![3-2](https://user-images.githubusercontent.com/53929665/93607383-35581680-fa04-11ea-87e3-34354ed7fcff.JPG)

#### (3) `WHERE`절과 `HAVING`절의 차이점

- `WHERE`절을 사용하지 않고 `HAVING`절만 사용한 경우


```python
SELECT DEPTNO, JOB, AVG(SAL)
    FROM EMP
    GROUP BY DEPTNO, JOB
        HAVING AVG(SAL) >= 2000
    ORDER BY DEPTNO, JOB;
```

결과 :
![3-3(1)](https://user-images.githubusercontent.com/53929665/93607373-32f5bc80-fa04-11ea-8cbf-895e35be000e.JPG)


- `WHERE`절과 `HAVING`절 모두 사용한 경우


```python
SELECT DEPTNO, JOB, AVG(SAL)
    FROM EMP
    WHERE SAL <= 3000
    GROUP BY DEPTNO, JOB
        HAVING AVG(SAL) >= 2000
    ORDER BY DEPTNO, JOB;
```

결과 : SAL의 평균이 5000인 PRESIDENT행이 사라짐

![3-3(2)](https://user-images.githubusercontent.com/53929665/93607378-3426e980-fa04-11ea-94d8-8c02095520a3.JPG)



> (결론)  
> - 위의 결과를 통해, `WHERE`절이 `GROUP BY`절, `HAVING`절 보다 먼저 실행되는 것을 알 수 있다.

---
### [ 4. 그룹화와 관련된 여러 함수 ]

#### (1) ROLLUP, CUBE함수
- `ROLLUP`, `CUBE` : 그룹화 데이터의 합계를 출력할 때 유용하게 사용


- `ROLLUP` 기본형식
```
SELECT [조회할 열1 이름], [열2 이름], ..., [열N 이름]
FROM   [조회할 테이블 이름]
WHERE  [조회할 행을 선별하는 조건식]
GROUP BY ROLLUP [그룹화 열 지정(여러 개 지정 가능)];
```


- `CUBE` 기본형식
```
SELECT [조회할 열1 이름], [열2 이름], ..., [열N 이름]
FROM   [조회할 테이블 이름]
WHERE  [조회할 행을 선별하는 조건식]
GROUP BY CUBE [그룹화 열 지정(여러 개 지정 가능)];
```

#### (1-1) 기존 `GROUP BY`절만 사용한 그룹화


```python
SELECT DEPTNO, JOB, COUNT(*), MAX(SAL), SUM(SAL), AVG(SAL)
    FROM EMP
    GROUP BY DEPTNO, JOB
    ORDER BY DEPTNO, JOB
```

결과 :
![4-1-1](https://user-images.githubusercontent.com/53929665/93713121-f60cff80-fb94-11ea-93f3-7f77b8ea059d.JPG)
![4-1-2(2)](https://user-images.githubusercontent.com/53929665/93713124-f73e2c80-fb94-11ea-8692-d7e805b39c77.JPG)

#### (1-2) `ROLLUP` 함수를 적용한 그룹화


```python
SELECT DEPTNO, JOB, COUNT(*), MAX(SAL), SUM(SAL), AVG(SAL)
    FROM EMP
    GROUP BY ROLLUP(DEPTNO, JOB);
```

결과 : 
- 결과를 살펴보면 `ROLLUP` 함수는 명시한 열을 소그룹부터 대그룹의 순서로  
각 그룹별 결과를 출력하고 마지막에 총 데이터의 결과를 출력한다.


(주의)
- `ROLLUP` 함수에 명시한 열에 한하여 결과가 출력된다
- `ROLLUP` 함수에는 그룹 함수를 지정할 수 없다

![4-1-2](https://user-images.githubusercontent.com/53929665/93713126-f73e2c80-fb94-11ea-9290-750624723c74.JPG)

#### (1-3) `CUBE` 함수를 적용한 그룹화


```python
SELECT DEPTNO, JOB, COUNT(*), MAX(SAL), SUM(SAL), AVG(SAL)
    FROM EMP
    GROUP BY CUBE(DEPTNO, JOB)
    ORDER BY DEPTNO, JOB;
```

결과 :
- `CUBE` 함수는 `ROLLUP` 함수를 사용했을 때보다 좀 더 많은 결과가 나온다.
- `ROLLUP`함수에 없는 결과는 빨간 박스에 해당한다.
- 이렇듯 `CUBE` 함수는 지정한 모든 열에서 가능한 조합의 결과를 모두 출력한다.

![4-1-3](https://user-images.githubusercontent.com/53929665/93713127-f7d6c300-fb94-11ea-85b9-3912ff24ec9b.JPG)

#### (1-4) `ROLLUP`, `CUBE` 함수가 만드는 조합의 가지 수

- `ROLLUP` 함수
    - 지정한 열 수에 따라 다음과 같이 결과 값이 조합된다.
        > ROLLUP(A, B, C)  
        > A 그룹별 B 그룹별 C 그룹에 해당하는 결과 출력  
        > A 그룹별 B 그룹에 해당하는 결과 출력  
        > A 그룹에 해당하는 결과 출력  
        > 전체 데이터 결과 출력
    - 따라서, n개의 열을 지정하면 기본적으로 n+1개의 조합이 출력된다.


- `CUBE` 함수
    - 지정한 모든 열의 조합을 사용
        > CUBE(A, B, C)  
        > A 그룹별 B 그룹별 C 그룹에 해당하는 결과 출력  
        > A 그룹별 C 그룹에 해당하는 결과 출력  
        > B 그룹별 C 그룹에 해당하는 결과 출력  
        > A 그룹별 C 그룹에 해당하는 결과 출력  
        > A 그룹 결과  
        > B 그룹 결과  
        > C 그룹 결과  
        > 전체 데이터 결과  
    - 따라서, n개의 열을 지정하면 2^n 개의 조합이 출력된다.
    

따라서, 두 함수는 지정한 열이 많을수록 출력될 조합이 많아질 것이다.  
특히, `CUBE`함수의 경우는 기하급수적으로 증가한다.  
<U>이를 방지하기 위해 필요한 조합의 출력만 보기 방법</U> -> `Partical Rollup/Cube`

#### (1-5) 부분 `ROLLUP`
    - DEPTNO를 먼저 그룹화한 후 ROLLUP 함수에 JOB 지정하기


```python
SELECT DEPTNO, JOB, COUNT(*)
    FROM EMP
    GROUP BY DEPTNO, ROLLUP(JOB);
```

결과 :

![4-1-5(1)](https://user-images.githubusercontent.com/53929665/93713129-f7d6c300-fb94-11ea-8677-997dc55fe1da.JPG)

- JOB을 먼저 그룹화한 후 ROLLUP 함수에 DEPTNO 지정하기


```python
SELECT DEPTNO, JOB, COUNT(*)
    FROM EMP
    GROUP BY JOB, ROLLUP(DEPTNO);
```

결과 :

![4-1-5(2)](https://user-images.githubusercontent.com/53929665/93713130-f86f5980-fb94-11ea-9cdb-83e4ae6bab0d.JPG)

#### (2) GROUPING SETS 함수
- 같은 수준의 그룹화 열이 여러 개일 때 각 열별 그룹화를 통해 결과 값을 출력하는 데 사용
- 앞에서 본 `ROLLUP`과 `CUBE`함수는 열을 대그룹, 소그룹과 같이 계층적으로 그룹화하여 데이터를 집계했다.
- 하지만, `GROUPING SETS` 함수는 부서별 인원수, 직책별 인원수의 결과 값을 하나의 결과로 출력할 수 있다. (예시를 보는게 이해가 더 빠르다)


- 기본형식
```
SELECT [조회할 열1 이름], [열2 이름], ..., [열N 이름]
FROM   [조회할 테이블 이름]
WHERE  [조회할 행을 선별하는 조건식]
GROUP BY GROUPING SET [그룹화 열 지정(여러 개 지정 가능)];
```

#### (2-1) `GROUPING SETS` 함수를 사용하여 열별로 그룹으로 묶어 출력


```python
SELECT DEPTNO, JOB, COUNT(*)
    FROM EMP
    GROUP BY GROUPING SETS(DEPTNO, JOB);
```

결과 :
- 계층적으로 분류되지 않고 각각 따로 그룹화한 후 연산을 수행했음을 알 수 있다.

![4-2-1](https://user-images.githubusercontent.com/53929665/93713131-f86f5980-fb94-11ea-979a-cb3d8d4c2ae8.JPG)

#### (3) 그룹화 함수
- 그룹화 함수는 데이터 자체의 가공이나 특별한 연산 기능을 수행하지는 않지만,
- <U>그룹화 데이터의 식별이 쉽고 가독성을 높이기 위한 목적으로 사용한다.</U>
- `GROUPIING` 함수, `GROUPING_ID` 함수

#### (3-1) `GROUPING` 함수
- `GROUPING` 함수는 `ROLLUP` 또는 `CUBE` 함수를 사용한 `GROUP BY` 절에  
그룹화 대상으로 지정한 열이 그룹화된 상태로 결과가 집게되었는지 확인하는 데 사용


- `GROUP BY` 절에 명시된 열 중 하나를 지정할 수 있다.


- 기본형식
```
SELECT [조회할 열1 이름], [열2 이름], ..., [열N 이름],
    GROUPING [GROUP BY절에 ROLLUP 또는 CUBE에 명시한 그룹화 할 열 이름]
FROM   [조회할 테이블 이름]
WHERE  [조회할 행을 선별하는 조건식]
GRJOUP BY ROLLUP 또는 CUBE [그룹화할 열];
```

#### (3-1-1)  그룹화 여부를 `GROUPING` 함수로 확인하기


```python
SELECT  DEPTNO, JOB, COUNT(*), MAX(SAL), SUM(SAL), AVG(SAL),
        GROUPING(DEPTNO),
        GROUPING(JOB)
    FROM EMP
    GROUP BY CUBE(DEPTNO, JOB)
    ORDER BY DEPTNO, JOB;
```

결과 :
- 0 : `GROUPING`함수에 지정한 열이 그룹화 되었음
- 1 : `GROUPING`함수에 지정한 열이 그룹화 되지 않았음
- 따라서, `GROUPING`함수의 결과 값을 통해서 출력되는 데이터가 어떤 열의 그룹화를 통해 나온 것인지 알 수 있다.

![4-3-1-1](https://user-images.githubusercontent.com/53929665/93713133-f907f000-fb94-11ea-80ee-2859b9eacfc8.JPG)

#### (3-1-2) `DECODE`문으로 `GROUPING` 함수를 적용하여 결과 표기


```python
SELECT  DECODE(GROUPING(DEPTNO), 1, 'ALL_DEPT', DEPTNO) AS DEPTNO,
        DECODE(GROUPING(JOB), 1, 'ALL_JOB', JOB) AS JOB,
        COUNT(*), MAX(SAL), SUM(SAL), AVG(SAL)
   FROM EMP
   GROUP BY CUBE(DEPTNO, JOB)
   ORDER BY DEPTNO, JOB;
```

결과 :
- 0과 1로만 출력된다는 점에서 다음과 같이 결과를 만들어 낼 수도 있다.

![4-3-1-2](https://user-images.githubusercontent.com/53929665/93713134-f907f000-fb94-11ea-989e-ab9ef31b13ec.JPG)

#### (3-2) `GROUPING_ID` 함수
- `GROUPING` 함수와 마찬가지로 `ROLLUP` 또는 `CUBE` 함수로 연산할 때 특정 열이 그룹화 되었는지를 출력하는 함수이다.


- 그룹화 여부를 검사할 열을 하나씩 지정하는 `GROUPING`함수와 달리  
`GROUPING_ID` 함수는 한 번에 여러 열을 지정할 수 있다.


- 기본형식
```
SELECT [조회할 열1 이름], [열2 이름], ..., [열N 이름]
    GROUPING_ID [그룹화 여부를 확인할 열(여러개 지정 가능)]
FROM   [조회할 테이블 이름]
WHERE  [조회할 행을 선별하는 조건식]
GROUP BY ROLLUP 또는 CUBE [그룹화할 열];
```


- `GROUPING_ID` 함수를 사용한 결과는 <U>그룹화 비트 벡터</U>값으로 나타낸다.  
`GROUPING_ID(a, b)와 같이 열을 두 개 지정한다면 출력 결과는 다음과 같다.  
<U>(0과 1로 구성된 그룹화 비트 벡터 값을 2진수로 보고 10진수로 바꾼 값이 최종 결과로 출력)</U>

|그룹화된 열|그룹화 비트 벡터|최종 결과|
|:---------:|:--------------:|:-------:|
|a, b|0 0|0|
|a|0 1|1|
|b|1 0|2|
|없음|1 1|3|

#### (3-2-1) `GROUPING_ID` 함수 사용하기


```python
SELECT  DEPTNO, JOB, COUNT(*), SUM(SAL),
        GROUPING(DEPTNO),
        GROUPING(JOB),
        GROUPING_ID(DEPTNO, JOB)
    FROM EMP
    GROUP BY CUBE(DEPTNO, JOB)
    ORDER BY DEPTNO, JOB;
```

결과 :

![4-3-2](https://user-images.githubusercontent.com/53929665/93713135-f9a08680-fb94-11ea-8561-723cd1a44777.JPG)

#### (4) LISTAGG 함수

- 오라클 11g 버전 부터 사용할 수 있음.
- 그룹에 속해 있는 데이터를 가로로 나열할 때 사용
- `GROUP BY`없이 사용못함


- 기본형태
```
SELECT [조회할 열1 이름], [열2 이름], ..., [열N 이름]
    LISTAGG([나열할 열(필수)], [각 데이터를 구분하는 구분자(선택)])
    WITHIN GROUP(ORDER BY 나열할 열의 정렬 기준 열 (선택)]
FROM   [조회할 테이블 이름]
WHERE  [조회할 행을 선별하는 조건식];
```

#### (4-1) `LISTAGG` 함수 사용하기
- 부서별 사원 이름을 나란히 나열하여 출력하기


```python
SELECT  DEPTNO,
        LISTAGG(ENAME, ',')
        WITHIN GROUP(ORDER BY SAL DESC) AS ENAMES
    FROM EMP
    GROUP BY DEPTNO;
```

결과 :

![4-4-1](https://user-images.githubusercontent.com/53929665/93713136-f9a08680-fb94-11ea-9e41-1bda64a19404.JPG)

#### (5) PIVOT, UNPIVOT 함수
- 오라클 11g 버전 부터 사용할 수 있음.
- `PIVOT` : 기존 테이블 행을 열로 바꿔서 출력
- `UNPIVOT` : 기존 테이블 열을 행으로 바꿔서 출력

#### (5-1) `PIVOT` 함수
- `PIVOT`과 비교하기위한 `GROUP BY`절을 활용한 데이터 테이블


```python
SELECT DEPTNO, JOB, MAX(SAL)
    FROM EMP
    GROUP BY DEPTNO, JOB
    ORDER BY DEPTNO, JOB;
```

결과 :

![4-5-1(1)](https://user-images.githubusercontent.com/53929665/93713137-fa391d00-fb94-11ea-8e7c-59a85aa86594.JPG)

- 위와 같이 세로로만 나열되는 DEPTNO와 JOB열 값을 `PIVOT` 함수를 사용하면 스프레드시트처럼 가로와 세로로 나누어 출력할 수 있다.


```python
SELECT *
    FROM(SELECT DEPTNO, JOB, SAL FROM EMP)
    PIVOT(MAX(SAL) FOR DEPTNO IN (10, 20, 30))
    ORDER BY JOB;
```

결과 :

![4-5-1(2)](https://user-images.githubusercontent.com/53929665/93713139-fa391d00-fb94-11ea-9425-add68f71d1c5.JPG)


```python
SELECT *
    FROM(SELECT DEPTNO, JOB, SAL FROM EMP)
    PIVOT(MAX(SAL) FOR JOB IN ('CLERK' AS CLERK,
                               'SALESMAN' AS SALESMAN, 
                               'PRESIDENT' AS PRESIDENT, 
                               'MANAGER' AS MANAGER,
                               'ANALYST' AS ANALYST))
    ORDER BY DEPTNO;
```

결과 :

![4-5-1(3)](https://user-images.githubusercontent.com/53929665/93713141-fad1b380-fb94-11ea-8cf7-5cdbb4ddb10d.JPG)

- 위의 결과를 `PIVOT`함수없이 `DECODE`함수로 아래와 같이 구현이 가능하다. (오라클 11g 이전)


```python
SELECT  DEPTNO,
        MAX(DECODE(JOB, 'CLERK', SAL)) AS CLERK,
        MAX(DECODE(JOB, 'SALESMAN', SAL)) AS SALESMAN,
        MAX(DECODE(JOB, 'PRESIDENT', SAL)) AS PRESIDENT,
        MAX(DECODE(JOB, 'MANAGER', SAL)) AS MANAGER,
        MAX(DECODE(JOB, 'ANALYST', SAL)) AS ANALYST
    FROM EMP
    GROUP BY DEPTNO
    ORDER BY DEPTNO;
```

결과는 위와 동일

#### (5-2) `UNPIVOT` 함수
- `PIVOT`함수와 반대의 기능
- `PIVOT`함수와 동일하게 SELECT문을 먼저 작성하고 출력 데이터를 명시한 후 세로로 늘어뜨릴 가로 열을 `FOR`에 명시한다.


```python
SELECT *
    FROM( SELECT  DEPTNO,
        MAX(DECODE(JOB, 'CLERK', SAL)) AS CLERK,
        MAX(DECODE(JOB, 'SALESMAN', SAL)) AS SALESMAN,
        MAX(DECODE(JOB, 'PRESIDENT', SAL)) AS PRESIDENT,
        MAX(DECODE(JOB, 'MANAGER', SAL)) AS MANAGER,
        MAX(DECODE(JOB, 'ANALYST', SAL)) AS ANALYST
        FROM EMP
        GROUP BY DEPTNO
        ORDER BY DEPTNO)
     UNPIVOT(SAL FOR JOB IN(CLERK, SALESMAN, PRESIDENT, MANAGER, ANALYST))
     ORDER BY DEPTNO, JOB;
```

결과 :

![4-5-2](https://user-images.githubusercontent.com/53929665/93713142-fad1b380-fb94-11ea-88a9-4f15ca5a6acb.JPG)


그외 사용방법 : oracle.com/technetwork/articles/sql/11g-pivot-097235.html

<br>

---

### References

- 이지훈, 『Do it! 오라클로 배우는 데이터베이스』, 이지스퍼블리싱 (2018)

