---
layout: post
title:  "[BigQuery] ARRAY, UNNEST, STRUCT (2)"
subtitle: "[BigQuery] ARRAY, UNNEST, STRUCT의 검색, 집계"
categories: gcp
tags: bigquery
comments: true

---

#### BigQuery `ARRAY`, `UNNEST`, `STRUCT`
- 본 포스팅은 BigQuery의 `ARRAY`, `UNNEST`, `STRUCT`에 관한<br>  <u>검색, 집계</u>에 관한 정보를  다룹니다.

#### contents
- 배열 검색 - `IN`, `EXSITS`
- 배열과 집계 - `ARRAY_AGG`, `ARRAY_CONCAT_AGG`

---

### [ 1. 배열 검색 ]

- `UNNEST`의 `IN` 연산자
		- 배열에 특정 값이 있는지 확인하기 위해서 사용한다.
<br>
- `UNNEST`의 `EXISTS` 연산자
	- 배열에 조건과 일치하는 값이 있는지 확인

###  특정 값 검색
- 배열에서 <u>특정 값을 검색</u>하려면 `IN`연산자를 사용해야한다.
- 다음 예시는 배열에 숫자 2가 있으면 반환한다.

```SQL
SELECT 2 IN UNNEST([0, 1, 1, 2, 3, 5]) AS contains_value;
```

<img width="150" alt="1" src="https://user-images.githubusercontent.com/53929665/96666280-c28ae400-1391-11eb-92a2-4c711d8d7d62.PNG">

- 배열 열에 <u>특정 값이 있는 테이블 행을 반환하려면</u><br> `WHERE` 절을 사용하여 `IN UNNEST` 결과를 필터링한다.
- 다음 예시는 각 행의 배열 값으로 2를 가지고 있는 행의 id 값을  모두 반환한다.

```SQL
WITH sequences AS
  (SELECT 1 AS id, [0, 1, 1, 2, 3, 5] AS some_numbers
   UNION ALL SELECT 2 AS id, [2, 4, 8, 16, 32] AS some_numbers
   UNION ALL SELECT 3 AS id, [5, 10] AS some_numbers)
SELECT id AS matching_rows
FROM sequences
WHERE 2 IN UNNEST(sequences.some_numbers)
ORDER BY matching_rows;
```

<img width="150" alt="1-1" src="https://user-images.githubusercontent.com/53929665/96666285-c3237a80-1391-11eb-86b9-6eb16327aa7f.PNG">

### 조건을 충족하는 값 검색
- 배열에서 <u>조건과 일치하는 값을 검색</u>하려면<br>`UNNEST`를 사용하여 배열의 요소 테이블을 반환하고,<br>`WHERE`를 사용하여 서브 쿼리에서 결과 테이블을 필터링 시킨 뒤에<br>`EXIST`를 사용하여 필터링된 테이블에 행이 포함되어 있는지 확인한다.
- 다음 예시는 배열 열에 5보다 큰 숫자를 가진 행의 id값을 반환한다.

```SQL
WITH sequences AS
  (SELECT 1 AS id, [0, 1, 1, 2, 3, 5] AS some_numbers
   UNION ALL SELECT 2 AS id, [2, 4, 8, 16, 32] AS some_numbers
   UNION ALL SELECT 3 AS id, [5, 10] AS some_numbers)
SELECT id AS matching_rows FROM sequences
WHERE EXISTS (SELECT *
              FROM UNNEST(some_numbers) AS x
              WHERE x > 5);
```

<img width="150" alt="1-2" src="https://user-images.githubusercontent.com/53929665/96666287-c3bc1100-1391-11eb-9b84-c5c4be2f8858.PNG">

### 조건을 충족하는 STRUCT 필드 값 검색
- `STRUCT`로 구성된 배열에서<br>값이 조건과 일치하는 필드를 검색하려면 `UNNEST`를 사용하여<br> 각 `STRUCT`필드에 대한 열이 있는 테이블을 반환한 후<br>`WHERE EXSITS`를 사용하여 이 테이블에서 일치하지 않는 행을 필터링한다.
-  다음 예시는 배열 열에 `STRUCT`필드의 값이 3보다 큰 b가 포하된 행을 반환한다.

