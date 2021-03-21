---
layout: post
title:  "[BigQuery] 자주 사용하는 자료형 변환 함수 정리"
subtitle: "[BigQuery] 자주 사용하는 자료형 변환 함수 정리"
categories: gcp
tags: bigquery
comments: true
---
- 본 포스팅은 BigQuery사용 중 자주 이용하고 헷갈려하는 변환 함수들에 관하여 정리한 글입니다.
- GCP 공식 가이드를 기반으로 작성됩니다.
- 지속적으로 기록될 예정입니다.

---
### [ 1. FORMAT_(DataType) 함수 ]

```SQL
FORMAT_[DATE/DATETIME/TIMESTAMP]( [format_string], [DATE/DATETIME/TIMESTAMP_expr] )
```
- 지정된 `format_string`에 따라 타임스탬프의 형식을 지정한다.<br>ex1) 20201021 DATE 자료형 => 2020-10-21` STRING` 자료형<br>ex2) 20201021 00:00:00 DATETIME 자료형 => 2020-10-21 `STRING` 자료형<br>ex3)2020-10-21 00:00:00 UTC TIMESTAMP 자료형 => 2020-10-21 `STRING` 자료형
- 반환되는 데이터 유형은 ☑`STRING`이다.
	- FORMAT_(DataType)함수는 데이터의 포맷을 자유롭게 변경할 수 있는 장점을 가지고 있지만, 복잡한 쿼리에서는 날짜데이터에서 STRING으로 변경된 데이터 필드를 다시 날짜데이터로 바꾸는 번거로움을 경험할 수 있다. 
	- 따라서, 쿼리가 단순하지 않을 것이라 예상되면 다음의 쿼리를 대신 사용할 것을 추천한다.
	
	```SQL
	EXTRACT(DATE FROM 날짜컬럼)
	```

- zone 적용하기

기존 UTC형식으로 표기된 날짜를 KST형식으로 바꾸고 날짜와 시간을 추출하기 위해서 나는 기존에는 항상 아래와 같이 수행했다. 아래처럼 사용하면 쿼리가 길어지고 처리시간이 길어질 뿐더러 가시성도 함수가 복잡해질수록 떨어진다는 단점이이있다.

```SQL
FORMAT_TIMESTAMP(‘%Y%m%d %T’, 
				TIMESTAMP_ADD([UTC로 표기된 expr], INTERVAL 9 HOUR))
```

<br>

하지만 FORMAT_TIMESTAMP함수를 아래와 같이 사용하면 위에서 했던거처럼 길게 함수를 적을 필요가 없다. (왜 지금까지 몰랐던걸까 ㅠ.ㅠ...) 

```SQL
FORMAT_TIMESTAMP(‘%Y%m%d %T’, 
				[UTC로 표기된 expr], ‘Asia/Seoul’[‘+9’로 해도됨])
```

<br>

---
### [ 2. PARSE_(DataType) 함수 ]

```SQL
PARSE_[DATE/DATETIME/TIMESTAMP]( [format_string], [DATE/DATETIME/TIMESTAMP_string] )
```

- DATE/DATETIME/TIMESTAMP의 `STRING` 표현을 각각 ☑`DATE/DATETIME/TIMESTAMP`객체로 반환한다.
- <u>[DATE/DATETIME/TIMESTAMP_string]의 각 요소는 [format_string]에 해당하는 요소여야 한다.</u>
- `FORMAT_(DataType)`과 마찬가지로 Parse함수도 zone을 이용하여 시각을 바꿀수 있다.

```SQL
# ex)
SELECT 
	PARSE_TIMESTAMP(‘%Y-%m-%d’, UTC_ts_expr, ‘Asia/Seoul’) —— timestamp객체가 KST시각으로 변환됨
```

---
### [ 3. TIMESTAMP_MICROS 함수 ]

```SQL
TIMESTAMP_MICROS(int64_expression)
```

- `int64-expression`( ex. 1230219000000000 )을 마이크로초 수로 해석하여 `TIMESTAMP`형태로 반환한다. <br>ex) 1230219000000000 => 2008-12-25 15:30:00 UTC

