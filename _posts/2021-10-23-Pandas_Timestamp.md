---
layout: post
title:  "[Pandas] 시계열 데이터 : Timestamp"
subtitle: "[Pandas] 시계열 데이터 : Timestamp"
categories: data
tags: pandas
comments: true
mathjax: true
---
#### Contents
- [Object To Timestamp : to_datetime함수](#object-to-timestamp--to_datetime함수)
	- [1-1) format 옵션](#1-1-format-옵션)
	- [1-2) infer_datetime_format 옵션](#1-2-infer_datetime_format-옵션)
	- [1-3) to_datetime함수에서 format 스트링을 명시해야하는 이유](#1-3-to_datetime함수에서-format-스트링을-명시해야하는-이유)
	- [1-4) error 옵션](#1-4-error-옵션)
	- [1-5) dayfirst, yearfirst 옵션](#1-5-dayfirst-yearfirst-옵션)
- [Timestamp의 timezone](#timestamp의-timezone)
	- [2-1) dt.tz_localize](#2-1-dttz_localize)
	- [2-2) dt.tz_convert](#2-2-dttz_convert)
- [Timestamp 배열 생성 : date_range](#timestamp-배열-생성--date_range)

본 포스팅은 카테고리 pandas의 시계열데이터에 관하여 정리하였습니다.

<br>

---

예시 데이터를 불러온다. 다음 데이터는 시간과 날짜에 대한 예시 정보를 가지고 있으며 모든 필드가 `object`객체로 이루어져있다.  

```python
import pandas as pd
df = pd.read_csv('./datetime_ex.csv')
print(df.info())
df.head(5)
```

```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 1000 entries, 0 to 999
Data columns (total 3 columns):
 #   Column        Non-Null Count  Dtype 
---  ------        --------------  ----- 
 0   origin_date1  1000 non-null   object
 1   origin_date2  1000 non-null   object
 2   origin_date3  1000 non-null   object
dtypes: object(3)
memory usage: 23.6+ KB
None

	      origin_date1 origin_date2 origin_date3
0  2015-10-02 21:12:01 UTC   02/10/2015     02/10/15
1  2014-10-26 15:12:00 UTC   26/10/2014     26/10/14
2  2014-10-26 15:12:00 UTC   26/10/2014     26/10/14
3  2014-10-26 15:12:00 UTC   26/10/2014     26/10/14
4  2014-10-26 18:12:00 UTC   26/10/2014     26/10/14
```

<br>

## <span style="color:navy">Object To Timestamp : to_datetime함수<span>

판다스의 `to_datetime()` 함수를 이용하면 `object`,  <br>
즉 문자열로 구성된 시간 및 날짜를 `datetime64`형식으로 변환시킬 수 있다.<br>
다음은 `to_datetime()` 함수의 기본형식과 유용한 파라미터이다.

```python
pd.to_datetime(
		arg,
		format=..., infer_datetime_format=True|False, utc=True|False,
		errors= 'raise'|'coerce'|'ignore', 
		dayfirst=True|False, yearfirst=True|False
		)
```

- `arg` :  `datetime64`형식으로 변환시키려는 `object`형식의 대상
- `format`
    - `arg`의 <u>timestamp패턴을 직접 지정할 때 사용</u>한다.
    - 만약 2021-01-01(type = object)을 2021년 1월 1일이라는 의미를 가진 timestmap로 
    변환시킬 때 `format = '%Y-%m-%d'`로 기입한다.
- `infer_datetime_format`
    - `boolean`값을 받는 옵션이다. (기본값은 `False`이다.)
    - 해당 옵션이 `True`일 경우, `arg`의 패턴을 직접 지정하는 `format` 과 다르게, 
    <u>timestamp관련 포맷 중 하나를 자동으로 파싱하여 지정</u>한다.
- `utc`
    - `boolean`값을 받는 옵션이다. (기본값은 `None`이다.)
    - `object`를 변환시킬 때 해당 옵션이 `True`일 경우, <u>반환값의 timezone이 UTC로 반환</u>된다.
    - <u>timezone이 변경될 </u>`object`<u>타입의 timestamp에 주로 사용</u>한다.
- `errors`  : 포맷 파싱에 실패할 경우 어떻게 처리할지 지정한다.
    - `raise` :  에러를 반환하도록 한다. (기본값이다.)
    - `coerce` : `NaT`를 반환한다.
    - `ignore` : 에러를 반환하지 않고 원본값을 반환한다.
- `dayfirst`  : 파싱되는 순서를 구체화시킬 때 사용한다
    - `boolean`값을 받는 옵션이다. (기본값은 `False`이다.)
    - `True` 일 경우 date의 가장 우측(day)부터 년/월/일 순서로 파싱된다.
    ex) 10-12-11 → 2011년 12월 10일
- `yearfirst` : 파싱되는 순서를 구체화시킬 때 사용한다
    - `boolean`값을 받는 옵션이다. (기본값은 `False`이다.)
    - `True`일 경우 date의 가장 좌측(year)부터 년/월/일 순서로 파싱된다.
    ex) 10-12-11 → 2010년 12월 11일
    - `dayfirst` 옵션과 동시에 사용할 경우, `yearfirst`가 먼저 적용된다.

<br>

지금부터  `to_datetime`함수를 이용한 예제들을 살펴볼 것이며 각 예제는 함수의 각 옵션의 특징에 관해서 함께 다룰 것이다.

<br>

### <span style="color:navy">1-1) format 옵션<span>

먼저, `format`옵션을 직접 지정하여 변환시킨 예제이다.  
`datetime64`로 변환되었지만 timezone이 설정되지 않은 것을 확인할 수 있다.

```python
df_ex2 = df[['origin_date1', 'origin_date2']].copy()
df_ex2['ex1'] = pd.to_datetime(df_ex2['origin_date1'], format = '%Y-%m-%d %H:%M:%S UTC')
df_ex2['ex2'] = pd.to_datetime(df_ex2['origin_date2'], format = '%d/%m/%Y')

print(df_ex2.head(5), '\n')
print('변형 전 타입 :', df_ex2['origin_date1'].dtypes)
print('변형 후 타입 :', df_ex2['ex1'].dtypes)
print('변형 전 타입 :', df_ex2['origin_date2'].dtypes)
print('변형 후 타입 :', df_ex2['ex2'].dtypes)
```

```
		origin_date1 origin_date2               ex1        ex2
0  2015-10-02 21:12:01 UTC   02/10/2015 2015-10-02 21:12:01 2015-10-02
1  2014-10-26 15:12:00 UTC   26/10/2014 2014-10-26 15:12:00 2014-10-26
2  2014-10-26 15:12:00 UTC   26/10/2014 2014-10-26 15:12:00 2014-10-26
3  2014-10-26 15:12:00 UTC   26/10/2014 2014-10-26 15:12:00 2014-10-26
4  2014-10-26 18:12:00 UTC   26/10/2014 2014-10-26 18:12:00 2014-10-26 

변형 전 타입 : object
변형 후 타입 : datetime64[ns]
변형 전 타입 : object
변형 후 타입 : datetime64[ns]
```

<br>

### <span style="color:navy">1-2) infer_datetime_format 옵션<span>

위에서 처럼 직접 지정하여 변환시킬 수 있지만, 기본형식에서 확인한 `infer_datetime_format` 옵션을 지정하면 timestamp의 다양한 포맷중 하나를 자동으로 파싱하여 적용시킬 수도 있다.<br>

아래 예제의 경우  `UTC`옵션을 별도로 지정하지 않았지만, <u>UTC라는 문자열을 통해 timezone을 자동으로 파싱</u>한 것을 확인할 수 있다. <br>

반면, UTC문자열이 없는 origin_date2는 timezone이 설정되지 않고 자동으로 파싱된 것을 확인할 수 있다. 더불어, 다른 정수들을 자동으로 파싱하여 원본이 D/M/Y 포맷임을 자동으로 파싱하여 적용한 것을 확인할 수 있다. 

```python
# infer_datetime_format : 여러 datetime 유명 포맷중에서 파싱해서 자동으로 변환
df_ex3 = df[['origin_date1', 'origin_date2']].copy()
df_ex3['ex1'] = pd.to_datetime(df_ex3['origin_date1'], infer_datetime_format = True)
df_ex3['ex2'] = pd.to_datetime(df_ex3['origin_date2'], infer_datetime_format = True)

print(df_ex3.head(5), '\n')
print('변형 전 타입 :', df_ex3['origin_date1'].dtypes)
print('변형 후 타입 :', df_ex3['ex1'].dtypes)
print('변형 전 타입 :', df_ex3['origin_date2'].dtypes)
print('변형 후 타입 :', df_ex3['ex2'].dtypes)
```

```
		origin_date1 origin_date2                     ex1        ex2
0  2015-10-02 21:12:01 UTC   02/10/2015 2015-10-02 21:12:01+00:00 2015-02-10
1  2014-10-26 15:12:00 UTC   26/10/2014 2014-10-26 15:12:00+00:00 2014-10-26
2  2014-10-26 15:12:00 UTC   26/10/2014 2014-10-26 15:12:00+00:00 2014-10-26
3  2014-10-26 15:12:00 UTC   26/10/2014 2014-10-26 15:12:00+00:00 2014-10-26
4  2014-10-26 18:12:00 UTC   26/10/2014 2014-10-26 18:12:00+00:00 2014-10-26 

변형 전 타입 : object
변형 후 타입 : datetime64[ns, UTC]
변형 전 타입 : object
변형 후 타입 : datetime64[ns]
```

<br>

### <span style="color:navy">1-3) to_datetime함수에서 format 스트링을 명시해야하는 이유<span>

그리고, `to_datetime` 함수의 경우 위에서 언급한 파싱관련 옵션을 사용하지 않고도 datetime64형식의 timestamp로 변환하는 것이 가능하다. 하지만 해당 방법은 가능하면 추천하지 않는다.

```python
df_ex1 = df[['origin_date1']].copy()
df_ex1['ex1'] = pd.to_datetime(df_ex1['origin_date1']) # Here!

print(df_ex1.head(5), '\n')
print('변형 전 타입 :', df_ex1['origin_date1'].dtypes)
print('변형 후 타입 :', df_ex1['ex1'].dtypes)
```

```
              origin_date1                       ex1
0  2015-10-02 21:12:01 UTC 2015-10-02 21:12:01+00:00
1  2014-10-26 15:12:00 UTC 2014-10-26 15:12:00+00:00
2  2014-10-26 15:12:00 UTC 2014-10-26 15:12:00+00:00
3  2014-10-26 15:12:00 UTC 2014-10-26 15:12:00+00:00
4  2014-10-26 18:12:00 UTC 2014-10-26 18:12:00+00:00

변형 전 타입 : object
변형 후 타입 : datetime64[ns, UTC]
```

<br>

왜냐하면, `format` 혹은 `infer_datetime_format`옵션을  통해 format 스트링을 전달하지 않을 경우, 코드의 퍼포먼스가 감소되기 때문이다. <br>

아래의 예제에서 옵션을 사용하지 않은 경우 처리시간이 가장 긴 것을 확인할 수 있으며, timestamp의 포맷을 `format` 옵션을 이용하여 직접 지정한 경우 가장 빠른 것을 확인할 수 있다. 이러한 격차는 데이터 양이 늘어날수록 더 벌어지기 때문에, 사이즈가 큰 데이터의 object를 datetime64로 변환시키고자 할 경우에는 format스트링을 직접 지정해주는 것을 권장한다.

```python
from datetime import datetime
ex = df[['origin_date1']].copy()

# 옵션 x
t1 = datetime.now()
ex['ex1'] = pd.to_datetime(ex['origin_date1'])
t1 = datetime.now() - t1

# format 옵션
t2 = datetime.now()
ex['ex2'] = pd.to_datetime(ex['origin_date1'], format = '%Y-%m-%d %H:%M:%S UTC')
t2 = datetime.now() - t2

# infer_datetime_format 옵션
t3 = datetime.now()
ex['ex3'] = pd.to_datetime(ex['origin_date1'], infer_datetime_format = True)
t3 = datetime.now() - t3

print('옵션 x :', t1)
print('format 옵션 :', t2)
print('infer_datetime_format 옵션 :', t3)
```

```
옵션 x : 0:00:00.094021
format 옵션 : 0:00:00.004001
infer_datetime_format 옵션 : 0:00:00.015003
```

<br>

### <span style="color:navy">1-4) error 옵션<span>

다음으로 `error` 옵션에 관한 예제를 확인해보자.<br>

default값인 `'raise'` 의 경우 2099-99-99라는 날짜는 없기 때문에 `datetime64`로 변환이 불가능하여 오류를 반환시키는 것을 확인할 수 있다.<br>

반면, `'ignore'`혹은 `'coerce'`경우에는 변환이 불가능할 경우에는 각각 불가능한 값만 원본데이터를 반환하거나 `NaT`로 치환시키며 나머지의 경우 변환시켜 출력시킨다. <br>

많은 양의 데이터를 다를 경우 다른 날짜 데이터로 치환하거나,  `error='coerce'` 옵션을 지정하여 `NaT`로 치환시킨 후 `df[df[날짜필드].notnull()]`등으로 필터링하여 해당 row를 제거하는 것을 권장한다.

```python
# error 옵션
ex = pd.DataFrame(['2099-99-99', '2021-01-01'], columns = ['example'])
ex1 = pd.to_datetime(ex['example'], format = '%Y-%m-%d', errors = 'ignore')
ex2 = pd.to_datetime(ex['example'], format = '%Y-%m-%d', errors = 'coerce')

try :
    ex3 = pd.to_datetime(ex['example'], format = '%Y-%m-%d', errors = 'raise')
except ValueError:
    print('ValueError: time data 2099-99-99 doesn\'t match format specified')

print('\n[ errors = \'ignore\' ]\n', ex1)
print('\n[ errors = \'coerce\' ]\n', ex2)
```

```
ValueError: time data 2099-99-99 doesn't match format specified

[ errors = 'ignore' ]
0    2099-99-99
1    2021-01-01
Name: example, dtype: object

[ errors = 'coerce' ]
0          NaT
1   2021-01-01
Name: example, dtype: datetime64[ns]
```

### <span style="color:navy">1-5) dayfirst, yearfirst 옵션<span>

마지막으로 `dayfirst`와 `yearfirst`에 관한 예제를 확인해보자.<br>

두 옵션들은 `format`옵션을  사용했을 경우에는 고려할 필요 없지만, `infer_datetime_format` 옵션의 경우 아래와 같이 뭐가 year인지 month인지 day인지 구별하기 어려운 경우에는 지정해주는 것이 권장된다. 하지만, 해당 옵션을 사용할 일은 그래도 많지 않을 것이라 생각되어진다. <br>

아래 예제에서 2010년 12월 11일을 표현할 의도로 다음과 같이 '10-12-11'이라 표현하였다(아마도 이렇게 날짜를 쓰는 나라는 없거나 아주 소수 일 것이다...). 하지만, `infer_datetime_format` 만 지정할 경우(ex2) 의도와 다르게 스페인에서 주로 날짜를 표현하는 방식인 dd-mm-yy로 파싱하여 변환시킨다.<br>

차례대로 year-month-day(방향 : →)로 파싱하기 위해서는 ex3과 같이  `infer_datetime_forma=True` 와 함께 `yearfirst=True`를 같이 지정할 필요가 있다.<br>

그외, `dayfirst=True`를 지정할 경우 오른쪽에서 왼쪽 방향 순서대로 year, month, day를 파싱하는 것을 확인할 수 있다.

```python
ex = ['10-12-11']
ex1 = pd.to_datetime(ex, infer_datetime_format=True)
ex2 = pd.to_datetime(ex, infer_datetime_format=True, dayfirst=True)
ex3 = pd.to_datetime(ex, infer_datetime_format=True, yearfirst=True)

print('[ original ] : ', ex[0])
print('[ infer_datetime_format=True ] : ', ex1[0]) # %m-%d-%y 로 파싱함
print('[ infer_datetime_format=True & dayfirst = True ] : ', ex2[0]) # %d-%m-%y 로 파싱함
print('[ infer_datetime_format=True & yearfirst = True ] : ', ex3[0]) # %y-%m-%d 로 파싱함
```

```
[ original ] :  10-12-11
[ infer_datetime_format=True ] :  2011-10-12 00:00:00
[ infer_datetime_format=True & dayfirst = True ] :  2011-12-10 00:00:00
[ infer_datetime_format=True & yearfirst = True ] :  2010-12-11 00:00:00
```

<br>

---

## <span style="color:navy">Timestamp의 timezone<span>

### <span style="color:navy">2-1) dt.tz_localize<span>

`dt`연산자의 `tz_localize`메소드를 이용하여 <u>timezone이 지정되지 않은 timestamp</u>의 timezone을 지정할 수 있다.  <br>

<u>기존 timezone을 다른 timezone 시간대로 변형하는 것이 아님</u>을 주의하자. <br>

더불어, <u>해당 메소드는  timezone이 지정된 timestamp에 적용할 시 에러를 반환</u>한다.

<br>

다음 예제에서는 `object`타입인 origin_date1필드를 timezone이 지정되지 않은 timestamp로 변형하여 한국의 시간대(KST)로 지정하는 예제이다. 결과에서 KST시간대로 지정되었기 때문에 +09:00을 확인할 수 있다.

```python
df_ex4 = df[['origin_date1']].copy()

# tz_localize, utc옵션을 사용x -> timezone = None
df_ex4['ex1'] = pd.to_datetime(df_ex4['origin_date1'], 
                               format = '%Y-%m-%d %H:%M:%S UTC')
df_ex4['ex2'] = df_ex4['ex1'].dt.tz_localize('Asia/Seoul') 

print(df_ex4[['ex1', 'ex2']].head(5), '\n')
print('변형 후 타입 (utc = False) :', df_ex4['ex1'].dtypes)
print('변형 후 타입 (naive to kst) :', df_ex4['ex2'].dtypes)
```

```
		ex1                       ex2
0 2015-10-02 21:12:01 2015-10-02 21:12:01+09:00
1 2014-10-26 15:12:00 2014-10-26 15:12:00+09:00
2 2014-10-26 15:12:00 2014-10-26 15:12:00+09:00
3 2014-10-26 15:12:00 2014-10-26 15:12:00+09:00
4 2014-10-26 18:12:00 2014-10-26 18:12:00+09:00 

변형 후 타입 (utc = False) : datetime64[ns]
변형 후 타입 (none to kst) : datetime64[ns, Asia/Seoul]

<br>

```

### <span style="color:navy">2-2) dt.tz_convert<span>

`dt`연산자의 `tz_convert`함수는 <u>기존 timezone의 시간대를 다른 timezone의 시간대로 변환</u>시킬 때 사용한다. 예를 들어, UTC시간대를 KST로 변경시킬 수 있으며,  KST에서 GMT로도 변경시킬 수 있다.<br>

더불어, <u>timezone이 세팅되어있을 경우 메소드에 None을 기입하여 설정된 timezone을 None으로 변경</u>시킬 수 있다. 하지만, <u>timezone이 지정되지 않은 timestamp에 해당 메소드를 사용할 경우 에러를 반환</u>한다.<br>

다음 예제에서는 `object`타입인 origin_date1필드를 `utc`옵션을 지정한 `to_datetime`함수를 통해 timezone이 UTC인 새로운 timestamp열을 만들고, 새로 추가된 열의 timezone을 UTC에서 KST에서 변환시켜 새로운 열로 추가한 뒤, 마지막으로 해당 열의 timezone지운 새로운 열을 추가하는 과정이다. 

```python
df_ex4 = df[['origin_date1']].copy()

# tz_convert
df_ex4['ex3'] = pd.to_datetime(df_ex4['origin_date1'], 
                               format = '%Y-%m-%d %H:%M:%S UTC', utc=True)
df_ex4['ex4'] = df_ex4['ex3'].dt.tz_convert('Asia/Seoul') 
df_ex4['ex5'] = df_ex4['ex3'].dt.tz_convert(None) 

print(df_ex4[['ex3', 'ex4']].head(5), '\n')             
print('변형 후 타입 (utc = True) :', df_ex4['ex3'].dtypes) # object -> datetime64(UTC)
print('변형 후 타입 (utc to kst) :', df_ex4['ex4'].dtypes) # UTC -> KST
print('변형 후 타입 (kst to None) :', df_ex4['ex5'].dtypes) # KST -> None
```

```

		ex3                       ex4
0 2015-10-02 21:12:01+00:00 2015-10-03 06:12:01+09:00
1 2014-10-26 15:12:00+00:00 2014-10-27 00:12:00+09:00
2 2014-10-26 15:12:00+00:00 2014-10-27 00:12:00+09:00
3 2014-10-26 15:12:00+00:00 2014-10-27 00:12:00+09:00
4 2014-10-26 18:12:00+00:00 2014-10-27 03:12:00+09:00 

변형 후 타입 (utc = True) : datetime64[ns, UTC]
변형 후 타입 (utc to kst) : datetime64[ns, Asia/Seoul]
변형 후 타입 (kst to None) : datetime64[ns]
```

<br>

---

## <span style="color:navy">Timestamp 배열 생성 : date_range<span>

판다스의 `date_range` 함수를 이용하면,  timestamp 배열의 시계열 데이터를 생성할 수 있다. <br>

`date_range`함수의 기본형식은 다음과 같다.

```
pd.data_range(
		start=..., end=...,
		freq=..., periods=...,
		tz=...
	)
```

- `start` : timestamp배열의 시작 점을 지정한다.
- `end` : timestamp배열의 끝 점을 지정한다.
- `freq` : timestamp배열의 생성 주기를 지정한다. (offset aliases 참조 [링크](https://pandas.pydata.org/pandas-docs/stable/user_guide/timeseries.html#timeseries-offset-aliases))
- `periods` : `freq`에서 지정한 주기 별로 총 몇 개의 timestamp를 만들지 지정한다.
- `tz` : 생성된 timestamp의 timezone을 지정한다.

<br>

다음 예제의 range_ex1은 시작 날짜가 2021년 1월 1일(`start = '2021-01-01'`)이며, 월의 시작일(`freq = 'MS'`)을 주기로 총 10개(`periods = 10`)의  timezone이 KST(`tz = 'Asia/Seoul'`)인 timestamp로 이루어진 배열을 받는다. <br>

range_ex2의 경우 시작날짜는 2020년 1월 1일(`start = '2021-01-01'`)이며, 3달 간격(`freq = '3M'`)으로 총 6개(`periods = 6`) 의 timezone이 UTC(`tz = 'UTC'`)인 timestamp로 이루어진 배열을 받는다.

```python
import pandas as pd
range_ex1 = pd.date_range(start = '2021-01-01',
                          end = None,
                          periods = 10,
                          freq = 'MS',
                          tz = 'Asia/Seoul')

range_ex2 = pd.date_range(start = '2021-01-01',
                          end = None,
                          periods = 6,
                          freq = '3M',
                          tz = 'UTC')
print(range_ex1)
print()
print(range_ex2)
```

```
DatetimeIndex(['2021-01-01 00:00:00+09:00', '2021-02-01 00:00:00+09:00',
               '2021-03-01 00:00:00+09:00', '2021-04-01 00:00:00+09:00',
               '2021-05-01 00:00:00+09:00', '2021-06-01 00:00:00+09:00',
               '2021-07-01 00:00:00+09:00', '2021-08-01 00:00:00+09:00',
               '2021-09-01 00:00:00+09:00', '2021-10-01 00:00:00+09:00'],
              dtype='datetime64[ns, Asia/Seoul]', freq='MS')

DatetimeIndex(['2021-01-31 00:00:00+00:00', '2021-04-30 00:00:00+00:00',
               '2021-07-31 00:00:00+00:00', '2021-10-31 00:00:00+00:00',
               '2022-01-31 00:00:00+00:00', '2022-04-30 00:00:00+00:00'],
              dtype='datetime64[ns, UTC]', freq='3M')
```

<br>

---

## References

- [stackoverflow : Why is pandas.to_datetime slow for non standard time format](https://stackoverflow.com/questions/32034689/why-is-pandas-to-datetime-slow-for-non-standard-time-format-such-as-2014-12-31)
- [pandas doc : pandas.to_datetime](https://pandas.pydata.org/docs/reference/api/pandas.to_datetime.html)
- [pandas doc : pandas.date_range](https://pandas.pydata.org/docs/reference/api/pandas.date_range.html)
- [pandas doc  : offset aliases](https://pandas.pydata.org/pandas-docs/stable/user_guide/timeseries.html#timeseries-offset-aliases)
- [pandas doc : pandas.Series.dt.tz_localize](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Series.dt.tz_localize.html)
- [pandas doc : pandas.Series.dt.tz_convert](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Series.dt.tz_convert.html?highlight=dt%20tz_convert#pandas.Series.dt.tz_convert)
- [python doc :  strftime format](https://docs.python.org/3/library/datetime.html#strftime-and-strptime-behavior)
- 오승환, 『파이썬 머신러닝 판다스 데이터 분석』, 정보문화사 (2019)
