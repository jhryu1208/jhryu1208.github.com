---
layout: post
title:  "[BigQuery] DECLARE/SET"
subtitle: "[BigQuery] DECLARE/SET"
categories: gcp
tags: bigquery
comments: true
---
####  Contents
- [DECLARE](#declare)
	- 기본형식
	- 예제
- [SET](#set)
	- 기본형식
	- 예제

본 포스팅은 Bigquery 공식문서의  `DECLARE`과 `SET`에 관한 내용을 정리한 내용입니다.

<br>

---

## DECLARE

`DECLARE`은 말 그대로 특정 유형의 <u>변수를 선언 혹은 초기화</u>할 때 사용한다. (파이썬의 변수선언과 유사한 것 같다.)

<br>

### 기본형식

```sql
DECLARE variable_name[, ...] [variable_type] [DEFAULT expression];
```

- `DECLARE` 문은 다음의 위치에서만 사용할 수 있다.
    - Query 스크립트 시작 부분
    - `BEGIN`으로 선언된 블록의 시작 부분

<br>

- `variable_name` : valid identifier
    - 대소문자를 구분하지 않는다.  (eg. name = Name = NAME)
    - 단 하나의 `DECLARE`문에 `variable_name`은 여러번 지정할 수 있다.

- `variable_type` : bigquery type
    - `DEFAULT`와 함께 expression을 지정해주면, `varibable_name`이라는 변수가 expression값으로 초기화된다. 이때, 변수는 지정한 expression의 Data type으로 강제변환된다.
    - 반대로, `DEFAULT`절을 지정해주지않으면, 생성 시켜준 객체가 `NULL`로 초기화된다.
    - `varibale_type` 과 expression의 경우 `variable_name`과 달리 여러번 나타낼 수 없다. 즉 단 하나만 지정할 수 있다.
    - `variable_type` 을 지정하지 않을 경우, 반드시 `DEFAULT` 절을 지정해줘야한다.

<br>

- 🔔 주의점
    - 파이썬과 다르게 이전 `DECLARE` 문에서 지정한 `varibale_name` 의 이름을 중복으로 사용할 수 없다.

<br>

### 예제

#### DEFAULT절을 생략했을 때

`DEFAULT`절을 **생략할 경우** 다음과 같이 지정한 변수가 NULL값으로 초기화한다.


![Untitled](https://user-images.githubusercontent.com/53929665/122646166-cd805900-d158-11eb-885f-2f39b053d952.png)

<br>

#### DEFAULT절을 지정했을 때

`DEFAULT` 절을 **지정할 경우** x, y, z 변수가 모두 0으로 초기화되는 것을 확인할 수 있다.

![Untitled 1](https://user-images.githubusercontent.com/53929665/122646159-cbb69580-d158-11eb-8dcb-9482dc1898c6.png)

<br>

#### variable_type을 지정하지 않을 때

`variable_type`을 지정하지 않을 수 있다. 하지만, 이 경우에는 반드시 `DEFAULT`절을 지정해야한다. 이때, 변수 x는 expression에서 지정한 값의 data type으로 강제변환(coerce)된다. 즉, 아래의 예제에서는 변수 x는 DATE type으로 강제변환된 것을 확인할 수 있다.


![Untitled 2](https://user-images.githubusercontent.com/53929665/122646161-cc4f2c00-d158-11eb-84f6-3d09d2f29973.png)

따라서, varible_type을 expression의 Data type과 다르게 지정할 경우 아래와 같이 오류메시지를 확인할 수 있다.

![Untitled 3](https://user-images.githubusercontent.com/53929665/122646162-cc4f2c00-d158-11eb-8bef-3e61382d5596.png)

![Untitled 4](https://user-images.githubusercontent.com/53929665/122646163-cce7c280-d158-11eb-8b40-5f4173835e26.png)

<br>

#### 쿼리의 결과로  변수 초기화!

쿼리의 결과를 expression으로 지정 할 수 있다.(*expression의 특성 상 오직 하나의 값만 사용할 수 있어서 아래의 예제에서 볼 수 있듯이 LIMIT절을 추가하였다).  특정 목적을 가지는 쿼리의 경우는 이와 같은 방법을 유용하게 이용할 수 있을 것 같다. 예를들어 테이블의 특정 target의 고유 레코드의 필드 값 중 실시간으로 변동하는 값을 변수로 이용하는 경우 라던가... 🤔 🤔  🤔


![Untitled 5](https://user-images.githubusercontent.com/53929665/122646165-cce7c280-d158-11eb-8e6b-1ad198473c8d.png)

<br>

## SET

`SET`은 `DECLARE`과 같이 사용되어진다. `DECLARE`에서 변수의 type을 지정하고, `SET`을 이용하여 type이 지정된 변수에 값을 초기화시킬 수 있다. 쉽게 말하자면, C언어에서 변수를 선언할 때와 비슷하다(ex. int x= 1). 물론 위에서 확인할 수 있듯이 `DECLARE`에서 이 과정을 모두 수행할 수 있다. 하지만, `SET`은 expression에 따라 <u>변수를 동시에 설정</u>할 수 있으며, <u>Query스크립트 내 어디에서나 사용할 수 있다는 장점</u>을 가지고 있다!

<br>

### 기본형식

```sql
SET name = expression;
```

```sql
SET (variable_name_1, variable_name_2, …, variable_name_n) =
  (expression_1, expression_2, …, expression_n);
```

<br>

### 예제

#### 변수 초기화
x라는 이름을 가진 변수에 다음과 같이 지정할 수 있다.

![Untitled](https://user-images.githubusercontent.com/53929665/122646878-6c5a8480-d15c-11eb-8cd4-aec8de4eb94e.png)

<br>

####  다변수 초기화
   
변수를 다음과 같이 동시 선언 및 초기화할 수 있다. 하지만, <u>변수의 경우 새로 지정할 때마다 반드시 </u>`DECLARE`<u>절로 선언 해주는 것을 잊지한다!</u>


![Untitled 1](https://user-images.githubusercontent.com/53929665/122646881-6cf31b00-d15c-11eb-83e2-1ac6665be704.png)

<br>

---

### References

- [Google Colud Bigquery  Document  : scripting](https://cloud.google.com/bigquery/docs/reference/standard-sql/scripting)

