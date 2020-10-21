---

layout: post
title:  "[BigQuery] ARRAY, UNNEST, STRUCT (1)"
subtitle: "[BigQuery] ARRAY, UNNEST, STRUCT의 기본적인 생성과 특징"
categories: gcp
tags: bigquery
comments: true

---

#### BigQuery `ARRAY`, `UNNEST`, `STRUCT`
- 본 포스팅은 BigQuery의 `ARRAY`, `UNNEST`, `STRUCT`에 관한  <u>기본적인</u> 정보를  다룹니다.

#### contents
- 배열 생성
- 배열 요소 접근 - `OFFSET`, `ORDINARY`
- 길이확인 -`ARRAY_LENGTH`
- 배열 평면화 - `UNNEST`
- 구조체 - `STRUCT`

---

### [ 0. BASIC ]
- BigQuery에서 배열은 데이터 유형이 동일한 0개 이상의 값으로 구성된 순서가 지정된 목록을 의미한다.



- `INT64`와 같은 단순한 자료형 or `STRUCT`와 같은 복합 데이터 유형의 배열을 생성할 수 있다.
- 하지만, `ARRAY`의 배열은 지원하지 않는다.


- 배열에는 `NULL`이 포함될 수 있다.
- BigQuery UI에서 배열로 보여줄 때 세로로 나열된다.
- 하나의 행에 <U>데이터 타입이 동일한 여러 값</u>을 저장한다.

---
### [ 1. 배열 생성 ]
### (1-1) Using Array Literals `[]`
- `[` 와 `]`를 이용해서 Array Literal을 구성할 수 있다.
- 배열의 각 요소는 쉼표로 구분된다.

```SQL
SELECT [1, 2, 3] AS numbers;

SELECT ['apple', 'pear', 'orange'] AS fruit;

SELECT [true, false, true ] AS booleans;
```

다음과 같이 `SELECT` 절을 이용해서 서브쿼리에 있는 값들을 배열로 표현할 수 있다.

 ```SQL
SELECT [a, b, c]
FROM  (SELECT 5 AS a,37 AS b, 406 AS c);

SELECT [a, b, c]
FROM  (SELECT CAST(5 AS INT64) AS a,
				  CAST(37 AS FLOAT64) AS b,         
				  406 AS c);
```

- 또한, `ARRAY<원하는데이터자료형> `을 배열 리스트앞에 선언하여 기존 배열의 데이터 자료형을 변환시켜 출력할 수 있다.

아래의 쿼리문 결과, 정수형으로 이루어진 배열은 실수형태로 출력된 것을 확인할 수 있다.

``` SQL
SELECT ARRAY<FLOAT64>[1, 2, 3] AS floats;
```

### (1-2) Using Generated Values
- 파이썬의 연속적인 list 배열 생성처럼 BigQuery에서도 할 수 있다.

#### (1-2-1) 정수 배열 생성
- `GENERATE_ARRAY`
	- 시작, 종료, 그리고 step 값을 이용하여 연속적인 배열을 생성한다.

아래의 쿼리 결과 11부터 33까지 공차가 2인 배열이 생성되는 것을 확인할 수 있다.

```SQL
SELECT GENERATE_ARRAY(11, 33, 2) AS odds;
```
<img width="50" alt="(1-2-1) 1" src="https://user-images.githubusercontent.com/53929665/96611754-f76a4d00-1337-11eb-9e7f-cd22169cb27a.PNG">

step 값이 음수일 때도 정상적으로 표현된다.

```SQL
SELECT GENERATE_ARRAY(21, 14, -1) AS countdown;
```

<img width="75" alt="(1-2-1) 2" src="https://user-images.githubusercontent.com/53929665/96611757-f802e380-1337-11eb-8c82-7581ea46a321.PNG">


#### (1-2-2) 날짜 배열 생성
- `GENERATE_DATE_ARRAY`
	- 날짜의 시작, 종료, 그리고 `INTERVAL`을 이용하여 배열을 생성한다.
	- DATE값 집합을 생성할 수 있다.

아래의 쿼리 결과,  7일 단위씩 배열이 생성되는 것을 확인할 수 있다.

