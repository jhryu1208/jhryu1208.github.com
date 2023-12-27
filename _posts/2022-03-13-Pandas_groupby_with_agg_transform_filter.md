---
layout: post
title: "[Pandas] 데이터 그룹 집계2 : agg, transform, filter"
subtitle: "[Pandas] 데이터 그룹 집계2 : agg, transform, filter"
categories: data
tags: pandas
comments: true
mathjax: true
---
#### Contents
- [agg 메소드](#agg-메소드)
  - [1-1) 모든열에 하나의 함수 적용](#1-1-모든열에-하나의-함수-적용)
  - [1-2) 하나의 열에 여러 개의 집계 함수 적용](#1-2-하나의-열에-여러-개의-집계-함수-적용)
  - [1-3) 열마다 다른 종류의 집계 함수 적용](#1-3-열마다-다른-종류의-집계-함수-적용)
  - [1-4) 함수에 인자를 전달하는 경우](#1-4-함수에-인자를-전달하는-경우)
- [transform 메소드](#transform-메소드)
- [filter 메소드](#filter-메소드)

해당 포스팅에서는 pandas의 `agg`, `transform`, 그리고 `filter`에 대해서 정리하였습니다.

<br>

---

## <span style="color:navy">agg 메소드<span>

`agg`는 [예전 포스팅](https://jhryu1208.github.io/data/2022/02/27/Pandas_Apply1/)에서 소개한 `apply`메소드와 비슷한 작업을 수행하며, 사용자 정의 함수(UDF)도 메소드에 넘겨 사용할 수 있다. 더불어, 둘 다 DataFrameGroupBy객체에서도 사용할 수 있다. 

하지만, `agg`메소드는 `appyly`메소드와 달리 **다수의 함수를 동시에 적용할 수 있다는 점**에서 차이를 보인다.  즉, `apply`메소드를 여러번 사용하지 않아도 `agg`메소드 하나로 간결하게 끝낼 수 있다.

다음은 `agg`메소드의 기본 format이다.

```python
DataFrameGroupBy.agg(arg, *args, **kwargs)
```

- `func` ( ***function, str, list or dict** )*
    - 데이터 집계 값을 반환하는 사용자 정의 함수
    - 동시에 여러 개의 함수를 일괄 적용할 때는 **list로 묶어서 함수**를 전달한다.
    - 열마다 다른 종류의 함수를 적용하려면 `**{열: 함수}`형태의 dict**를 전달한다.
- `*args` : Positional arguments to pass to func
- `**kwargs` : Keyword arguments to pass to func

<br>

### <span style="color:navy">1-1) 모든열에 하나의 함수 적용<span>

다음은 [저번 포스팅](https://jhryu1208.github.io/data/2022/03/12/Pandas_groupby/)에서 보았던 데이터에서  타수(at_bat), hits(안타), runs(득점)을 추가한 데이터이다. 그리고 해당 데이터에서 `agg`메소드를 이용해서 각 팀별 타자의 평균 타수, 안타 수, 득점 수 집계하였다. 

하지만, `groupby`로 인해 반환된 DataFrameGroupBy클래스가 내장하고 있는 `mean`메소드를 통해서 이용하는 것이 좀 더 간략하다.  물론, `apply`와 `np.mean` 조합으로도 도출할 수 있다.

```python
mean_df  = df.groupby('team').agg('mean')
# mean_df  = df.groupby('team').mean() 이렇게 사용해도 동일!
# mean_df  = df.groupby('team').apply(np.mean) 요렇게 사용해도 동일!
print(mean_df)
```

| team    |   at_bat |    hits |   home_runs |    runs |
|:--------|---------:|--------:|------------:|--------:|
| DOOSAN  |  175     | 46.9286 |     3.92857 | 26.3571 |
| HANWHA  |  130.75  | 30.9444 |     2.22222 | 16.6667 |
| KIA     |  156.194 | 38.7097 |     2.12903 | 18.3871 |
| KIWOOM  |  158.032 | 40.8065 |     2.93548 | 23.4516 |
| LG      |  158.333 | 39.6    |     3.66667 | 21.8    |
| LOTTE   |  161.581 | 44.9355 |     3.45161 | 23.4516 |
| NC      |  141.412 | 36.9118 |     5       | 20.6765 |
| SAMSUNG |  166.862 | 44.5517 |     4.58621 | 24.5517 |
| SSG     |  174.929 | 45.6429 |     6.60714 | 26.9643 |
| kt      |  178.148 | 47.2593 |     3.92593 | 26.6296 |

이와 같은 케이스에서는 `agg`메소드는 큰 이점을 가지고 있지 않는다. 하지만, 다음에 소개될 예제에서는 위에서 언급한 `agg`의 장점에 대해서 확인할 수 있다.

<br>

### <span style="color:navy">1-2) 하나의 열에 여러 개의 집계 함수 적용<span>

`agg`메소드는 여러 함수가 묶인 리스트를 전달 받으면, **각 열에 전달한 모든 함수를 적용**시킬 수 있다. 다음의 예제는 타수(at_bat)와 안타 수(hits)의 평균과 총 수를 MultiColumn으로 보여주는 것을 확인할 수 있다. 

```python
multi_func_df  = df.groupby('team').agg(['mean', 'sum'])
print(multi_func_df)
```

| team    |   ('at_bat', 'mean') |   ('at_bat', 'sum') |   ('hits', 'mean') |   ('hits', 'sum') |   ('home_runs', 'mean') |     ... |
|:--------|---------------------:|--------------------:|-------------------:|------------------:|------------------------:|--------:|
| DOOSAN  |              175     |                4900 |            46.9286 |              1314 |                 3.92857 | ... |
| HANWHA  |              130.75  |                4707 |            30.9444 |              1114 |                 2.22222 | ... |
| KIA     |              156.194 |                4842 |            38.7097 |              1200 |                 2.12903 | ... |
| KIWOOM  |              158.032 |                4899 |            40.8065 |              1265 |                 2.93548 |... |
| LG      |              158.333 |                4750 |            39.6    |              1188 |                 3.66667 |   ... |
| LOTTE   |              161.581 |                5009 |            44.9355 |              1393 |                 3.45161 | ...|
| NC      |              141.412 |                4808 |            36.9118 |              1255 |                 5       |... |
| SAMSUNG |              166.862 |                4839 |            44.5517 |              1292 |                 4.58621 | ... |
| SSG     |              174.929 |                4898 |            45.6429 |              1278 |                 6.60714 | ... |
| kt      |              178.148 |                4810 |            47.2593 |              1276 |                 3.92593 | ... |

<br>

### <span style="color:navy">1-3) 열마다 다른 종류의 집계 함수 적용<span>

또한, `agg`메소드에 `{열: 함수}`형태의 dict를 전달하면 **각 열 마다 지정한 함수를 적용**할 수 있다. 다음은 총 타수(at_bat), 평균 안타 수(hits), 그리고 홈런(home_runs)을 친 고유 타자 수를 집계한다. 

혹시 만약 dict에 동일 key가 중복으로 다른 함수와 명시된다면 둘 다 적용되는 것이 아니라 나중에 나온 함수가 적용된다. 

```python
agg_dict = {'at_bat': 'sum', 'hits': 'mean', 'home_runs': 'nunique'}

multi_func_df  = df.groupby('team').agg(agg_dict)
print(multi_func_df)
```

| team    |   at_bat |    hits |   home_runs |
|:--------|---------:|--------:|------------:|
| DOOSAN  |     4900 | 46.9286 |          11 |
| HANWHA  |     4707 | 30.9444 |          10 |
| KIA     |     4842 | 38.7097 |           9 |
| KIWOOM  |     4899 | 40.8065 |           9 |
| LG      |     4750 | 39.6    |          11 |
| LOTTE   |     5009 | 44.9355 |          11 |
| NC      |     4808 | 36.9118 |          12 |
| SAMSUNG |     4839 | 44.5517 |          11 |
| SSG     |     4898 | 45.6429 |          15 |
| kt      |     4810 | 47.2593 |          12 |

<br>

### <span style="color:navy">1-4) 함수에 인자를 전달하는 경우<span>

다음은 함수에 인자를 전달하는 예제이다.  원본 DataFrame의  열 기준으로  Series를 나누어, 첫 번째 인자로 사용하는 것을 확인할 수 있다. 그리고, Series의 각 Index의 값에 전달된 x, y, z인자를 사용하여 연산을 수행한다. 마지막으로, 연산된 Series가 모두 병합된 DataFrame이 출력되는 것을 확인할 수 있다. (이 과정은 `apply`메소드와 동일하다.)

```python
df = pd.DataFrame([[1, 2, 3],[4, 5, 6],[7, 8, 9]],
                columns = ['c1', 'c2', 'c3'],
                index = ['r1', 'r2', 'r3'])

def power(sr, x, y, z):
    print(f"type:{type(sr)}")
    sr.r1**=x
    sr.r2**=y
    sr.r3**=z

df.agg(power, x=2, y=3, z=4)

print(df)
```

```
type:<class 'pandas.core.series.Series'>
type:<class 'pandas.core.series.Series'>
type:<class 'pandas.core.series.Series'>
```

|    |   c1 |   c2 |   c3 |
|:---|-----:|-----:|-----:|
| r1 |    1 |    4 |    9 |
| r2 |   64 |  125 |  216 |
| r3 | 2401 | 4096 | 6561 |

<br>

---

## <span style="color:navy">transform 메소드<span>

pandas에는 SQL에서의 **`window`함수처럼** 사용할 수 있는 `transform`메소드가 존재한다. 

기본적인 format은 `agg`함수와 유사하다. 

하지만, `transform`메소드는 DataFrame을 지정한 key기준으로 그룹별로 구분하여 각 원소에 함수를 적용하여 반환된 DataFrame의 사이즈는 원본 DataFrame과 동일하다. 즉, **그룹별로 값을 반환하는 것이 아니라 본래 행 인덱스와 열 이름을 기준으로 연산된 결과를 반환**한다.

```python
DataFrameGroupBy.transform(func, *args, **kwargs)
```

<br>

다음은 각 팀에서 각 선수들이 차지하는 타수 비중을 집계하는 과정이다.  

먼저, `transform`메소드를 통해서 팀별 총 타수를 집계하여, 원본 DataFrame의 total_at_bat이라는 열로 추가한다. 그리고, 추가한 팀별 총 타수(total_at_bat)를  각 선수의 타수(at_bat)로 나누어 비중을 계산한다.

이때, `transform`메소드의 결과 팀별로 집계된 총 타수를 원본 DataFrame의 Index를 기준으로 반환하는 것을 확인할 수 있다.

```python
return_df = df[['name', 'team', 'at_bat']]
return_df['total_at_bat'] = return_df.groupby('team')['at_bat'].transform('sum')

return_df['ratio_at_bat'] = \
return_df['at_bat'].div(return_df['total_at_bat'],fill_value=np.nan)

print(return_df.sort_values(by=['team', 'ratio_at_bat'], ascending=[True, False]))
```

|     | name           | team   | at_bat | total_at_bat | ratio_at_bat |
|----:|:---------------|:-------|-------:|-------------:|-------------:|
|   7 | Jose Fernandez | DOOSAN |    540 |         4900 |     0.110204 |
|  16 | Suk Hwan Yang  | DOOSAN |    488 |         4900 |    0.0995918 |
|  19 | Jae Hwan Kim   | DOOSAN |    475 |         4900 |    0.0969388 |
|  20 | Kyoung Min Heo | DOOSAN |    468 |         4900 |    0.0955102 |
|  28 | Kun Woo Park   | DOOSAN |    458 |         4900 |    0.0934694 |
| ... | ...            | ...    |    ... |          ... |          ... |
| 178 | Hong Ku Lee    | kt     |     55 |         4810 |    0.0114345 |
| 182 | Byung Hee Kim  | kt     |     52 |         4810 |    0.0108108 |
| 185 | Min Sub Song   | kt     |     48 |         4810 |   0.00997921 |
| 192 | Min Gook Kang  | kt     |     45 |         4810 |   0.00935551 |
| 194 | Seong Ho Cheon | kt     |     42 |         4810 |   0.00873181 |

<br>

---

## <span style="color:navy">filter 메소드<span>

`filter`메소드는 사용하기에 따라서 SQL의 `HAVING`절 혹은 `QUALIFY`절과 유사한 기능을 수행한다. 왜냐하면, 해당 메소드는 조건 함수를 전달 받으며,  조건을 만족하지 못하는 그룹의 경우 출력에서 제외시키기 기능을 수행하기 때문이다. 

기본 format은 아래와 같다. `agg`, `transform`과 마찬가지로 함수(UDF포함)를 전달 받기 때문에 인수도 같이 전달 받을 수 있다.

```python
DataFrameGroupBy.filter(func, *args, **kwargs)
```

- `func` ( ***function** )*
    - 조건식을 가진 함수를 전달한다.
- `*args` : Positional arguments to pass to func
- `**kwargs` : Keyword arguments to pass to func

<br>

다음은 `filter`메소드를 이용해서 총 타석 수가 4900건 이상인 팀을 필터링하는 예제이다. 

`filter`메소드에 의해서 반환된 DataFrame을 기준으로 각 팀별 총 타석 수의 합을 도출했을 때, 4900건 미만인 팀의 경우 제외된 것을 확인할 수 있다.

```python
# 총 타석 수가 4900 이상인 팀을 필터링한다.
filter_df = df.groupby('team').filter(lambda x: x['at_bat'].sum() >= 4900)

# 필터링된 결과 확인
result_sr = filter_df.groupby('team')['at_bat'].sum()
print(result_sr.sort_values(ascending=False))
```

```
team
LOTTE     5009
DOOSAN    4900
Name: at_bat, dtype: int64
```

<br>

---

## <span style="color:navy">References<span>

- [pandas doc : pandas.core.groupby.DataFrameGroupBy.agg](https://pandas.pydata.org/pandas-docs/version/0.23/generated/pandas.core.groupby.DataFrameGroupBy.agg.html)
- [pandas doc : pandas.core.groupby.DataFrameGroupBy.transform](https://pandas.pydata.org/docs/reference/api/pandas.core.groupby.DataFrameGroupBy.transform.html)
- [pandas doc : pandas.core.groupby.DataFrameGroupBy.filter](https://pandas.pydata.org/docs/reference/api/pandas.core.groupby.DataFrameGroupBy.filter.html?highlight=filter#pandas.core.groupby.DataFrameGroupBy.filter)
- 오승환, 『파이썬 머신러닝 판다스 데이터 분석』, 정보문화사 (2019)

