---
layout: post
title:  "[MySQL] WINDOW 함수의 ROW/RANGE"
subtitle: "[MySQL] WINDOW 함수의 ROW/RANGE"
categories: devlang
tags: (dbms)mysql
comments: true
---
#### [ MySQL ] WINDOW 함수의 ROW/RANGE
- 본 포스팅은   WINDOW 함수의 `ROW/RANGE`에 관하여 학습합니다


---

### WINDOW 함수 기본 형태

```sql
WINDOW함수 OVER([PARTITION BY column] [ORDER BY column [ASC|DESC]]  
[[ROWS|RANGE] BETWEEN UNBOUNDED PRECEDING[CURRENT ROW] 
				  AND UNBOUNDED FOLLOWING[CURRENT ROW] ]  
)
```
####  ROWS/RANGE
- `ROWS` : (개인적으로 자주 이용!)<u>물리적인 단위</u>로 행 집합을 지정
	-  PARTITION BY 첨가 =>동일 수치의 중복값이 있을 때  두 수치에 대한 최종 결과값을 출력한다.
- `RANGE` : (이런게 있다 정도로만 이해!)<u>논리적인 단위</u>로 행 집합을 지정
	-  PARTITION BY 첨가 => 동일 수치의 중복값이 있어도 행의 순서에 따라서 결과값을 순차적으로 출력한다.

<br>

#### BETWEEN "A" AND "B" 요소

- `UNBOUNDED PRECEDING` : <u>시작 위치가 첫 번째 로우</u>임을 의미
- `UNBOUNDED FOLLOWING` : <u>마지막 위치가 마지막 로우</u>임을 의미
- `[ROW수] PRECEDING` : <u시작 위치가 ROW수 만큼 이전</u>임을 의미
- `[ROW수] FOLLOWING` : <u시작 위치가 ROW수 만큼 이후</u>임을 의미
- `CURRENT ROW` : <U>현재 ROW</u>를 의미

<br>

예제는 이해를 쉽게하기 위하여 SUM()함수로 모두 진행한다.

<br>

#### 예제 1-1 ) `ROWS` BETWEEN `UNBOUNDED PRECEDING` AND `CURRENT ROW`

- STEP 1) PARTITION BY 사용 X
```sql
SELECT ENAME, JOB, SAL,
	   SUM(SAL) OVER(ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS ACC_SUM
FROM employees.emp
ORDER BY JOB;
```

- STEP 2) PARTITION BY 사용 O

```sql
SELECT ENAME, JOB, SAL,
	   SUM(SAL) OVER(PARTITION BY job
					 ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS ACC_SUM
FROM employees.emp
ORDER BY JOB;
```

![IMG3](https://user-images.githubusercontent.com/53929665/104948013-ecd75680-59ff-11eb-9cb5-35a24ba73b45.JPG)


`ROWS`를 `UNBOUNDED PRECEDING` 와 `CURRENT ROW`를 같이 사용하게 될 경우 오름차순으로 sal이 누적으로 합산되는 것을 확인할 수 있다. 

<br>

더불어, `PARTITION BY`와 같이 사용했을 때, 각 job value 파티션별로 오름차순으로 누적 합산이 진행되는 것을 볼 수 있다.

<br>

`ROWS` 같은 경우는 각 행 혹은 파티션별로 누적 합산(그이외의 윈도우 함수를 포함) 값을 출력할 수 있다는 점에서 개인적으로는 `RANGE` 보다  더 유용하게 사용하는 중이다.

<br>

#### 예제 1-2 ) `ROWS` BETWEEN `CURRENT ROW` AND `UNBOUNDED FOLLOWING`

- STEP 1) PARTITION BY 사용 X
```sql
SELECT ENAME, JOB, SAL,
	   SUM(SAL) OVER(ROWS BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING) AS ACC_SUM
FROM employees.emp
ORDER BY JOB;
```

- STEP 2) PARTITION BY 사용 O

```sql
SELECT ENAME, JOB, SAL,
	   SUM(SAL) OVER(PARTITION BY job
					 ROWS BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING) AS ACC_SUM
FROM employees.emp
ORDER BY JOB;
```

![IMG4](https://user-images.githubusercontent.com/53929665/104948019-ee088380-59ff-11eb-8ed5-6690997f2ed4.JPG)


`ROWS`를  `CURRENT ROW` 와 `UNBOUNDED FOLLOWING` 을 같이 사용하게 될 경우 내림차순으로 sal이 누적으로 합산되는 것을 확인할 수 있다.

<br>

---

<br>

#### 예제 2-1 ) `RANGE` BETWEEN `UNBOUNDED PRECEDING` AND `CURRENT ROW`

- STEP 1) ORDER BY 사용 X, PARTITION BY 사용 X
```sql
SELECT ENAME, JOB, SAL,
	   SUM(SAL) OVER(RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS ACC_SUM
FROM employees.emp
ORDER BY JOB;
```