```SQL
SELECT  GENERATE_DATE_ARRAY('2017-11-21', '2017-12-31', INTERVAL 1 WEEK) AS date_array;
```

<img width="75" alt="1-2-2" src="https://user-images.githubusercontent.com/53929665/96611758-f89b7a00-1337-11eb-97d3-9c6ff6d251b8.PNG">

---
### [ 2. 배열 요소 접근 - OFFSET, ORDINARY ]
- 배열의 N번째 값을 가져오고 싶을 때
	- `OFFSET(N)` : 배열의 초기 인덱스가 0부터 시작해서 N번째 값
	- `ORDINARY(N)` : 배열의 초기 인덱스가 1부터 시작해서 N번째 값

아래의 쿼리 결과

-  `OFFSET(1)`일 경우,  
각 행의 (1행) 0, (2행) 2, (3행) 5는 개별적으로 인덱스가 0번째 순서로 지정된다.  
그리고, N=1 이므로 0번 째 기준에서의 첫 번째 인덱스를 의미한다.  
따라서, (1행) 1, (2행) 4, (3행) 10 이 출력된다.   

- `ORDINARY(3)` 일 경우,  
각 행의 (1행) 0, (2행) 2, (3행) 5는 개별적으로 인덱스가 1번째 순서로 지정된다.  
그리고, N=3 이므로 첫 번째 기준에서의 두 번째 인덱스를 의미한다.  
따라서, (1행) 1, (2행) 8, (3행) 30 이 출력된다.


```SQL
WITH sequences AS
  (SELECT [0, 1, 1, 2, 3, 5] AS some_numbers
   UNION ALL SELECT [2, 4, 8, 16, 32] AS some_numbers
   UNION ALL SELECT [5, 10, 30] AS some_numbers)
SELECT some_numbers,
       some_numbers[OFFSET(1)] AS offset_1,
       some_numbers[ORDINAL(3)] AS ordinal_1
FROM sequences;
```

<img width="150" alt="2" src="https://user-images.githubusercontent.com/53929665/96611759-f89b7a00-1337-11eb-86cf-af3feab2d30b.PNG">

---
### [ 3. 길이확인 - ARRAY_LENGTH ]
- `ARRAY_LENGTH()`
	- 배열의 길이를 반환한다.

```SQL
WITH sequences AS
  (SELECT [0, 1, 1, 2, 3, 5] AS some_numbers
   UNION ALL SELECT [2, 4, 8, 16, 32] AS some_numbers
   UNION ALL SELECT [5, 10] AS some_numbers)
SELECT some_numbers,
       ARRAY_LENGTH(some_numbers) AS len
FROM sequences;
```

<img width="125" alt="3" src="https://user-images.githubusercontent.com/53929665/96611760-f9341080-1337-11eb-9605-b840b60880f3.PNG">

---
### [ 4. 배열 평면화 - UNNEST ]
- `UNNEST`
	- `ARRAY`를 일련의 행으로 변환
	- `UNNEST` 연산자는 `ARRAY`를 입력으로 받고 배열안의 각 요소를 행 하나 하나에 개별적으로 반환한 테이블을 생성한다.
	- `UNNEST()` 내부에는 단 하나의 행만 생성하는 (ex. 스칼라서브쿼리) 반환하는 값을 넣을 수 있다.

```SQL
SELECT *
# UNNEST 내부에 1행만을 반환하는 배열이 들어갔다
FROM UNNEST(['foo', 'bar', 'baz', 'qux',
		     'corge', 'garply', 'waldo', 'fred']) AS element

WITH OFFSET AS offset # WITH OFFSET 절을 같이 사용하여 표현하는 경우가 많다.

ORDER BY offset;
```

<img width="100" alt="4(1)" src="https://user-images.githubusercontent.com/53929665/96611762-f9341080-1337-11eb-8618-a8775a906f9c.PNG">

- `CROSS JOIN`
	- 각 행의 다른 열 값을 보존하면서, <U>다수 행의 `ARRAY`를 평면화할 때 사용</u>
	- `CROSS JOIN`말고 `,`를 사용해도 동일한 결과를 얻을 수 있다.

