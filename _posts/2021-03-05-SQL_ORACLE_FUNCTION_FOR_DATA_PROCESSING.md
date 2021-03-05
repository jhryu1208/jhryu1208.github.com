---
layout: post
title:  "[ORACLE] 4.Function for Data Processing"
subtitle: "[ORACLE] 4.Function for Data Processing"
categories: devlang
tags: (dbms)oracle
mathjax: true
comments: true
---
- 오라클 함수
- 문자 함수
- 숫자 함수
- 날짜 함수
- 형 변환 함수
- NULL처리 함수 
- DECODE 함수와 CASE문

---
### [ 1. 오라클 함수 ]
#### <u>내장 함수(buit-in function)</u>
- 오라클에서 기본으로 제공하는 함수


- <b>단일행 함수(single-row-function)</b>
    - 한 행당 결과가 하나씩 나오는 함수



- <b>다중행 함수(multiple-row function)</b>
    - 여러 행이 입력되어 하나의 행으로 결과가 반환되는 함수


#### <u>정의 함수(user-defined function)</u>
- 사용자가 필요에 의해 직접 정의한 함수

---
### [ 2. 문자 함수 ]

|함수|설명|
|:------:|:---|
|`UPPER(문자열)`|괄호 안 문자 데이터를 모두 대문자로 변환하여 반환|
|`LOWER(문자열)`|괄호 안 문자 데이터를 모두 소문자로 변환하여 반환|
|`INITCAP(문자열)`|괄호 안 문자 데이터 중 첫 글자는 대문자로, 나머지는 소문자로 변환 후 반환|

#### (1) UPPER, LOWER, INITCAP 함수 


```python
SELECT ENAME, UPPER(ENAME), LOWER(ENAME), INITCAP(ENAME)
FROM EMP;
```

