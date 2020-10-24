---
layout: post
title:  "[MySQL] PERCENT_RANK() 함수"
subtitle: "[MySQL] PERCENT_RANK() 함수"
categories: devlang
tags: (dbms)mysql
comments: true
---
#### [ MySQL ] PERCENT_RANK 함수
- 본 포스팅은 행의 percentile ranking을 구하기 위한 함수인 `PERCENT_RANK()` 함수에 관하여 설명합니다.
- mysqltutorial.org의 설명을 기반으로 작성했습니다.

---

### Introduction : PERCENT_RANK()

- 다음의  함수는 `PERCENT_RANK()` 함수의 기본 형태이다.

```SQL
PERCENT_RANK()
OVER (	PARTITION BY expr . . . 
		ORDER BY expr [ASC|DESC] . . . )
```

- `PERCENT_RANK()`
	- 0 부터 1까지의 값을 반환한다.
	-`( rank - 1 ) / ( total_rows - 1 )`을 계산한다.
		- 여기서 rank란 행에 매겨지는 특정 순위를 의미하고,
		- total_rows는 함수에 사용되는 모든 행의 수를 의미한다.
	- partition이나 result set안에서의 first row에 대해서는 항상 0을 반환한다.
	- 반복되는 값이라면 같은 값을 반환한다.
 
<br>

- `PARTITION BY`
	- 행을 파티션으로 분류할 때 사용한다.<br>( ex. 만약 TOOL컬럼이 PEN과 ERASER을 가지고 있다면,<br> PARTITION BY TOOL을 사용할 경우 ERASER와 독립적으로 PEN끼리 함수가 적용되고,<br> PEN과 독립적으로 ERASER값을 가진 행끼리 함수가 적용된다.)

<br>

- `ORDER BY`
	- 함수를 적용할 논리적인 순서를 결정할 때 사용한다.

---

### Example : PERCENT_RANK()

- [sample database](https://www.mysqltutorial.org/mysql-sample-database.aspx)로 부터 `productLineSales` 테이블을 생성하여 예제로 사용할 것이다.

```SQL
CREATE TABLE productLineSales
SELECT
    productLine,
    YEAR(orderDate) orderYear,
    quantityOrdered * priceEach orderValue
FROM
    orderDetails
        INNER JOIN
    orders USING (orderNumber)
        INNER JOIN
    products USING (productCode)
GROUP BY
    productLine ,
    YEAR(orderDate);
```

- 다음의 쿼리를 이용하여 모든 product line의 order value에 대한 percentile rank를 구할 수 있다.
	-  아래의 예제에서 `ROUND`함수를 사용하여 백분위를 2_decimal까지 표현한 센스까지 확인해 주자.

```MySQL
WITH t AS (
    SELECT productLine, SUM(orderValue) orderValue
    FROM
        productLineSales
    GROUP BY
        productLine
)
SELECT
    productLine, orderValue,
    ROUND(
       PERCENT_RANK() OVER (
          ORDER BY orderValue
       )
    ,2) percentile_rank
FROM
    t;
```

- 결과는 다음과 같다.
	- 백분위 0% 인 Trains는 order value가 모든 productline 중에서 최하위임을 확인할 수 있다.
	- Vintage Cars 다른 50%이하 기종들 보다  더 나은 수행능력을 보여주고 있음을 확인할 수 있다.
	- Classic Cars는 productline 중에서 최상위임을 확인할 수 있다.
	<br>
	<img width="350" alt="1" src="https://user-images.githubusercontent.com/53929665/97085613-7a9de280-1659-11eb-93a6-37514e19a858.PNG">

---

### Example : PERCENT_RANK() OVER the PARTITION

- 다음의 예제는 <u>연도 별로</u> order values의 percentile ranking을 계산하는 쿼리이다.

```SQL
SELECT productLine, orderYear, orderValue,
    ROUND(
    PERCENT_RANK()
    OVER (
        PARTITION BY orderYear
        ORDER BY orderValue
    ),2) percentile_rank
FROM
    productLineSales;
```

- 결과는 다음과 같다.
	- 가장 먼저 두드러지는 특징은 `PARTITION BY orderYear` 때문에  연도 별로 `PERCENT_RANK()` 함수가 적용되었다는 점이다. 아래의 결과와 같이 2003, 2004, 2005년이 별개로 그룹화되어 계산되었다.
	- 아래의 결과를 통해 2003년에 수행능력이 좋았던 productline과 2004년에 수행능력이 좋았던 productline 등을 비교할 수 있다는 장점이 있다.

<img width="350" alt="2" src="https://user-images.githubusercontent.com/53929665/97085819-b4bbb400-165a-11eb-8b9f-2f16ea056cbf.PNG">

- 또 다른 응용으로 `WHERE`절을 추가하여 <u>상위 n%의 데이터</u>도 구할 수 있다.

---

#### index
- MySQLTutorial : https://www.mysqltutorial.org/mysql-window-functions/mysql-percent_rank-function/

