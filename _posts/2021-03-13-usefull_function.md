---
layout: post
title:  "[BigQuery] 알아두면 좋은 숫자형/문자형 함수"
subtitle: "[BigQuery] 알아두면 좋은 숫자형/문자형 함수"
categories: gcp
tags: bigquery
comments: true
---
- 본 포스팅은 BigQuery사용 중 이용하면 편리한  숫자형/문자형 함수들에 관하여 정리한 글입니다.
- 지속적으로 기록될 예정입니다.

<br>

- - -

### 1) 숫자형 함수 中

#### IEEE_DIVIDE 함수

`IEEE_DIVIDE`함수는 IEEE에서 설정한 표준 계산법을 따른다. 따라서,
- 0을 0으로 나누려고하면 `NaN`을 반환한다.
- 실수를 0으로 나누려고하면 `inf`를 반환한다.
- 일반적으로 가능한 연산에서는 `float`를 반환한다. (ex. 5.0)

```SQL
# ex
SELECT
	IEEE_DIVIDE(0, 0), -- NaN반환
	IEEE_DIVIDE(1, 0), -- Infinity 반환
	IEEE_DIVIDE(5, 1), -- 5.0 반환
```

`SAFE_DIVIDE`처럼 0으로 나눌때 NaN을 반환해서 편리할 수 있지만, 0이 아닌 수를 0으로 나눌때 Inf를 반환하기에 개인적으로는 평상시에는 잘 사용하지 않는다. 

<br>

#### SAFE 함수

스칼라 함수에 `SAFE`접두사를 사용하면 쿼리를 수행했을 때 오류가 발생하는 상황에서 NULL을 반환한다.

```SQL
# ex
SELECT 
	SAFE.LOG(10, -3) 
	-- 음수의 로그는 정의되지 않지만 NULL이 반환된다.
``` 

<br>

#### SAFE_DIVIDE
`SAFE_DIVIDE`는 분모가 0인 계산에서 NULL을 반환한다.

```SQL
# ex
SELECT 
	SAFE_DIVIDE(0, 0) -- NULL 반환
	SAFE_DIVIDE(10, 0) -- NULL 반환  
```

SAFE_DIVIDE는 개인적으로 정말 꾸준히 이용하는 함수중 하나이다. 특히 누적된 데이터가 많지 않은 상황에서의 비중 지표를 확인할 때 편리하다. (개인적으로는 `/`보다는 해당 함수를 애용하는 것을 추천한다.)


<br>

#### NUMERIC을 사용한 정밀 소수 계산

빅쿼리에서 `NUMERIC`데이터 타입은 숫자를 나타내는 38자리 수를 제공하며 그 숫자 중 9 자리는 소수점 아래의 숫자로 표시한다. 또한 16 바이트를 스토리지로 사용하므로 소수점 이하 자릿수를 정확하게 표현할 수 있어 재무 계산에 적합하다고한다. 따라서,  재무 계산에서 사용되는 테이블의 숫자의 경우 데이터 타입을 살펴보고 INT64 혹은 FLOAT64등으로 설정되어 있을 경우 NUMERIC으로 치환하여 사용하는 것을 추천한다.


- - -


### 2) 문자형 함수 中

#### STARTS/ENDS_WITH(value1, value2)

- `STARTS_WITH` : 두 번째 값(value2)이 첫 번째 값(value1)의 프리픽스이면 `TRUE`를 반환한다.

- `ENDS_WITH` : 두 번째 값(value2)이 첫 번째 값(value1)의 서픽스이면 `TRUE`를 반환한다.

```SQL
# ex
SELECT 
	STARTS_WITH(“Hello”, “o”) -- FALSE
	STARTS_WITH(“Hello”, “H”) -- TRUE  
	ENDS_WITH(“Hello”, “o”) -- TRUE
	ENDS_WITH(“Hello”, “H”) -- FALSE
```

<br>

####  STRPOS(value1, value2)
value1내부에서 value2의 인덱스(1부터 시작)값을 반환한다. 만약 value2를 찾을 수 없을 경우 0을 반환한다.

```SQL
# ex
SELECT 
	STRPOS(“Hello”, e) —— 2
	STRPOS(“Hello”, f) —— 0
```

<br>


#### 정규표현식


<br>

- - - 

### References

- [Bigquery 공식문서](https://cloud.google.com/bigquery/docs)
	- [정규표현식관련](https://support.google.com/a/answer/1371415?hl=ko)
	- [문자열관련](https://cloud.google.com/bigquery/docs/reference/standard-sql/string_functions?hl=ko)


- 『구글 빅쿼리 완벽 가이드』, 발리아파 락쉬마난, 조던 티가니 지음, 변성윤, 장현희 옮김, 책만, 9791189909239

