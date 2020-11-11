---
layout: post
title:  "[BigQuery] COUNTIF 함수"
subtitle: "[BigQuery] COUNTIF 함수"
categories: gcp
tags: bigquery
comments: true
---
#### BigQuery `COUNTIF`
- 본 포스팅은 BigQuery의 `COUNTIF`함수에 관한 정보를  다룹니다.
- NULL정보 손쉽게 카운팅하기

---

#### <u> COUNTIF </u>

이번에 NULL값 데이터의 갯수만 추출하려는 상황이 있었다. 나는 기존에 이런 경우 `COUNT`와 `IF`를 따로따로 사용하여 `COUNT(IF(어쩌구... `이런식으로 쿼리를 짜왔다. 하지만, 이번 상황에서는 안통하길래 따로 관련 함수가 있나 찾아봤는데 역시나 빅쿼리 공식문서에 확실히 작성되어 있었다.

<br>

`COUNTIF` 함수의 기본 형태는 다음과 같다.

``` SQL
COUNTIF(expression)  [OVER (...)]
```

<br>

해당 함수는 기본적으로  `expression`의 `TRUE` 값의 개수를 반환한다.<br>입력 행이 없거나 `expression`의 값이 `FALSE`또는 `NULL` 일 경우 0을 반환한다.

<br>

`NULL` 값에 해당하는 경우 0을 반환한다지만, 다음과 같이 사용할 경우 NULL의 갯수를 손쉽게 구할 수 있다.

```sql
COUNTIF( [object] IS NULL )
```

<br>

---

### References

- https://cloud.google.com/bigquery/docs/reference/standard-sql/aggregate_functions?hl=ko#countif