결과 :
![2-1](https://user-images.githubusercontent.com/53929665/93022415-5267a080-f624-11ea-8832-2bfdf9fe6dd9.JPG)

#### (1-1) `UPPER` 함수로 문자열 비교하기(사원 이름이 SCOTT인 데이터 찾기)


```python
SELECT *
FROM EMP
WHERE UPPER(ENAME) = UPPER('scott');
```

결과 :
![2-1-1](https://user-images.githubusercontent.com/53929665/93022416-53003700-f624-11ea-8d3e-fecf2bd1bf9c.JPG)

#### (1-2) `UPPER`함수로 문자열 비교하기(사원 이름에 SCOTT단어를 포함한 데이터찾기)


```python
SELECT *
FROM EMP
WHERE UPPER(ENAME) LIKE UPPER('%scott%');
```

결과 :
![2-1-2](https://user-images.githubusercontent.com/53929665/93022417-5398cd80-f624-11ea-855a-9cfc2dbadcf4.JPG)


---
####  (2) LENGTH 함수 
- `LENGTH` : 특정 문자열의 길이를 구할 때 사용한다.

#### (2-1) `LENGTH`함수 사용하기
- 선택한 열의 문자열 길이 구하기


```python
SELECT ENAME, LENGTH(ENAME)
FROM EMP;
```

결과 :
![2-2-1](https://user-images.githubusercontent.com/53929665/93022418-5398cd80-f624-11ea-87b8-d6ec23286118.JPG)

#### (2-2)` WHERE`절에서 `LENGTH`함수 사용하기
- 사원 이름의 길이가 5 이상인 행 출력하기


```python
SELECT ENAME, LENGTH(ENAME)
FROM EMP
WHERE LENGTH(ENAME) >= 5;
```

결과:
![2-2-2](https://user-images.githubusercontent.com/53929665/93022419-54316400-f624-11ea-9d3c-c55c3b7bb3f7.JPG)

#### (2-3) `LENGTH` Vs `LENGTHB` 함수
- `LENGTHB` 함수 : LENGTH함수와 사용방법은 같지만, 문자열 데이터 길이가 아닌 바이트 수를 반환한다.


- (참조) 한글은 한 문자당 2byte, 영어는 한 글자당 1byte


```python
SELECT LENGTH('한글'), LENGTHB('한글')
FROM DUAL;
```

결과 :
![2-2-3](https://user-images.githubusercontent.com/53929665/93022420-54316400-f624-11ea-928c-6c8b51e05551.JPG)


---
#### (3) SUBSTR 함수
- `SUBSTR` : 문자열 중 일부를 추출할 때 사용


- 사용방법
    - `SUBSTR(문자열 데이터, 시작 위치, 추출 길이)` :  
      문자열 데이터의 시작 위치부터 추출 길이만큼 추출  
      (시작 위치가 음수) 마지막 위치부터 거슬러 올라간 위치에서 시작
      <BR>
      
    - `SUBSTR(문자열 데이터, 시작 위치)` :  
        문자열 데이터의 시작 위치부터 문자열 데이터 끝까지 추출  
        (시작 위치가 음수) 마지막 위치부터 거슬러 올라간 위치에서 끝까지 추출

#### (3-2) `SUBSTR` 함수 사용하기


```python
SELECT JOB, SUBSTR(JOB,1,2), SUBSTR(JOB, 5)
FROM EMP;
```

결과 :
![2-3-2](https://user-images.githubusercontent.com/53929665/93022421-54c9fa80-f624-11ea-9ade-e4c854d1f097.JPG)

#### (3-3) `SUBSTR`함수 안에 다른 함수(`LENTH`) 함께 사용하기


```python
SELECT JOB,
       SUBSTR(JOB, -LENGTH(JOB)),
       SUBSTR(JOB, -LENGTH(JOB), 2),
       SUBSTR(JOB, -3)
FROM EMP;
```

결과 :
![2-3-3](https://user-images.githubusercontent.com/53929665/93022422-54c9fa80-f624-11ea-9364-d567fe493e08.JPG)


---
#### (4) INSTR 함수
- `INSTR` : 문자열 데이터 안에 특정 문자나 문자열이 어디에 포함되어 있는지 알고자 할 때 사용
- `INSTR`함수는 총 4개의 입력 값을 지정할 수 있으며,  
최소 2개의 입력값, 즉 원본 문자열 데이터와 원본 문자열에서 찾으려는 문자  
이렇게 두 가지는 반드시 지정해야한다.


- 기본형식
```
INSTR([대상 문자열 데이터(필수)],
      [위치를 찾으려는 부분 문자(필수)],
      [위치 찾기를 시작할 대상 문자열 데이터 위치(선택, 기본값 1)],
      [시작 위치에서 찾으려는 문자가 몇 번째인지 지정(선택, 기본값 1)])
```

#### (4-1) `INSTR`함수로 문자열 데이터에서 특정 문자열 찾기


```python
SELECT INSTR('HELLO, ORALCE!', 'L') AS INSTR_1,
       INSTR('HELLO, ORACLE!', 'L', 5) AS INSTR_2,
       INSTR('HELLO, ORACLE!', 'L', 2, 2) AS INSTR_3
FROM DUAL;
```

결과 :
- (1) L이 세 번째 문자에서 가장 먼저 발견되므로 3!
- (2) 검색 시작 위치인 5부터 열두 번째 문자에서 L이 가장 먼저 발견되므로 12!
- (3) 검색 시작 위치인 2부터 <U>두 번째로 등장한 L</U>을 의미하기 때문에 4!
![2-4-1](https://user-images.githubusercontent.com/53929665/93022423-55629100-f624-11ea-8a89-8e63c99de61d.JPG)

#### (4-2) 특정 문자를 포함하고 있는 행 찾기 : `INSTR` VS `LIKE`
- `INSTR` 함수로 사원 이름에 문자 S가 있는 행 구하기
    - 만약 찾으려는 <U>문자가 문자열 데이터에 포함되어 있지 않다면</U> 위치 값이 없으므로 <U>0</U>을 반환


```python
SELECT *
FROM EMP
WHERE INSTR(ENAME, 'S') > 0;
```

- `LIKE` 함수로 사원 이름에 문자 S가 있는 행 구하기


```python
SELECT *
FROM EMP
WHERE ENAME LIKE '%S%';
```

결과 (동일) :
![2-4-2](https://user-images.githubusercontent.com/53929665/93022424-55629100-f624-11ea-839b-4b20e10a3d13.JPG)

---
#### (5) REPLACE 함수
- `REPLACE` : 특정 문자열 데이터에 포함된 문자를 다른 문자로 대체할 경우에 사용


- 기본형식
```
REPLACE([문자열 데이터 또는 열이름(필수)],[찾는 문자(필수)],[대체할 문자(선택)]
```

#### (5-1) `REPLACE`함수로 문자열 안에 있는 특정 문자 바꾸기
    - '대체할 문자'를 지정하지 않으면 '찾는 문자'가 삭제된다.


```python
SELECT '010-1234-5678' AS REPLACE_BEFORE,
       REPLACE('010-1234-5678', '-', ' ') AS REPLACE_1,
       REPLACE('010-1234-5678', '-') AS REPLACE_2
FROM DUAL;
```

결과 :
![2-5-1](https://user-images.githubusercontent.com/53929665/93022425-55fb2780-f624-11ea-9fb3-6677c1a40002.JPG)


---
#### (6) LPAD, RPAD 함수
- 데이터와 자릿수를 지정한 후 데이터 길이가 지정한 자릿수보다 작을 경우에 나머지 공간을 특정 문자로 채우는 함수
- `LPAD`(Left Padding) : 남은 빈 공간을 왼쪽에 채움
- `RPAD`(Right Padding) : 남은 빈 공간을 오른쪽에 채움
- <u>빈 공간에 채울 문자를 지정하지 않으면</u> LPAD와 RPAD함수는 빈 공간의 자릿수만큼 <u>공백 문자(spacebar)</u>로 띄운다.


- 기본형식

```
LPAD([문자열 데이터 또는 열이름(필수)], [데이터 자릿수(필수)], [빈 공간에 채울 문자(선택)]

RPAD([문자열 데이터 또는 열이름(필수)], [데이터 자릿수(필수)], [빈 공간에 채울 문자(선택)]
```

#### (6-1) LPAD, RPAD 함수 사용하여 출력하기


```python
SELECT 'Oracle',
       LPAD('Oracle', 10, '#') AS LPAD_1,
       RPAD('Oracle', 10, '*') AS RPAD_1,
       LPAD('Oracle', 10) AS LPAD_2,
       RPAD('Oracle', 10) AS RPAD_2
FROM DUAL;
```

결과 :
![2-6-1](https://user-images.githubusercontent.com/53929665/93022426-55fb2780-f624-11ea-801f-1f5d2ba225e3.JPG)

#### (6-2) 특정 문자로 자릿수 채워서 출력하기
- `RPAD`함수를 사용하여 개인정보 뒷자리 * 표시로 출력하기


```python
SELECT RPAD('971208-', 14, '*') AS RPAD_JMNO,
       RPAD('010-3615', 13, '*') AS RPAD_PHONE
FROM DUAL;
```

결과 :
![2-6-2](https://user-images.githubusercontent.com/53929665/93022427-5693be00-f624-11ea-8234-1ae1c4114666.JPG)

---
#### (7) CONCAT 함수
- `CONCAT` : 두 개의 문자열 데이터를 하나의 데이터로 연결해 주는 역할
- 두 개의 입력 데이터 지정을 하고 열이나 문자열 데이터 모두 지정 가능


- 기본형식
```
CONCAT([문자열 데이터 또는 열이름 1], [문자열 데이터 또는 열이름 2])
```


- (참조)
> `||` 연산자는 `CONCAT` 함수와 유사하게 열이나 문자열을 연결한다.
> ```
> SELECT EMPNO || ENAME,
>        EMPNO || ':' || ENMAE
> FROM...
> ```

#### (7-1) 두 열 사이에 콜론(:) 넣고 연결하기


```python
SELECT CONCAT(EMPNO, ENAME),
       CONCAT(EMPNO, CONCAT(':', ENAME))
FROM EMP
WHERE ENAME = 'SCOTT';
```

결과 :
![2-7-1](https://user-images.githubusercontent.com/53929665/93022428-5693be00-f624-11ea-96b8-ed48bad6b2f7.JPG)

---
#### (8) TRIM, LTRIM, RTRIM 함수
- 문자열 데이터 내에서 특정 문자를 지우기 위해 사용
- 원본 문자열 데이터를 제외한 나머지 데이터는 모두 생략
- 삭제할 문자가 생략될 경우에 기본적으로 공백을 제거
- 삭제옵션
    - `LEADING` : 왼쪽에 있는 글자 삭제
    - `TRAILING` : 오른쪽에 있는 글자 삭제
    - `BOTH` : 양쪽에 있는 글자 삭제
    
    
- 기본형식
    - `TRIM`
       ```
       TRIM([삭제옵션(선택)][삭제할 문자(선택)] FROM [원본 문자열 데이터(필수)])
       ```
       - TRIM 함수의 삭제할 문자는 필수가 아니므로 지정하지 않아도 된다.
       - 삭제할 문자가 없으면 공백이 제거된다.
       
       <BR>
    - `LTRIM`
       ```
       LTRIM([원본 문자열 데이터(필수)], [삭제할 문자 집합(선택)]
       ```
       - 왼쪽의 지정 문자 삭제
       - TRIM과 마찬가지로 삭제할 문자가 없으면 공백이 제거된다.
    
       <BR>
    - `RTRIM`
       ```
       RTRIM([원본 문자열 데이터(필수)], [삭제할 문자 집합(선택)]
       ```
       - 오른쪽의 지정 문자 삭제
       - TRIM과 마찬가지로 삭제할 문자가 없으면 공백이 제거된다.

#### (8-1) `TRIM`함수 사용(삭제할 문자가 없을 때)

- `TRIM`함수로 공백 제거하여 출력


```python
SELECT '_' || TRIM(' _ _Oracle_ _ ') || '_' AS TRIM,
       '_' || TRIM(LEADING FROM ' _ _Oracle_ _ ') || '_' AS TRIM_LEADING,
       '_' || TRIM(TRAILING FROM ' _ _Oracle_ _ ') || '_' AS TRIM_TRAILING,
       '_' || TRIM(BOTH FROM ' _ _Oracle_ _ ') || '_' AS TRIM_BOTH
FROM DUAL;
```

결과 :
아래 결과 테이블을 보면 '_' 와 '_'의 띄임과 붙임을 통해 TRIM함수가 어떤 함수인지 눈으로 확인 가능하다.
![2-8-1](https://user-images.githubusercontent.com/53929665/93022429-572c5480-f624-11ea-8ff5-76c89e2183a9.JPG)

#### (8-2) `TRIM`함수 사용하기(삭제할 문자가 있을 때)
- `TRIM`함수로 삭제할 문자 '_' 삭제 후 출력하기


```python
SELECT '_' || TRIM('_' FROM '_ _Oracle_ _') || '_' AS TRIM,
       '_' || TRIM(LEADING '_' FROM '_ _Oracle_ _') || '_' AS TRIM_LEADING,
       '_' || TRIM(TRAILING '_' FROM '_ _Oracle_ _') || '_' AS TRIM_TRAILING,
       '_' || TRIM(BOTH '_' FROM '_ _Oracle_ _') || '_' AS TRIM_BOTH
FROM DUAL; 
```

결과 : 잘 안보이지만, 8-2의 예제 _ _ Oracle _ _은 양쪽에 공백이 없지만, 8-1의 예제는 양쪽에 공백이 있다.
![2-8-2](https://user-images.githubusercontent.com/53929665/93022430-572c5480-f624-11ea-99f3-b647940cef0c.JPG)


#### (8-3) `TRIM`, `LTRIM`, `RTRIM` 사용하여 문자열 출력하기


```python
SELECT '_' || TRIM(' _Oracle_ ') || '_' AS TRIM,
       '_' || LTRIM(' _Oracle_ ') || '_' AS LTRIM,
       '_' || LTRIM('<_Oracle_>', '_<') || '_' AS LTRIM_2,
       '_' || RTRIM(' _Oracle_ ') || '_' AS RTRIM,
       '_' || RTRIM('<_Oracle_>', '>_') || '_' AS RTRIM_2
FROM DUAL;
```

결과 : 
![2-8-3](https://user-images.githubusercontent.com/53929665/93022431-57c4eb00-f624-11ea-9fa6-606267616ca0.JPG)


---
### [ 3. 숫자 함수 ]
|함수|설명|
|---|---|
|`ROUND`|지정한 숫자의 특정 위치에서 반올림한 값을 반환|
|`TRUNC`|지정한 숫자의 특정 위치에서 버림한 값을 반환|
|`CEIL`|지정된 숫자보다 큰 정수 중 가장 작은 정수를 반환|
|`FLOOR`|지정된 숫자보다 작은 정수 중 가장 큰 정수를 반환|
|`MOD`|지정된 숫자를 나눈 나머지 값을 반환|

#### (1) ROUND 함수 (숫자 Ver.)
- 특정 숫자를 반올림하되 반올림할 위치를 지정할 수 있다.
- default 값 : 소수점 첫째 자리
- 반올림위치가 음수인 경우 : 자연수에서 반올림
    - -1 -> 자연수 일의자리에서 반올림
    - -2 -> 자연수 십의자리에서 반올림


- 기본형태
```
ROUND([숫자(필수)], [반올림위치(선택)])
```

#### (1-1) `ROUND` 함수를 사용하여 반올림된 숫자 출력하기


```python
SELECT ROUND(1234.5678) AS ROUND,
       ROUND(1234.5678, 0) AS ROUND_0,
       ROUND(1234.5678, 1) AS ROUND_1,
       ROUND(1234.5678, 2) AS ROUND_2,
       ROUND(1234.5678, -1) AS ROUND_MINUS1,
       ROUND(1234.5678, -2) AS ROUND_MINUS2
FROM DUAL;
```

결과 :
![3-1-1](https://user-images.githubusercontent.com/53929665/93734394-14661000-fc14-11ea-8ae1-2b5b115a573e.JPG)

#### (2) TRUNC 함수 (숫자 Ver.)
- `TRUNC` : 지정된 자리에서 숫자를 버림 처리하는 함수
- ROUND 함수와 같은 방식으로 버림 처리할 자릿수 지정이 가능
- default : 소수점 첫째 자리


- 기본형태
```
TRUNC([숫자(필수)], [버림위치(선택)])
```

#### (2-1) `TRUNC` 함수를 사용하여 숫자 출력하기


```python
SELECT TRUNC(1234.5678) AS TRUNC,
       TRUNC(1234.5678, 0) AS TRUNC_0,
       TRUNC(1234.5678, 1) AS TRUNC_1,
       TRUNC(1234.5678, 2) AS TRUNC_2,
       TRUNC(1234.5678, -1) AS TRUNC_MINUS1,
       TRUNC(1234.5678, -2) AS TRUNC_MINUS2
FROM DUAL;
```

결과 :

![3-2-1](https://user-images.githubusercontent.com/53929665/93734399-14fea680-fc14-11ea-9bbd-19c6981160c3.JPG)

#### (3) `CEIL`, ` FLOOR` 함수
- `CEIL`함수와 `FLOOR`함수는 각각 입력된 숫자와 가까운 큰 정수, 작은 정수를 반환하는 함수


- 기본형태
```
CEIL([숫자(필수)])
FLOOR([숫자(필수)])
```


#### (3-1) `CEIL`, `FLOOR` 함수로 숫자 출력하기


```python
SELECT CEIL(3.14),
       FLOOR(3.14),
       CEIL(-3.14),
       FLOOR(-3.14)
FROM DUAL;
```

결과 :

![3-3-1](https://user-images.githubusercontent.com/53929665/93734400-15973d00-fc14-11ea-8397-bfc32f8ef6f0.JPG)

#### (4) MOD 함수
- 나머지를 구할때 쓰인다.
- 입력 데이터가 <U>짝수</U>인지, <U>홀수</U>인지 구별하는 용도로도 사용할 수 있다.

- 기본형태
```
MOD([나눗셈 될 숫자(필수)][나눌 숫자(필수)])
```

#### (4-1) `MOD`함수를 사용하여 나머지 값 출력하기


```python
SELECT MOD(15, 6),
       MOD(10, 2),
       MOD(11, 2)
FROM DUAL;
```

결과 :

![3-4-1](https://user-images.githubusercontent.com/53929665/93734401-162fd380-fc14-11ea-915d-090154396d7d.JPG)

---
### [ 4. 날짜 함수 ]
- 오라클에서 날짜 데이터, 즉 `DATE`형 데이터는 다음과 같이 간단한 연산이 가능하다.
- (주의) 날짜 데이터끼리의 더하기 연산은 가능하지 않다.


|연산|설명|
|:--|:---|
|날짜 데이터 + 숫자|날짜 데이터로 부터 숫자만큼 일수 이후의 날짜|
|날짜 데이터 - 숫자|날짜 데이터로 부터 숫자만큼 일수 이전의 날짜|
|날짜 데이터 - 날짜 데이터|두 날짜 데이터 간의 일수 차이|
|날짜 데이터 + 날짜 데이터|지원X|

#### (1) SYSDATE 함수
- `SYSDATE` : 별다른 입력 데이터 없이, 오라클 데이터베이스 서버가 놓인 OS의 현재 날짜와 시간을 보여준다.

#### (1-1) SYSDATE 함수를 사용하여 날짜 출력하기


```python
SELECT SYSDATE AS NOW,
       SYSDATE+1 AS TOMORROW,
       SYSDATE-1 AS YESTERDAY
FROM DUAL;
```

결과 :

![4-1-1](https://user-images.githubusercontent.com/53929665/93734402-16c86a00-fc14-11ea-9957-a2b51a1ef3f4.JPG)

#### (2) ADD_MONTHS 함수
- `ADD_MONTHS` : 특정 날짜에 지정한 개월 수 이후 날짜 데이터를 반환하는 함수


- 기본형태
```
ADD_MONTHS([날짜 데이터(필수)], [더할 개월 수(정수)(필수)])
```


#### (2-1) `SYSDATE`와 `ADD_MONTHS` 함수로 3개월 후 날짜 구하기


```python
SELECT SYSDATE AS NOW,
       ADD_MONTHS(SYSDATE, 3) AS AFTER3MONTH
FROM DUAL;
```

결과 :

![4-2-1](https://user-images.githubusercontent.com/53929665/93734403-16c86a00-fc14-11ea-8da6-992d5381ede7.JPG)

#### (2-2) 입사 10주년이 되는 사원들 데이터 출력하기
    - `ADD_MONTHS`함수는 별로 사용되지 않을 것이라 여길 수도 있다. 하지만, 은근히 자주 사용되는 함수에 속한다.  
       특히, 윤년 등의 이유로 복잡해질 수 있는 날짜 계산을 간단하게 만들어주기 때문이다.


```python
SELECT EMPNO, ENAME, HIREDATE,
       ADD_MONTHS(SYSDATE, 120)
FROM EMP;
```

결과 :

![4-2-2](https://user-images.githubusercontent.com/53929665/93734404-17610080-fc14-11ea-822d-2b89eec5ac1b.JPG)

#### (2-3) 입사 32년 미만인 사원 출력하기


```python
SELECT EMPNO, ENAME, HIREDATE
FROM EMP
WHERE ADD_MONTHS(SYSDATE, 384) > SYSDATE;
```

결과 :

![4-2-3](https://user-images.githubusercontent.com/53929665/93734406-17610080-fc14-11ea-9542-ad223217d41a.JPG)

#### (3) MONTHS_BETWEEN 함수
- `MONTHS_BETWEEN` : 두 개의 날짜 데이터를 입력하고 두 날짜 간의 <U>개월 수 차이</U>를 구하는데 사용.


- 기본형태
```
MONTHS_BETWEEN([날짜 데이터1(필수)][날짜 데이터2(필수)])
```


#### (3-2) HIREDATE와 `SYSDATE` 사이의 개월 수를 `MONTHS_BETWEEN` 함수로 출력하기


```python
SELECT ENAME, HIREDATE, SYSDATE,
       MONTHS_BETWEEN(HIREDATE, SYSDATE) AS MONTHS_1,
       MONTHS_BETWEEN(SYSDATE, HIREDATE) AS MONTHS_2,
       TRUNC(MONTHS_BETWEEN(SYSDATE, HIREDATE)) AS MONTHS_3
FROM EMP;
```

결과 :

![4-3-2](https://user-images.githubusercontent.com/53929665/93734407-17f99700-fc14-11ea-8698-d1cd75d76a2c.JPG)

#### (4) NEXT_DAY, LAST_DAT 함수
- `NEXT_DAY` : 입력한 날짜 데이터에서 <U>돌아오는 요일의 날짜</U>를 반환
    - ex. 돌아오는 월요일의 날짜를 알고싶은 경우 :  
    오늘이 화요일이면 다음주 월요일의 날짜를 구해줌
    
    - ex. 돌아오는 목요일의 날짜를 알고싶은 경우 :  
    오늘이 화요일이면 이번주 목요일의 날짜를 구해줌
    
    
    
- `LAST_DAY` : 입력한 날짜 데이터가 <U>속한 달의 마지막 날짜</U>를 반환


- 기본형태

```
NEXT_DAY([날짜 데이터(필수)],[요일 문자(필수)])
```

```
LAST_DAY([날짜 데이터(필수)])
```

#### (4-1) `NEXT_DAY`, `LAST_DAY` 함수를 사용하여 출력


```python
SELECT SYSDATE,
       NEXT_DAY(SYSDATE, '월요일'),
       LAST_DAY(SYSDATE)
FROM DUAL;
```

결과 :

![4-4-1](https://user-images.githubusercontent.com/53929665/93734408-17f99700-fc14-11ea-8327-bc3794a6f3e6.JPG)

#### (5) ROUND, TRUNC 함수 (날짜 Ver.)
- 숫자 데이터의 반올림, 버림에 사용되는 이 함수는 날짜 데이터를 입력 데이터로 사용할 수 있다.
- 이때는 <U>소수점 위치 정보를 입력하지 않고</U> 반올림, 버림의 <U>기준</U>이 될 `포맷(format)`값을 지정해준다.


- 기본형태

```
ROUND([날짜데이터(필수)],[반올림 기준 포맷])
```

```
TRUNC([날짜데이터(필수)],[버림 기준 포맷])
```


- (<B>오라클</B>) 날짜 데이터를 사용할때 <U>기준 포맷값</U> :
![oracle1](https://user-images.githubusercontent.com/53929665/93224451-119a9380-f7ac-11ea-8391-869c4141ba7f.png)



#### (5-1) `ROUND` 함수를 사용하여 날짜 데이터 출력하기


```python
SELECT  SYSDATE,
        ROUND(SYSDATE,'CC') AS FORMAT_CC,
        ROUND(SYSDATE,'YYYY') AS FORMAT_YYYY,
        ROUND(SYSDATE,'Q') AS FORMAT_Q,
        ROUND(SYSDATE,'DDD') AS FORMAT_DDD,
        ROUND(SYSDATE,'HH') AS FORMAT_HH
FROM DUAL;
```

결과 :
- `CC`의 경우 : 2020년은 <U>2050년보다</U> 작아 반올림하면 2001/01/01  
- `YYYY`의 경우 : 9월 15일은 <U>기준 7월 1일</U>보다 지났기에 반올림하면 2021/01/01   
- `Q`의 경우 : <U>3분기의 두 번쨰 달의 16일인 8월 16일 기준으로</U> 9월 15일은 기준보다 지났기에 한달 반올림하여 2020/10/01  
- `DDD`의 경우 : 오후 11시는 <U>낮 12시 기준으로</U> 지난 시간이기에 반올림하면 다음날인 2020/9/16  
- `HH`의 경우 : 시간을 기준으로 <U>30분을 넘었으므로</U> 38분이기 때문에 시간을 반올림해서 00:00:00  

![4-5-1](https://user-images.githubusercontent.com/53929665/93734409-18922d80-fc14-11ea-81ed-5784cf0d9b97.JPG)

#### (5-2) `TRUNC` 함수를 사용하여 날짜 데이터 출력하기


```python
SELECT  SYSDATE,
        TRUNC(SYSDATE,'CC') AS FORMAT_CC,
        TRUNC(SYSDATE,'YYYY') AS FORMAT_YYYY,
        TRUNC(SYSDATE,'Q') AS FORMAT_Q,
        TRUNC(SYSDATE,'DDD') AS FORMAT_DDD,
        TRUNC(SYSDATE,'HH') AS FORMAT_HH
FROM DUAL;
```

결과 :
- `CC`의 경우 : 년도의 일의 자리, 십의 자리 숫자 버림
- `YYYY`의 경우 : 연도 기준으로 월, 일 버림
- `Q`의 경우 : 9월은 3분기에 속하므로, 3분기의 시작인 7월까지 버림
- `HH`의 경우 : 시간을 기준으로 분 버림

![4-5-2](https://user-images.githubusercontent.com/53929665/93734410-18922d80-fc14-11ea-80e8-86ff6ac83f77.JPG)

---
### [ 5. 형 변환 함수 ]

#### (1) 자동 형 변환
- 암시적 형 변환 (implicit ype conversion) 
- 숫자처럼 생긴 문자 데이터는 숫자로 바꿔준다. ( 그 외의 경우는 잘 동작하지 않는다. )

#### (1-1) 숫자와 문자열(숫자)을 더하여 출력하기


```python
SELECT EMPNO, ENAME, EMPNO + '500'
FROM EMP
WHERE ENAME = 'SCOTT';
```

결과 : 숫자로 인식 가능한 문자 데이터가 자동으로 숫자로 바뀐 후 연산이 수행됨

![5-1-1](https://user-images.githubusercontent.com/53929665/93734412-192ac400-fc14-11ea-8d59-7d3c6ce7506c.JPG)

#### (1-2) 문자열(문자)과 숫자를 더하여 출력하기 (오류)


```python
SELECT 'ABCD' + EMPNO, EMPNO
FROM EMP
WHERE ENAME = 'SCOTT';
```

결과 : (1-1)의 예제의 경우 외에는 오류가 발생한다.

![5-1-2](https://user-images.githubusercontent.com/53929665/93734414-192ac400-fc14-11ea-9d86-fe39660a6277.JPG)


#### (2) 명시적 형 변환
- `명시적 형 변환` : `형 변환 함수`를 사용하여 자료형을 변환해 주는 방식


- 오라클에서 자료형이 자동으로 변환되는 방식이 아닌 사용자, 즉 우리가 자료형을 직접 지정해 주는 방식을 `명시적 형 변환(explicit type conversion)`이라고 한다. 


- `형 변환 함수`의 종류
    - 문자를 중심으로 숫자 또는 날짜 데이터의 변환

|종류|설명|
|:----|:----|
|`TO_CHAR`|숫자,날짜 데이터 -> 문자 데이터|
|`TO_NUMBER`|문자 데이터 -> 숫자 데이터|
|`TO_DATE`|문자데이터 -> 날짜 데이터|



#### (3) TO_CHAR 함수
- `TO_CHAR` 함수는 날짜, 숫자데이터를 문자데이터로 변환해 주는 함수
- 주로 날짜 데이터에서 문자 데이터로 변환하는데 빈번히 사용된다.


- 기본형태
```
TO_CHAR([날짜 데이터(필수)], '[출력되길 원하는 문자 형태(필수)]')
```

- (참조) 출력되길 원하는 문자 형태 Ver. 날짜(format)
<img src="https://user-images.githubusercontent.com/53929665/93344524-bed4e080-f86c-11ea-9d94-d8f7fd91c2fd.jpg" alt="drawing" width="350"/>

- (참조) 출력되길 원하는 문자 형태 Ver. 시간(format)
<img src="https://user-images.githubusercontent.com/53929665/93359272-5b9f7a00-f87d-11ea-8301-8b71877a0405.jpg" alt="drawing" width="350"/>

- (참조) 출력되길 원하는 문자 형태 Ver. 숫자(format)
<img src="https://user-images.githubusercontent.com/53929665/93360132-4545ee00-f87e-11ea-982a-9fc9f43972b3.png" alt="drawing" width="350"/>


#### (3-2) SYSDATE 날짜 형식 지정하여 출력하기


```python
SELECT TO_CHAR(SYSDATE, 'YYYY/MM/DD HH24:MI:SS') AS 현재날짜시간
FROM DUAL;
```

결과 :

![5-3-2](https://user-images.githubusercontent.com/53929665/93734416-19c35a80-fc14-11ea-8252-d8c6be7b72c4.JPG)

#### (3-3) 월과 요일을 다양한 형식으로 출력하기


```python
SELECT  SYSDATE,
        TO_CHAR(SYSDATE, 'MM') AS MM,
        TO_CHAR(SYSDATE, 'MON') AS MON,
        TO_CHAR(SYSDATE, 'MONTH') AS MONTH,
        TO_CHAR(SYSDATE, 'DD') AS DD,
        TO_CHAR(SYSDATE, 'DAY') AS DAY
FROM DUAL;
```

결과 :

![5-3-3](https://user-images.githubusercontent.com/53929665/93734417-19c35a80-fc14-11ea-9f8b-36546e40bbfc.JPG)

#### (3-4)  (중요) 특정 언어에 맞춰서 날짜 출력하기
```
TO_CHAR([날짜 데이터(필수)], 
        '[출력되길 원하는 문자 형태(필수)]', 
        'NLS_DATE_LANGUAGE = language'(선택))
```

- 여러 언어로 날짜(월) 출력하기


```python
SELECT  SYSDATE,
        TO_CHAR(SYSDATE, 'MM') AS MM,
        TO_CHAR(SYSDATE, 'MON', 'NLS_DATE_LANGUAGE = KOREAN') AS MON_KOR,
        TO_CHAR(SYSDATE, 'MON', 'NLS_DATE_LANGUAGE = JAPANESE') AS MON_JPN,
        TO_CHAR(SYSDATE, 'MON', 'NLS_DATE_LANGUAGE = ENGLISH') AS MON_ENG,
        TO_CHAR(SYSDATE, 'MONTH', 'NLS_DATE_LANGUAGE = KOREAN') AS MONTH_KOR,
        TO_CHAR(SYSDATE, 'MONTH', 'NLS_DATE_LANGUAGE = JAPANESE') AS MONTH_JPN,
        TO_CHAR(SYSDATE, 'MONTH', 'NLS_DATE_LANGUAGE = ENGLISH') AS MONTH_ENG
FROM DUAL;
```

결과 :

![5-3-4(2)](https://user-images.githubusercontent.com/53929665/93734418-1a5bf100-fc14-11ea-99d1-6a1f0ae931ca.JPG)

- 여러 언어로 날짜(요일) 출력하기


```python
SELECT  SYSDATE,
        TO_CHAR(SYSDATE, 'MM') AS MM,
        TO_CHAR(SYSDATE, 'DD') AS DD,
        TO_CHAR(SYSDATE, 'DY', 'NLS_DATE_LANGUAGE = KOREAN') AS DY_KOR,
        TO_CHAR(SYSDATE, 'DY', 'NLS_DATE_LANGUAGE = JAPANESE') AS DY_JPN,
        TO_CHAR(SYSDATE, 'DY', 'NLS_DATE_LANGUAGE = ENGLISH') AS DY_ENG,
        TO_CHAR(SYSDATE, 'DAY', 'NLS_DATE_LANGUAGE = KOREAN') AS DAY_KOR,
        TO_CHAR(SYSDATE, 'DAY', 'NLS_DATE_LANGUAGE = JAPANESE') AS DAY_JPN,
        TO_CHAR(SYSDATE, 'DAY', 'NLS_DATE_LANGUAGE = ENGLISH') AS DAY_ENG
FROM DUAL;
```

결과 :

![5-3-4](https://user-images.githubusercontent.com/53929665/93734419-1a5bf100-fc14-11ea-8fbf-9371344f6eba.JPG)

#### (3-5) 시간 형식 지정하여 출력하기


```python
SELECT  SYSDATE,
        TO_CHAR(SYSDATE, 'HH24:MI:SS') AS HH24MISS,
        TO_CHAR(SYSDATE, 'HH12:MI:SS AM') AS HHMISS_AM,
        TO_CHAR(SYSDATE, 'HH:MI:SS P.M.') AS HHMISS_PM
FROM DUAL;
```

결과 :

![5-3-5](https://user-images.githubusercontent.com/53929665/93734420-1af48780-fc14-11ea-84f3-374c714ecf58.JPG)

#### (3-6) 숫자 형식을 사용하여 출력하기


```python
SELECT  SAL,
        TO_CHAR(SAL, '$999,999') AS SAL_$,
        TO_CHAR(SAL, 'L999,999') AS SAL_L,
        TO_CHAR(SAL, '999,999.00') AS SAL_1,
        TO_CHAR(SAL, '000,999,999.00') AS SAL_2,
        TO_CHAR(SAL, '000999999.99') AS SAL_3,
        TO_CHAR(SAL, '999,999,00') AS SAL_4
FROM EMP;
```

결과 :

![5-3-6](https://user-images.githubusercontent.com/53929665/93734422-1af48780-fc14-11ea-85d3-2d6f063a1996.JPG)


#### (4) TO_NUMBER 함수
- 아래의 숫자처럼 생긴 문자열 데이터는 위에서 봤던거 처럼 암묵적 연산이 불가능하다.
- 왜냐하면, 숫자 사이에 쉼표(,)가 들어가서 숫자로 변환이 되지 않았기 때문이다.


- 따라서, 아래의 예제와 같이 숫자 데이터가 가공된 문자 데이터로 저장되어있고 그 데이터를 산술 연산에 사용하고자 할 경우, 문자 데이터를 수자 형태로 강제로 인식시켜야한다.


- 이때, 사용하는 함수가 바로 `TO_NUMBER` 함수이다.


```python
SELECT '1,300' - '1,500'
FROM DUAL;
```

결과 :

![5-4](https://user-images.githubusercontent.com/53929665/93734423-1b8d1e00-fc14-11ea-99af-8b60e670c0a8.JPG)

- 기본형태
```
TO_NUMBER('[문자열 데이터(필수)]', '[인식될 숫자형태(필수)]')
```

#### (4-1) `TO_NUMBER` 함수로 연산하여 출력하기


```python
SELECT TO_NUMBER('1,300', '999,999') - TO_NUMBER('1,500', '999,999')
FROM DUAL;
```

결과 :

![5-4-1](https://user-images.githubusercontent.com/53929665/93734424-1b8d1e00-fc14-11ea-893d-298eff9d1a96.JPG)


#### (5) TO_DATE 함수
- `TO_DATE` : 문자열 데이터 -> 날짜 데이터


- 기본형식
```
TO_DATE('[문자열 데이터(필수)]`, `[인식될 날짜형태(필수)]`)
```

#### (5-1) `TO_DATE`함수로 문자 데이터를 날짜 데이터로 변환하기


```python
SELECT  TO_DATE('2020-09-17', 'YYYY-MM-DD') AS TODATE1,
        TO_DATE('20180917', 'YYYYMMDD') AS TODATE2
    FROM DUAL;
```

결과 :

![5-5-1](https://user-images.githubusercontent.com/53929665/93734426-1c25b480-fc14-11ea-8cb9-91bf14d7803e.JPG)

#### (5-2) 1981년 6월 1일 이후에 입사한 사원 정보 출력하기


```python
SELECT  *
    FROM EMP
    WHERE HIREDATE > TO_DATE('1981/06/01', 'YYYY/MM/DD');
```

결과 :

![5-5-2](https://user-images.githubusercontent.com/53929665/93734427-1c25b480-fc14-11ea-8e47-75f6044b7986.JPG)

#### (5-3) 두 자리 연도를 표현할 때 주의사항
- `YY` : 어떤 두 자리 수가 입력되어도 현 시점의 연도와 동일한 연도로 계산
- `RR` : 현 시점의 연도의 끝 두 자리 수가 00 ~ 49, 50 ~ 99 인 경우를 계산하여 비교적 가까운 날짜 데이터를 계산 (RR 포맷은 아직 이해가 안됨, 하지만 잘 안사용할거같음)


```python
SELECT  TO_DATE('49/12/19', 'YY/MM/DD') AS YY_YEAR_49,
        TO_DATE('49/12/19', 'RR/MM/DD') AS RR_YEAR_49,
        TO_DATE('50/12/19', 'YY/MM/DD') AS YY_YEAR_50,
        TO_DATE('50/12/19', 'RR/MM/DD') AS RR_YEAR_50,
        TO_DATE('51/12/19', 'YY/MM/DD') AS YY_YEAR_51,
        TO_DATE('51/12/19', 'RR/MM/DD') AS RR_YEAR_51
   FROM DUAL;
```

결과 :

![5-5-3](https://user-images.githubusercontent.com/53929665/93734428-1cbe4b00-fc14-11ea-90c0-e0a515cd0622.JPG)

---
### [ 6. NULL 처리 함수 ]
- 특정 열의 데이터가 `NULL`일 경우에 연산 수행을 위해 데이터를 NULL이 아닌 다른 값으로 대체해 주어야 할 때가 종종 있다.
- 이때, `NVL` 과 `NVL2`가 매우 유용하다.

#### (1) NVL 함수
- `NVL` : 첫 번째 입력 데이터가 NULL이 아니면 그 데이터를 그대로 반환하고, NULL 이라면 두 번째 입력 데이터에 지정한 값을 반환한다.


- 기본형식
```
NVL([NULL인지 여부를 검사할 데이터 또는 열(필수)],
    [앞의 데이터가 NULL일 경우 반환할 데이터](필수))
```

#### (1-1) `NVL`함수를 사용하여 출력하기


```python
SELECT  EMPNO, ENAME, SAL, COMM, SAL+COMM,
        NVL(COMM, 0),
        SAL+NVL(COMM, 0)
    FROM EMP;
```

결과 : COMM 열 값이 NULL인 데이터를 0으로 대체하여 연산이 가능하다.

![6-1-1](https://user-images.githubusercontent.com/53929665/93734430-1cbe4b00-fc14-11ea-9a63-450940470c1d.JPG)

#### (2) NVL2 함수
- `NVL2` : NVL 함수와 비슷하지만 데이터가 NULL이 아닐 때 반환할 데이터를 추가로 지정해 줄 수 있다.


- `NVL2` 함수는 `NVL` 함수와는 달리 NULL이 아닌 경우에 반환 데이터까지 지정할 수 있으므로 좀 더 다양한 용도로 활용 가능하다.


- 기본형식
```
NVL2([NULL인지 여부를 검사할 데이터 또는 열(필수)],
     [앞 데이터가 NULL이 아닐 경우 반환할 데이터 또는 계산식(필수)],
     [앞 데이터가 NULL일 경우 반환할 데이터 또는 계산식(필수)])
```

#### (2-1) `NVL2`함수를 사용하여 출력하기


```python
SELECT  EMPNO, ENAME, COMM,
        NVL2(COMM, 'O', 'X'),
        NVL2(COMM, SAL*12+COMM, SAL*12) AS ANNSAL
    FROM EMP;
```

결과 : 

![6-2-1](https://user-images.githubusercontent.com/53929665/93734431-1d56e180-fc14-11ea-81f7-102395d6fdde.JPG)



---
### [ 7. DECODE 함수와 CASE문 ]

- NVL, NVL2 함수는 NULL인 경우 어떤 데이터를 반환할지 정하는 함수이지만,
- `DECODE` 함수 또는 `CASE` 문은 특정 열 값이나 데이터 값에 따라 어떤 데이터를 반환할때 사용


- (주의) `DECODE`함수와 `CASE`문은 모두 조건별로 <U>동일한 자료형의 데이터를 반환</U>해야 한다!!!!!!!!!!!!!!!!  
ex) 특정 조건1의 결과가 문자형, 특정 조건2의 결과가 숫자형 : 불가능!!!!!!!!  
ex) 특정 조건1의 결과가 문자형, 특정 조건2의 결과가 문자형 : 가능!!!!!!!


#### (1) DECODE 함수
- `DECODE` : 기준이 되는 데이터를 먼저 지정한 후 해당 데이터 값에 따라 다른 결과 값을 내보내는 함수 ( 프로그래밍 언어에 사용되는 if 조건문, switch-case 조건문과 비슷 )


- 만약 `DECODE` 함수의 맨 마지막 데이터, 즉 조건에 해당하는 값이 없을 때 반환 값을 지정하지 않으면 `NULL`이 반환된다.


- 기본형식
```
DECODE([검사 대상이 될 열 또는 데이터, 연산이나 함수의 결과],
       [조건1], [데이터가 조건1과 일치할 때 반환할 결과],
       [조건2], [데이터가 조건1과 일치할 때 반환할 결과],
       ...
       [조건n], [데이터가 조건1과 일치할 때 반환할 결과],
       [위 조건1~조건n과 일치한 경우가 없을 때 반환할 결과])
```

#### (1-1) `DECODE` 함수를 사용하여 출력하기
    직책이 MANAGER인 사람은 급여의 10% 인상,  
    SALESMAN인 사람은 급여의 5% 인상,  
    ANALYST인 사람은 그대로,  
    나머지는 3%만큼 인상


```python
SELECT  EMPNO, ENAME, JOB, SAL,
        DECODE(JOB,
                'MANAGER', SAL*1.1,
                'SALESMAN', SAL*1.05,
                'ANALYST', SAL,
                SAL*1.03) AS UPSAL
    FROM EMP;
```

결과 :

![7-1-1](https://user-images.githubusercontent.com/53929665/93734433-1d56e180-fc14-11ea-9cd5-69e6ccc8faee.JPG)

#### (2) CASE 문
- DECODE 함수와 마찬가지로 특정 조건에 따라 반환할 데이터를 설정할 때 사용


- 기준 데이터를 반드시 명시하고 그 값에 따라 반환 데이터를 정하는 DECODE 함수와 달리,
- `CASE`문은 <U>각 조건에 사용하는 데이터가 서로 상관없어도 된다.</U>
- 또 기준 데이터 값이 같은 데이터외에 다양한 조건을 사용할 수 있다.


- 기본형식
```
CASE [검사 대상이 될 열 또는 데이터, 연산이나 함수의 결과(선택)]
    WHEN [조건1] THEN [조건1의 결과 값이 TRUE일 때, 반환할 결과]
    WHEN [조건2] THEN [조건2의 결과 값이 TRUE일 때, 반환할 결과]
    ...
    WHEN [조건n] THEN [조건n의 결과 값이 TRUE일 때, 반환할 결과]
    ELSE [위 조건1~조건n과 일치하는 경우가 없을 때 반환할 결과]
END
```

#### (2-1) `CASE`문을 사용하여 출력하기 (1-1과 같은 주제)


```python
SELECT  EMPNO, ENAME, JOB, SAL,
        CASE JOB
            WHEN 'MANNAGER' THEN SAL*1.1
            WHEN 'SALESMAN' THEN SAL*1.05
            WHEN 'ANALYST' THEN SAL
            ELSE SAL*1.03
        END AS UPSAL
    FROM EMP;
```

결과 : 1-1과 동일

#### (2-2) <U>기준 데이터 없이</U> 조건식만으로 `CASE`문 사용하기
- 열 값에 따라서 출력 값이 달라지는 `CASE`문


```python
SELECT  EMPNO, ENAME, COMM,
        CASE
        WHEN COMM IS NULL THEN '해당사항없음'
        WHEN COMM = 0 THEN '수당없음'
        WHEN COMM > 0 THEN '수당 :' || COMM
        END AS COMM_TEXT
    FROM EMP;
```

결과 :

![7-2-2](https://user-images.githubusercontent.com/53929665/93734434-1def7800-fc14-11ea-94f4-d6022a7e4591.JPG)

<br>

---

### References

- 이지훈, 『Do it! 오라클로 배우는 데이터베이스』, 이지스퍼블리싱 (2018)

