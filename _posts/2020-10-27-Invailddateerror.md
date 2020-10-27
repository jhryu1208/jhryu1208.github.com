---
layout: post
title:  "[BigQuery-ERROR] Invalid date"
subtitle: "[BigQuery-ERROR] Invalid date"
categories: gcp
tags: bigquery
comments: true
---
#### [ BigQuery - ERROR ] 
#### Error for "Invalid date"
----

다음과 같이 에러가 발생했을 경우, <U>자신이 짠 쿼리에관한 자료형을 면밀히 살펴볼 필요가있다.</u>

<img width="300" alt="1" src="https://user-images.githubusercontent.com/53929665/97304657-621f0980-189f-11eb-9760-d4a4681b561f.PNG">

나의 경우 아무생각없이 빅쿼리의 주의 문구 설명대로 자료형을 변환하다보니 위와 같은 오류가 발생하였다.
```SQL
SELECT	DATE_DIFF( CAST(FORMAT_DATE('%Y/%m/%d', CURRENT_DATE()) AS DATE), 
        CAST(FORMAT_DATE('%Y/%m/%d', CAST([타임스탬프 자료형] AS DATE)) AS DATE), DAY) AS DATE_DIFF
```

<U>우선 에러가 발생한 원인은  `NUMERIC( ex.날짜형자료형 )`필드의 데이터를 `STRING`으로 CAST를 진행했기 때문이다.</U>

위의  SELECT절을 예로 들어 설명하면 다음과 같다.
DATE_DIFF함수를 이용하여 날짜간의 차이를 구하기 위해, TIMESTAMP의 필드를`DATE`로 CAST시키고 포맷변환을 위해 `FORMAT_DATE`함수를 사용하였다.
```SQL
FORMAT_DATE('%Y/%m/%d', CAST([타임스탬프 자료형]  AS  DATE))
```
CAST된 TIMESTAMP자료형이 `DATE`자료형으로 바뀌고 `FORMAT_DATE`를 통해 `STRING`자료형으로 변환되었다.
즉 <U>DATE -> STRING</U>의 과정을 거친 것이다.

나 같은 경우는 내가 사용할 쿼리의 자료형을 다시 파악하고,<br> `NUMERIC`데이터에서 `STRING`데이터로 변환하지않고 수행하였다.<br>(신경써서 쿼리를 작성하니 더 간결해지고 보기 깔끔해졌다...)
```SQL
DATE_DIFF( CURRENT_DATE(), CAST(date_joined AS DATE), DAY)
```

항상 자료형을  CAST할 때는 꼼꼼히 신경쓰는 습관을 가지자.

---
#### Index
- https://kb.tableau.com/articles/issue/Error-Invalid-date-When-Connecting-to-BigQuery-Using-Custom-SQL