```SQL
WITH sequences AS
  (SELECT 1 AS id, [0, 1, 1, 2, 3, 5] AS some_numbers
   UNION ALL SELECT 2 AS id, [2, 4, 8, 16, 32] AS some_numbers
   UNION ALL SELECT 3 AS id, [5, 10] AS some_numbers)

SELECT id, flattened_numbers
FROM sequences
	 CROSS JOIN
	 # sequences 서브쿼리는 3개의 행을 반환하기에, 그냥 넣으면 오류가 발생한다.
	 # 따라서, CROSS JOIN 사용 권장
	 UNNEST(sequences.some_numbers) AS flattened_numbers;

# 또는
# FROM sequences,
#      UNNEST(sequences.some_numbers) AS flattened_numbers;
```

<img width="250" alt="4(2)" src="https://user-images.githubusercontent.com/53929665/96611764-f9cca700-1337-11eb-8fe3-a401efec2343.PNG">


---
### [ 5.  구조체 - STRUCT ]
- 구조체로, BigQuery UI에서 RECORD로 표현된다.

### (5-1) STRUCT 생성
#### (5-1-1)  `(`, `)` 사용

```SQL
SELECT (1,2,3) AS struct_test

```
<img width="250" alt="5" src="https://user-images.githubusercontent.com/53929665/96611771-fafdd400-1337-11eb-9128-7ade18019f07.PNG">

#### (5-1-2) `<`, `>` 안에 타입을 지정해서 사용

```SQL
SELECT STRUCT<INT64, FLOAT64, STRING>(1, 2, 'HI') AS struct_test
```

<img width="250" alt="5(2)" src="https://user-images.githubusercontent.com/53929665/96611768-fa653d80-1337-11eb-8d3e-5198cc9756b2.PNG">

- 또한 다음과 같이 타입 앞에 이름을 지정할 수 있다.

```SQL
SELECT STRUCT<hi INT64, hello FLOAT64, awesome STRING>(1, 2, 'HI') AS struct_test
```

<img width="250" alt="5(3)" src="https://user-images.githubusercontent.com/53929665/96611769-fa653d80-1337-11eb-9313-676473ad44ad.PNG">


위의 경우를 응용하면 다음과 같이 표현할 수 있다.   
`STRUCT < x STRUCT < y INT64, z STRING > >`  
이는`STRUNCT` 안에 X라는 이름을 가진 `STRUCT`가 존재하고,  
그 X라는 이름의 `STRUCT`안에는 INT 64인 값과 STRING 값이 저장되어있다는 의미이다.

```SQL
 SELECT STRUCT<
         struct_example STRUCT<y INT64, z STRING>
         >((2, 'HI')) AS struct_test
```

<img width="250" alt="응용한거" src="https://user-images.githubusercontent.com/53929665/96611773-fb966a80-1337-11eb-983e-d5ec9d8781fb.PNG">

- 또 다른 경우, 타입을 지정하지 않고, AS로 이름을 지정할 수 있다.

```SQL
SELECT STRUCT(1 as hi, 2 as hello, 'HI' as awesome) AS struct_test
```

<img width="250" alt="5(4)" src="https://user-images.githubusercontent.com/53929665/96611770-fafdd400-1337-11eb-998b-09fbdd21ea28.PNG">

### (5-2) `ARRAY` 안에 `STRUCT`를 사용하고 싶은 경우
- `ARRAY(SELECT AS STRUCT)`의 형태를 사용한다.

```SQL
  SELECT
    ARRAY(
      SELECT AS STRUCT 1 as hi, 2, 3
      UNION ALL
      SELECT AS STRUCT 4 as hi, 5, 6
    ) AS new_array
```

<img width="250" alt="STURCT마지막" src="https://user-images.githubusercontent.com/53929665/96611772-fb966a80-1337-11eb-8b73-13eeca9ff069.PNG">


---
#### index
- GCP BigQuery Array Document : https://cloud.google.com/bigquery/docs/reference/standard-sql/arrays
- zzsza님의 github 블로그 : https://zzsza.github.io/gcp/2020/04/12/bigquery-unnest-array-struct/#bigquery-struct
