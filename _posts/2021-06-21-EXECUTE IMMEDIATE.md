---
layout: post
title:  "[BigQuery] EXECUTE IMMEDIATE"
subtitle: "[BigQuery] EXECUTE IMMEDIATE"
categories: gcp
tags: bigquery
comments: true
---

####  Contents
- [EXECUTE IMMEDIATE](#execute-immediate)
	- [기본형식](#기본형식)
	- [예제](#예제)

본 포스팅은 Bigquery 공식문서의 `EXECUTE IMMEDIATE`에 관한 내용을 정리한 내용입니다.

<br>

---

## EXECUTE IMMEDIATE

`EXECUTE IMMEDIATE`절은 **Dynamic SQL**을 수행하기 위해 사용되는 방법 중 하나이다. 여기서 Dynamic SQL이란 간단하게 String 타입의 변수를 Script내에서 사용하는 SQL을 의미한다. 이를 이용하면 BigQuery SQL내에서 `DECLARE`로 선언한 변수 통해 Python이나 C++에서 변수를 사용하듯이 SQL문을 동적으로 활용할 수 있다. 

<br>

### 기본형식

```sql
EXECUTE IMMEDIATE sql_expression  [ INTO variable[, ...] ] [ USING identifier[, ...] ];

sql_expression:
  { "query_statement" | expression("query_statement") }

identifier:
  { variable | value } [ AS alias ]
```

- `sql_expression`
    - Query문(단일 DDL 혹은 단일 DML)을 나타낸다.
    - 작성한 Query문을 문자열 형태로 만들기 위해서  스크립트 양 끝에 `"` 혹은 `"""`을 추가해야한다. 예를 들자면 다음과 같다.
        - """SELECT * FROM my_table"""
        - "SELECT * FROM my_table"

<br>

- `expression`
    - **함수**, **조건식** 또는 표현식 하위 쿼리가 될 수 있다. 주로 문자열과 관련된 함수(ex. FORMAT, CAST, ... )가 자주 사용되어진다.
    - 좀 더 구체적인 예를 들자면 다음과 같다.
        - CAST("query_statement1", "query_statement2")
        - FORMAT("query_statement1", ... )
        - IF(조건, "query_statement1", "query_statement2")

<br>

- `INTO`절
    - sql_expression이 수행된 뒤, `INTO`절을 이용하여 쿼리에 대한 결과를 변수 한 개 이상에 저장할 수 있다.
    - INTO절에서 저장하기 위한 변수는 반드시 `DECLARE` 로 지정해줘야만 한다.

<br>

- `USING`절
    - sql_expression이 수행되기 전에, `USING`절을 이용하여 한 개 이상의 identifier을 sql_expression으로 전달할 수 있다. identifier는 변수 혹은 값이 될 수 있다. (참고로 `INTO`절에 사용되어진 변수를 `USING`절에서 사용할 수 있다.)
    - identifier를 참조하기 위해서 다음의 자리표시자(placeholder)들이 이용된다.
        - `?` : 식별자의 Index번호를 이용해 sql_expression내의 identifier의 위치를 구분하는 placeholder이다.
        - `@identifier`  :  별칭된 identifier의 name을 이용하여 식별자 위치를 구분하는 placeholder이다. (매개변수와 비슷한 기능을 한다.)


<br>

- `INTO`절과 `USING`절은  공식 문서에서 제시한 아래의 예시를 보면 확실하게 이해가 된다.
	
	```sql
   EXECUTE IMMEDIATE "SELECT ? * (? + 2)" INTO y USING 1, 3;
   ```

	 - 첫번째 `?`에는 1이 두번째 `?`에는 3이 전달된다.
	 - 그리고 쿼리 표현식의 최종 결과값인 5는 y에 저장된다.

	<br>

	```sql
	EXECUTE IMMEDIATE "SELECT @a * (@b + 2)" INTO y USING 1 as a, 3 as b;
	```

	- identifier 1과 3에는 각각 a와 b라는 name이 별칭되었다.
	- 각 a와 b에 대하여 쿼리표현식 `@a`와 `@b`에는 각각 1과 3이 전달된다.
	- 그리고 쿼리 표현식의 최종 결과값인 5는 y에 저장된다.

<br>

- 🔔 주의점
    - `EXECUTE IMMEDIATE` 내에서  `EXECUTE IMMEDIATE` 를 또 사용할 수 없다. 즉 중첩되게끔 사용할 수 없다.
    - 쿼리의 결과로 행이 0개가 반환되면 `INTO`절에는 `NULL`이 저장된다.

<br>

---

### 예제

BigQuery 공식문서의 예제를 확인해보자. 해당 예시의 경우 `EXECUTE IMMEDIATE`를 다양한 방법으로 이용할 수 있는 방법을 보여주는 좋은 예시라 생각한다👍.

- 먼저 `INTO`절에서 값을 저장할 변수들을 생성한다.
    - book_name변수는 문자열 타입 Ulysses을 저장한다.
    - book_year는 정수형 타입 1922 값을 저장한다.
    - first_date는 정수형 타입 변수로 선언만 되었다.

	```sql
	DECLARE book_name STRING DEFAULT 'Ulysses';
	DECLARE book_year INT64 DEFAULT 1922;
	DECLARE first_date INT64;
	```

<br>

####  (DDL : CREATE)문을 이용하여 테이블 생성하기

```sql
EXECUTE IMMEDIATE "CREATE TABLE [데이터셋].Books (title STRING, publish_date INT64)";
```

![Untitled](https://user-images.githubusercontent.com/53929665/122757484-a6e62d80-d2d2-11eb-8d55-c4c0770857f9.png)

<br>

####  (DML : INSERT)문을 이용하여 테이블에 레코드 추가하기

```sql
EXECUTE IMMEDIATE 
"INSERT INTO [데이터셋].Books (title, publish_date) VALUES('Hamlet', 1599)";
```

![Untitled 1](https://user-images.githubusercontent.com/53929665/122757470-a483d380-d2d2-11eb-8a88-d370cc7fab85.png)

<br>

####  USING절의 `?` placeholder를 이용하여 레코드 추가하기

`USING`절에 값이 아닌 `DECLARE`에서 값을 지정한 **변수**를 사용하는 것을 확인할 수 있다. 먼저, 

첫번째 `?`placeholder에는 변수 book_name에 저장된 "Ulysses"가 전달되는 것을 추가된 행의 첫번쨰 열을 통해 확인할 수 있으며,  두번쨰 `?`placeholder에는 변수 book_year에 저장된 "1922"가 전달되는 것을 마찬가지로 추가된 행의 두번째 열을 통해서 확인할 수 있다. 

```sql
EXECUTE IMMEDIATE
"INSERT INTO TEST_DATASET.Books (title, publish_date) VALUES(?, ?)"
USING book_name, book_year;
```

![Untitled 2](https://user-images.githubusercontent.com/53929665/122757476-a5b50080-d2d2-11eb-964e-4d0eb836eb72.png)

![Untitled 3](https://user-images.githubusercontent.com/53929665/122757478-a5b50080-d2d2-11eb-9806-c3f61e13c9f1.png)

<br>

####  USING절의 `@identifier` placeholder를 이용하여 레코드 추가하기

`USING`절의 각 identifier에 이름을 지칭한 것을 확인할 수 있다. 먼저, `@name` 에는 "Emma"가 전달되는 것을 확인할 수 있으며, `@year`에는 "1815"가 전달되는 것을 테이블의 추가된 행을 통해 알 수 있다. (USING절의 identifier순서와 query_expression내의 `@identifier`의 순서가 반대인 것도 포인트이다 ㅋ.ㅋ)

```sql
EXECUTE IMMEDIATE
"INSERT INTO TEST_DATASET.Books (title, publish_date) VALUES(@name, @year)"
USING 1815 as year, "Emma" as name;
```

![Untitled 4](https://user-images.githubusercontent.com/53929665/122757480-a64d9700-d2d2-11eb-81b6-247c8135b9bd.png)


![Untitled 5](https://user-images.githubusercontent.com/53929665/122757482-a64d9700-d2d2-11eb-8074-d1e8822b3939.png)

<br>

####  expression("query_statement") 예시

 expression을 어떻게 사용하느냐에 따라 방법이 무궁무진할 것 같다... 특히 `EXECUTE IMMEDIATE FORMAT`의 경우는 이번에 출시된 `PIVOT`과의 조합([참조](https://towardsdatascience.com/how-to-use-dynamic-sql-in-bigquery-8c04dcc0f0de))했을 때 감탄만 나왔다🙊.  (*`EXECUTE IMMEDIATE FORMAT`와 같이 사용했을 때 유용한 함수들에 관하여 알아낼 경우 지속적으로 업데이트할 예정이다.)

   - `CONCAT`

	  ```sql
	  EXECUTE IMMEDIATE CONCAT(
	  "INSERT INTO Books (title, publish_date)", 
	  "VALUES('Middlemarch', 1871)");
	  ```

   - `FORMAT`

	  ```sql
	  EXECUTE IMMEDIATE FORMAT(
	  "
	  INSERT INTO TEST_DATASET.Books (title, publish_date) 
	  VALUES('%s', 1871)
	  ", "Middlemarch") ;
	  ```

   - `IF`

	  ```sql
	  DECLARE x DEFAULT 1;
	  EXECUTE IMMEDIATE 
	  IF( x = 1, 
	      "INSERT INTO TEST_DATASET.Books (title, publish_date) VALUES('Hamlet', 1599)", 
	      "INSERT INTO TEST_DATASET.Books (title, publish_date) VALUES('Emma', 1815)") ;
	  ```

<br>

---

### References

- [Google Colud Bigquery Document : scripting](https://cloud.google.com/bigquery/docs/reference/standard-sql/scripting#execute_immediate)
- [GCP Blog : Smile with new user-friendly SQL capabilities in BigQuery](https://cloud.google.com/blog/topics/developers-practitioners/smile-new-user-friendly-sql-capabilities-bigquery)
- [Lak Lakshmanan : How to use Dynamic SQL in BigQuery](https://towardsdatascience.com/how-to-use-dynamic-sql-in-bigquery-8c04dcc0f0de)