- STEP 2) ORDER BY 사용 O, PARTITION BY 사용 X

```sql
SELECT ENAME, JOB, SAL,
	   SUM(SAL) OVER(ORDER BY job RANGE BETWEEN UNBOUNDED PRECEDING 
									    AND CURRENT ROW) AS ACC_SUM
FROM employees.emp
ORDER BY JOB;
```

- STEP 3) ORDER BY 사용 O, PARTITION BY 사용 O

```sql
SELECT ENAME, JOB, SAL,
	   SUM(SAL) OVER(PARTITION BY job ORDER BY job  
					 RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS ACC_SUM
FROM employees.emp
ORDER BY JOB;
```

![IMG1](https://user-images.githubusercontent.com/53929665/104946167-f7442100-59fc-11eb-9ec2-4fc963f4fc22.JPG)

`PARTITION BY`와 `ORDER BY` 없이 `RANGE` 와 `UNBOUNDED PRECEDING` 그리고 `CURRENT ROW` 를 같이 사용하였을 때는 SUM(sal) OVER()과 똑같은 결과가 나오는 것을 확인할 수 있다.

<br>

여기서 job에 관하여 `ORDER BY`를 추가하게 될 경우,  각 job의 sal을 모두 더한 값을 오름차순으로 순차적으로 더하는 것을 확인할 수 있다. 여기서 오름차순으로 누적합이 계산되는 이유는 `UNDERBOUNDED PRECEDING`에 의해서 이전 row의 최종 출력값을 가져오기 때문이다.  또한 이는 SUM(sal) OVER(ORDER BY JOB ASC)의 결과값과 동일하다.

<br>

더 나아가 job에 관하여 `PARTITION BY`를 추가하게 될 경우, 각 job 파티션에 관하여 누적값이 파티션별로 산출되는 것을 확인할 수 있다.  해당 결과는 SUM(sal) OVER(PARTITION BY JOB)과 동일하기 때문에 `UNBOUNDED PRECEDING`과 `CURRENT ROW`를 사용했다는 효과를 보기는 힘들다. 오히려 쿼리상 길어지기 때문에  SUM(sal) OVER(PARTITION BY JOB)를 사용하는 것이 더 간단하다.

<br>

---

<br>

#### 예제 2-2 ) `RANGE` BETWEEN `UNBOUNDED PRECEDING` AND `CURRENT ROW`

- STEP 1) ORDER BY 사용 X, PARTITION BY 사용 X
```sql
SELECT ENAME, JOB, SAL,
	   SUM(SAL) OVER(RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS ACC_SUM
FROM employees.emp
ORDER BY JOB;
```

- STEP 2) ORDER BY 사용 O, PARTITION BY 사용 X

```sql
SELECT ENAME, JOB, SAL,
	   SUM(SAL) OVER(ORDER BY job RANGE BETWEEN UNBOUNDED PRECEDING
									    AND CURRENT ROW) AS ACC_SUM
FROM employees.emp
ORDER BY JOB;
```

- STEP 3) ORDER BY 사용 O, PARTITION BY 사용 O

```sql
SELECT ENAME, JOB, SAL,
	   SUM(SAL) OVER(PARTITION BY job ORDER BY job 
					 RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS ACC_SUM
FROM employees.emp
ORDER BY JOB;
```

![IMG2](https://user-images.githubusercontent.com/53929665/104947220-c95fdc00-59fe-11eb-9a2a-336d77373629.JPG)


기본적인 설명은 <b>예제 1-1</b>과 동일하며, 차이점이 있다면 `UNBOUNDED FOLLOWING`에 의해 내림차순으로 각 job value의 sum(sal)이 누적 집계가 진행되는 것을 확인할 수 있다. 마찬가지로  SUM(sal) OVER(ORDER BY JOB DESC)와 동일한 결과값을 출력한다.

<br>

개인적으로는 `RANGE`와 `BETWEEN "A" AND "B"` 를 같이 사용하기 보다 기존에 사용하던 방식인 `WINDOW함수 OVER([PARTITION BY column] [ORDER BY column [ASC|DESC]] )`을 계속 사용할 것 같다.

<br>

---

<br>

####  예제 3) [ROW수] PRECEDING / [ROW수] FOLLOWING

![num1](https://user-images.githubusercontent.com/53929665/104949665-adf6d000-5a02-11eb-9852-ee8f4c063dd7.JPG)

![num2](https://user-images.githubusercontent.com/53929665/104949861-fb733d00-5a02-11eb-965f-fbe0aa076d3c.JPG)

`[ROW수] PRECEDING / [ROW수] FOLLOWING`는 `ORDER BY` 와 `ROWS`를 같이 사용해야 한다는 점과 ROWS의 수에 따른 이전까지의 값 이후까지의 값을 윈도우 함수에 따라 계산하는 것만 기억하도록 하자!

<br>

---

### References

- https://superkong1.tistory.com/42
- https://hongsii.github.io/2017/12/12/window-function-range/

