---
layout: post
title:  "[Pandas] 시계열 데이터 : Period"
subtitle: "[Pandas] 시계열 데이터 : Period"
categories: data
tags: pandas
comments: true
mathjax: true
---
#### Contents
- [Timestamp To Period : to_period함수](#timestamp-to-period--to_period함수)
- [Period 배열생성 : period_range](#period-배열생성--period_range)

본 포스팅은 카테고리 pandas의  period관련 함수에 관하여 정리하였습니다.

<br>

---

## <span style="color:navy">Timestamp To Period : to_period함수<span>

`to_period` 함수를 이용하여 timestamp객체를 일정한 기간을 나타내는 `period`객체로 변환시킬 수 있다. 이를 통해 시간 단위로 로깅되는 데이터를 연 단위 혹은 주 단위 등 특정 기간 별로 집계 혹은 연산하는데 유용다.  <br>

`DatetimeArray`혹은 `DatetimeIndex`에 사용 가능하며, 함수로 `DatetimeArray`와 `DatetimeIndex`를 변환시킬 경우 각각 `PeriodArray`와 `PeriodIndex`를 반환한다.  <br>

더불어, 함수의 `freq` 옵션을 이용하여 변환하고자 하는 기간의 기준([참조](https://pandas.pydata.org/docs/user_guide/timeseries.html#timeseries-offset-aliases))을 설정할 수 있다. 

<br>

다음의 예제를 통해 `to_period`함수를 이용하여 timstamp객체를 `period`로 변환시켜보자.<br>

먼저, 예제에 사용할 데이터를 준비한다. <br>

사용하고자 하는 데이터의 날짜/시간 열이 object이기에 timestamp로  먼저 변환시킨다. 그리고, 

```python
import pandas as pd

df = pd.read_csv('./datetime_ex.csv')
# origin_date1필드를 timestmap객체로 변환
df['origin_date1']=pd.to_datetime(df['origin_date1'],
				  infer_datetime_format=True,
				  utc=True) 

df.head(5)
```

```
	   	origin_date1 origin_date2 origin_date3
0 2015-10-02 21:12:01+00:00   02/10/2015     02/10/15
1 2014-10-26 15:12:00+00:00   26/10/2014     26/10/14
2 2014-10-26 15:12:00+00:00   26/10/2014     26/10/14
3 2014-10-26 15:12:00+00:00   26/10/2014     26/10/14
4 2014-10-26 18:12:00+00:00   26/10/2014     26/10/14
```

<br>

다음으로 `DatetimeArray`와 `DatetimeIndex`에 대한 사용 예제를 모두 확인하기 위하여 위에서 생성한 df의 origin_date1열을 인덱스로 지정하는 케이스와 배열로 변환한 케이스를 준비하였다.

```python
df1 = df.sample(n=5).copy()
df2 = df.sample(n=5).copy()

# DatetimeIndex
df1.set_index('origin_date1', inplace=True)
x = df1.index
print(type(x))
print(x)

# DatetimeArray
y = df2['origin_date1'].array

print(type(y))
print(y)
```

```
<class 'pandas.core.indexes.datetimes.DatetimeIndex'>
DatetimeIndex(['2015-10-28 16:12:56+00:00', '2015-10-18 09:12:41+00:00',
               '2015-10-10 12:12:36+00:00', '2015-10-07 15:12:52+00:00',
               '2015-10-29 09:12:06+00:00'],
              dtype='datetime64[ns, UTC]', name='origin_date1', freq=None)

<class 'pandas.core.arrays.datetimes.DatetimeArray'>
<DatetimeArray>
['2015-10-04 16:12:04+00:00', '2015-10-08 15:12:38+00:00',
 '2015-10-29 14:12:31+00:00', '2015-10-20 18:12:35+00:00',
 '2015-10-09 16:12:54+00:00']
Length: 5, dtype: datetime64[ns, UTC]
```

<br>

위에서 생성한 `DatetimeArray`와 `DatetimeIndex`에 `to_period` 함수를 적용한 결과 각각 `PeriodIndex`와 `PeriodArray` 변환된 것을 확인할 수 있다. 더불어, `freq = 'D'`로 지정하였기 때문에 timestamp의 기간에 속하는 Day에대한  period객체로 변환된 것을 확인할 수 있다.

```python
# DatetimeIndex to PeriodIndex
print(x.to_period(freq='D'))

# DatetimeArray to PeriodArray
print(y.to_period(freq='D'))
```

```
PeriodIndex(['2015-10-01', '2015-10-10', '2015-10-11', '2015-10-02',
             '2015-10-15'],
            dtype='period[D]', name='origin_date1')

<PeriodArray>
['2015-10-09', '2015-10-28', '2014-10-31', '2015-10-28', '2015-10-04']
Length: 5, dtype: period[D]
```

<br>

`freq`옵션은 위에서 일 단위(D) 말고도 주 단위(W), 월  단위(M), 분기 단위(Q), 연 단위(A) 등의 alias를 지정할 수 있다. 

```python
print('\n[ 일 단위 : D ]\n', x.to_period(freq='D')) 
print('\n[ 주 단위 : W ]\n', x.to_period(freq='W')) 
print('\n[ 월 단위 : M ]\n', x.to_period(freq='M')) 
print('\n[ 분기 단위 : Q ]\n', x.to_period(freq='Q')) 
print('\n[ 연 단위 : A ]\n', x.to_period(freq='A'))
```

```
[ 일 단위 : D ]
PeriodIndex(['2015-10-01', '2015-10-10', '2015-10-11', '2015-10-02',
             '2015-10-15'],
            dtype='period[D]', name='origin_date1')

[ 주 단위 : W ]
PeriodIndex(['2015-09-28/2015-10-04', '2015-10-05/2015-10-11',
             '2015-10-05/2015-10-11', '2015-09-28/2015-10-04',
             '2015-10-12/2015-10-18'],
            dtype='period[W-SUN]', name='origin_date1')

[ 월 단위 : M ]
PeriodIndex(['2015-10', '2015-10', '2015-10', '2015-10', '2015-10'], dtype='period[M]', name='origin_date1')

[ 분기 단위 : Q ]
PeriodIndex(['2015Q4', '2015Q4', '2015Q4', '2015Q4', '2015Q4'], dtype='period[Q-DEC]', name='origin_date1')

[ 연 단위 : A ]
PeriodIndex(['2015', '2015', '2015', '2015', '2015'], dtype='period[A-DEC]', name='origin_date1')
```

<br>

---

## <span style="color:navy">Period 배열생성 : period_range<span>

판다스의 `period_range`함수는 이전에 확인하였던 `date_range`함수와 비슷하다. 하지만, `date_range`함수는 timestamp배열을 만드는 반면, `period_range`함수는 period배열을 만들며, `PeriodIndex`를 반환한다.

<br>

`period_range`함수의 기본형식은 다음과 같다.

```python
pd.data_range(
		start=..., end=...,
		freq=..., periods=...,
		name=...
	)
```

- `start` : period배열의 시작 점을 지정한다.
- `end` : period배열의 끝 점을 지정한다.
- `freq` : period배열의 기간 길이를 지정한다.
- `periods` : `freq`에서 지정한 주기 별로 총 몇 개의 period의 수를 만들지 지정한다.
- `name` : 반환될 `PeriodIndex`이름을 지정한다.

<br>

다음 예제를 확인해보자.<br>

pr_d의 경우, period배열의 시작 날짜가 2021-10-01(`start = '2021-10-01'`)이며, 일별 간격(`freq = 'D'`)으로 총 10개(`periods = 10`)의 period를 생성한다.<br>

pr_m의 경우, 2021-01-01 부터 2021-12-01 사이(`start = '2021-01-01', end = '2021-12-01'`)의 period배열을 월 간격(`freq = 'M'`)으로 생성한다. 더불어, 월 간격으로 생성하기 때문에 배열 내의 각 원소는 년-월 단위로 이루어진 것을 확인할 수 있다.<br>

마지막으로 pr_2y의 경우, 1997-01-01부터(`start = '1997-01-01'`) 연 간격으로 총 10개(`periods = 10`)의 원소를 가진 배열을 생성한다. 하지만, `freq`옵션에 `A`가 아닌 `2A`를 지정해주었기 때문에 2년 간격으로 이루어진 period배열을 생성한다.

```python
pr_d = pd.period_range(start = '2021-10-01',
                       end = None,
                       periods = 10,
                       freq = 'D',
                       name = 'freq_D_example')

print('\n[ 일 단위로 period 생성 ]')
print(pr_d)

pr_m = pd.period_range(start = '2021-01-01',
                       end = '2021-12-01',
                       freq = 'M',
                       name = 'freq_M_example')
print('\n[ 월 단위로 period 생성 ]')
print(pr_m)

pr_2y = pd.period_range(start = '1997-01-01',
                       end = None,
                       periods = 10,
                       freq = '2A',
                       name = 'freq_Y_example')
print('\n[ 2년 단위로 period 생성 ]')
print(pr_2y)
```

```
[ 일 단위로 period 생성 ]
PeriodIndex(['2021-10-01', '2021-10-02', '2021-10-03', '2021-10-04',
             '2021-10-05', '2021-10-06', '2021-10-07', '2021-10-08',
             '2021-10-09', '2021-10-10'],
            dtype='period[D]', name='freq_D_example')

[ 월 단위로 period 생성 ]
PeriodIndex(['2021-01', '2021-02', '2021-03', '2021-04', '2021-05', '2021-06',
             '2021-07', '2021-08', '2021-09', '2021-10', '2021-11', '2021-12'],
            dtype='period[M]', name='freq_M_example')

[ 2년 단위로 period 생성 ]
PeriodIndex(['1997', '1999', '2001', '2003', '2005', '2007', '2009', '2011',
             '2013', '2015'],
            dtype='period[2A-DEC]', name='freq_Y_example')
```

<br>

---

## <span style="color:navy">References<span>

- [pandas doc : pandas.to_period](https://pandas.pydata.org/docs/reference/api/pandas.Series.dt.to_period.html)
- [pandas doc : pandas.period_range](https://pandas.pydata.org/docs/reference/api/pandas.period_range.html)
- 오승환, 『파이썬 머신러닝 판다스 데이터 분석』, 정보문화사 (2019)