```SQL
WITH sequences AS
  (SELECT 1 AS id, [STRUCT(0 AS a, 1 AS b)] AS some_numbers
   UNION ALL SELECT 2 AS id, [STRUCT(2 AS a, 4 AS b)] AS some_numbers
   UNION ALL SELECT 3 AS id, [STRUCT(5 AS a, 3 AS b), STRUCT (7 AS a, 4 AS b)]
     AS some_numbers)
SELECT id AS matching_rows
FROM sequences
WHERE EXISTS (SELECT 1
              FROM UNNEST(some_numbers)
              WHERE b > 3);
```

<img width="150" alt="1-3" src="https://user-images.githubusercontent.com/53929665/96666288-c3bc1100-1391-11eb-98e9-d1ff21ef8ff4.PNG">

---
### [ 2. 배열과 집계 ]
- `ARRAY_AGG()`
	- 값들을 배열로 집계할 수 있다.
	- 집계 순서는 임의로 지정된다.
	- BigQuery UI에서는 하나의 행으로 묶는다는 개념으로 이해하면 쉽다.

```SQL
WITH fruits AS
  (SELECT "apple" AS fruit
   UNION ALL SELECT "pear" AS fruit
   UNION ALL SELECT "banana" AS fruit)
SELECT ARRAY_AGG(fruit) AS fruit_basket
FROM fruits;
```

<img width="250" alt="2" src="https://user-images.githubusercontent.com/53929665/96666289-c454a780-1391-11eb-8a6b-d21b3014c7b5.PNG">


- 이 함수는 값을 연결하는 순서를 보장하지 않는다.<br>따라서, 배열 요소를 정렬하려면 `ORDER BY`를 사용한다.
-  예를 들면 다음과 같다.

```SQL
WITH fruits AS
  (SELECT "apple" AS fruit
   UNION ALL SELECT "pear" AS fruit
   UNION ALL SELECT "banana" AS fruit)
SELECT ARRAY_AGG(fruit ORDER BY fruit) AS fruit_basket
FROM fruits;
```

<img width="150" alt="2-1" src="https://user-images.githubusercontent.com/53929665/96666290-c454a780-1391-11eb-9d29-77bdbb4f814c.PNG">

- 또한, 배열 요소에 `SUM()`과 같은 집계 함수를 적용할 수 있다.
- 예를 들면 다음 쿼리는 `squences`테이블의 각 행에 대한 배열 요소의 합계를 반환한다.

```SQL
WITH sequences AS
  (SELECT [0, 1, 1, 2, 3, 5] AS some_numbers
   UNION ALL SELECT [2, 4, 8, 16, 32] AS some_numbers
   UNION ALL SELECT [5, 10] AS some_numbers)
SELECT some_numbers,
  (SELECT SUM(x)
   FROM UNNEST(s.some_numbers) x) AS sums
FROM sequences s;
```

<img width="150" alt="2-2" src="https://user-images.githubusercontent.com/53929665/96666291-c4ed3e00-1391-11eb-827e-d7cafb93059d.PNG">

- `ARRAY_AGG()`가 <U>한 개의 값이 저장된 단일 행</u>들을 <u>하나로 배열로 묶어</u>주는 반면에,
- <U>배열로 이루어진 다수의 단일 행</U>들을 <U>하나의 배열로 묶어</u>주는 `ARRAY_CONCAT_AGG()`도 있다.

```SQL
WITH aggregate_example AS
  (SELECT [1,2] AS numbers
   UNION ALL SELECT [3,4] AS numbers
   UNION ALL SELECT [5, 6] AS numbers)
SELECT ARRAY_CONCAT_AGG(numbers) AS count_to_six_agg
FROM aggregate_example;
```

<img width="250" alt="2-3" src="https://user-images.githubusercontent.com/53929665/96666292-c4ed3e00-1391-11eb-9f3e-b38539734c63.PNG">
