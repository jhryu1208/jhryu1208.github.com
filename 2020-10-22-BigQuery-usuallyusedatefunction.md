---
layout: post
title:  "[BigQuery] 자주 사용하는 자료형 변환 함수 정리"
subtitle: "[BigQuery] 자주 사용하는 자료형 변환 함수 정리"
categories: gcp
tags: bigquery
comments: true
---
#### BigQuery  자주 사용하는 자료형 변환 함수 정리
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

---
### [ 2. PARSE_(DataType) 함수 ]

```SQL
PARSE_[DATE/DATETIME/TIMESTAMP]( [format_string], [DATE/DATETIME/TIMESTAMP_string] )
```

- DATE/DATETIME/TIMESTAMP의 `STRING` 표현을 각각 ☑`DATE/DATETIME/TIMESTAMP`객체로 반환한다.
- <u>[DATE/DATETIME/TIMESTAMP_string]의 각 요소는 [format_string]에 해당하는 요소여야 한다.</u>

---
### [ 3. TIMESTAMP_MICROS 함수 ]

```SQL
TIMESTAMP_MICROS(int64_expression)
```

- `int64-expression`( ex. 1230219000000000 )을 마이크로초 수로 해석하여 `TIMESTAMP`형태로 반환한다. <br>ex) 1230219000000000 => 2008-12-25 15:30:00 UTC

