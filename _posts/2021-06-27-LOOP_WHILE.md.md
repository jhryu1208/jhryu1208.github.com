---
layout: post
title:  "[BigQuery] LOOP & WHILE"
subtitle: "[BigQuery] LOOP & WHILE"
categories: gcp
tags: bigquery
comments: true
---



####  Contents
- [LOOP](#loop)
	- 기본형식
	- 예제
- [WHILE](#while)
	- 기본형식
	- 예제

본 포스팅은 Bigquery 공식문서의 `LOOP`와 `WHILE`에  관한 내용을 정리한 내용입니다.

<br>

---

## <span style="color:blue">LOOP</span>

`LOOP`는 쉽게 생각하면 다른 프로그래밍 언어에서 사용할 수 있는 반복문이다. 반복문을 이용하면 특정 변수의 다양한 값에 대한 여러 결과를 검토할 수 있기 때문에 데이터 분석 쿼리에서 유용하다.  물론 **변수**를 이용하기 때문에 여기서도 `DECLARE`를 통해 변수를 선언해주는 우선과정이 필요하다. 더불어, `LOOP`외에도 동일 반복문인  `WHILE`을 사용한 반복문도 존재하며 해당 포스팅에서 같이 확인해볼 예정이다. 

하지만, 간단한 숫자 배열 혹은 등차 수열을 가진 배열을 만들기 위해서 LOOP혹은 WHILE을  사용할 필요는 없다. 이러한 간단한 배열들은  GENERATE_ARRAY 등으로 구성하는 것이 더 간단하다😉. 피보나치 수열이나 등비수열 등과 같이 등차적으로 표현하기 힘들 경우 사용하도록 하자!! 🐱‍🏍

<br>

### <span style="color:blue">기본형식</span>

```sql
LOOP
  sql_statement_list
END LOOP;
```

- `LOOP ~ END LOOP`
    - `BREAK` 또는 `LEAVE`문이 루프를 종료시킬 때까지  `LOOP`문 내부에 속해있는 `sql_statement_list`를 실행한다.

- `sql_statement_list`
    - 세미콜론(;)으로 끝나는 SQL 블럭 하나 혹은 그 이상의 SQL 블럭 리스트들을 의미한다.

<br>

### <span style="color:blue">예제</span>

#### `LEAVE` 와 `IF ~ THEN`을 이용한 조건을 갖는 `LOOP`

아래의 쿼리는 [GCP공식문서](https://cloud.google.com/bigquery/docs/reference/standard-sql/scripting#loop)에서 참조된 예제 쿼리이다.  차례대로 살펴보자.

- (1)에서는 `DECLARE`절을 통해 정수형 변수 x를 선언하였으며 0으로 초기화하였다.
- (2)`LOOP`문 안에서는 (3)`SET`을 통해 변수 x를 x+1로 초기화 시켰다. 즉, x에는 0이 먼저 전달되었기 때문에 x는 1로 초기화 되었다.
- 다음으로, (4)`IF~THEN`문에서 현재의 다시 초기화된 x가 10보다 큰지 작은지 판단한다.
    - x가 10이상이라면 조건이 **TRUE**이기 때문에 `LEAVE`문을 통해 `LOOP`를 중단시킬 것이다.
    - 하지만 지금의 x는 1이므로 **FALSE**이기 때문에 `LOOP` 문은 멈추지 않고 반복적으로 진행된다.
- 반복적으로 진행되어, x = 10으로 초기화되었을 경우 `LOOP`는 `LEAVE`문에 의해 (2-2)반복을 멈추고 최종 결과를 (5) `SELECT` 절로 넘겨준다.
- 따라서, 최종적인 결과로 10이 나오는 것을 확인할 수 있다.

```sql
DECLARE x INT64 DEFAULT 0; -- (1)
LOOP                       -- (2-1) LOOP시작
  SET x = x + 1;           -- (3)
  IF x >= 10 THEN LEAVE;   -- (4-1) IF시작
  END IF;                  -- (4-2) IF종료
END LOOP;                  -- (2-2) LOOP종료
SELECT x;                  -- (5)
```

![Untitled 1](https://user-images.githubusercontent.com/53929665/123545782-e4483080-d794-11eb-9f71-048abef7e59d.png)

<br>

#### 배열을 이용해서 `LOOP`문의 결과를 행으로 차례대로 반환하기

다음은 `LOOP`로 인해 순차적으로 발생한 변수 값들을 `ARRAY`에 저장해서 `UNNEST`로 전개하는 예제이다.  

- 먼저 (1-1)정수 타입의 변수 x를 선언 및 초기화하였으며, (1-2)정수 타입 배열 변수인 y를 선언하였다. 이 y변수에 순차적으로 발생하는 변수 값들을 저장할 계획이다.
- (2-1) `LOOP`문에서도 위에서 확인한 예제와 동일하게 (3-1)`IF`문이 있는 것을 확인할 수 있다. 해당 `IF` 문은 다음을 의미한다.
    - 만약 변수 x가 10이상의 값을 가지고 있으면, `LOOP`문을 스탑한다.
    - 만약 변수 x가 10미만의 값을 가지고 있으면, 변수 y를  `ARRAY_CONCAT(y, [x])`로 초기화한다.더불어, 변수 x에 1을 더해서 다시 초기화한다.
- 첫 시도에서는 x≥10 조건을 만족하지 않았기 때문에, `LOOP`문이 다시 반복된다.
- 반복적으로 진행되어, x = 10으로 초기화되었을 경우 `LOOP`는 `BREAK`문에 의해 (2-2)반복을 멈추고 변수 y의 최종결과를 (5)UNNEST시켜서 행을 출력한다.

하지만, 이러한 예제는 위에서도 언급했지만 `GENERATE_ARRAY`를 쓰는 것이 더 효율적이다!

```sql
DECLARE x INT64 DEFAULT 0;              -- (1-1)
DECLARE y ARRAY<INT64>;                 -- (1-2)

LOOP                                    -- (2-1) LOOP시작
  IF x >= 10 THEN BREAK;                -- (3-1) IF시작
  ELSE                                  -- (3-2)  
     SET y = ARRAY_CONCAT(y, [x]);      
     SET x = x + 1;
  END IF;                               -- (3-3) IF종료
END LOOP;                               -- (2-2) LOOP종료

SELECT result                           -- (5)
FROM UNNEST(y) AS result                
```


![Untitled 2](https://user-images.githubusercontent.com/53929665/123545784-e5795d80-d794-11eb-9dab-46de2dbc81fb.png)

`LOOP`의 경우 쿼리가 무한루프에 빠지지 않도록하기 위해서 `IF`를 별개로 추가하여 무한루프를 방지하였다. 이게 또 은근히 번거로울 수 있다. 이를 위해 `LOOP`와 `IF`를 합친 `WHILE`문도 존재한다(ㅇㅇ ㄷㄷㄷㅈ!!). 

<br>

---

## <span style="color:blue">WHILE</span>

`WHILE`은 앞에서 언급했듯이 `LOOP` + `IF`이다. 이를 제외한 `WHILE`에 관한 내용은 `LOOP`와 비슷하다. 그러면 긴 말없이 바로 기본 형식부터 알아보자.🏃‍♂️🏃‍♂️

<br>

### <span style="color:blue">기본형식</span>

```sql
WHILE boolean_expression DO
  sql_statement_list
END WHILE;
```

- `boolean_expression`
    - 이 표현식의 위치는 조건을 서술하는 자리이다.
    - boolean_expression이 **TRUE**인 동안 sql_statement_list이 계속 반복적으로 실행된다.
    - 즉, sql_statement_list가 실행되고 다음으로 다시 boolean_expression이 참인지 거짓인지 평가되고, 거짓이면 다시 sql_statement_list가 실행되고 다음으로 다시 boolean_expression이 참인지 거짓인지 평가되고, ...🤪, 거짓이면 다시 sql_statement_list가 실행되고 다음으로 다시 boolean_expression이 참인지 거짓인지 평가되고, ...  가 반복되는 것이다.
    - boolean_expression이 **FALSE**가 되면 반복은 종료된다.

<br>

### <span style="color:blue">예제</span>

다음 쿼리는 `WHILE`을 이용해서 games_post_wide테이블을 10번 출력하는 쿼리이다. `LOOP`와 비교했을 때 유일하게 다른 점은 역시 boolean_expression이 포함된다는 점이다. 아래에서 boolean_expression은 x < 10이며, 이 조건을 불만족(즉, FALSE)할 때까지 반복 처리한다.

```sql
DECLARE x INT64 DEFAULT 0;

WHILE x < 10 DO
    SELECT * FROM `bigquery-public-data.baseball.games_post_wide`;
    SET x = x + 1;
END WHILE
```


![Untitled](https://user-images.githubusercontent.com/53929665/123545786-e611f400-d794-11eb-90fa-11d7173b22e3.png)


<br>

---

### References

- [Google Colud Bigquery Document : scripting](https://cloud.google.com/bigquery/docs/reference/standard-sql/scripting#loop)
- [Bence Komarniczky : Loops in BigQuery](https://towardsdatascience.com/loops-in-bigquery-db137e128d2d)
