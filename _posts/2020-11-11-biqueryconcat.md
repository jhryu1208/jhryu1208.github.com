---
layout: post
title:  "[BigQuery] CONCAT 함수를 활용한 IS NOT NULL 반복 제거"
subtitle: "[BigQuery] CONCAT 함수를 활용한 IS NOT NULL 반복 제거"
categories: gcp
tags: bigquery
comments: true
---
#### BigQuery `CONCAT` 함수 팁!
- 본 포스팅은 BigQuery의 `CONCAT`함수를 사용한 간단한 팁에 관하여 다룹니다.
- IS NOT NULL 반복 최소화하는 법!

---

`SELECT` 절을 사용할 때,<br> 다룰 데이터와 테이블이 많아지면서 혹시나 테이블 내부에 있는 `NULL` 값을 가진 행을 제외하기 위해 나는 다음과 같이 필드마다 일일이 `IS NOT NULL`을 선언하였다. 

```sql
WHERE A_field IS NOT NULL
AND B-field IS NOT NULL
AND C-field IS NOT NULL
AND D-field IS NOT NULL
AND E-field IS NOT NULL
AND F-field IS NOT NULL
...
```

<br>

5개 미만까지는 단순히 반복하여 사용했지만 일정 수 이상이되면서 쿼리가 미관상 보기도 안좋고 복잡하며,  `IS NOT NULL`의 반복으로 쿼리 행을  쓸때없이 많이 소모하기 때문에 나중에 쿼리를 다시 들여다 봤을 때, 이리 저리 쿼리를 둘러보기 바빴다.

<br>

하지만, 위와 같은 상황은 `CONCAT` 함수하나로 정리되었다.
PostgreSQL과 달리 MySQL과 BigQuery에서는 다음과 같이 `CONCAT` 함수 내부 value로 `NULL`이 하나라도 있으면 `NULL`값을 반환한다. 즉,  `TRUE`와 `NULL`을 관계논리연산자 `AND` 로 연결하였을 때 출력 값으로 `NULL`이 나오는 것과 같은 원리이다. ( TRUE & NULL = NULL )

```sql
CONCAT('foo', 'bar', NULL) = NULL
```

<br>

이를 이용하여 위에서 다중으로 반복 사용된 `IS NOT NULL`을 다음과 같이 한 번에 축약할 수 있다.

```sql
WHERE CONCAT(A_field, B-field, C-field, D-field, E-field, F-field) IS NOT NULL
```





---

### References
- https://makandracards.com/makandra/825-mysql-concat-with-null-fields

