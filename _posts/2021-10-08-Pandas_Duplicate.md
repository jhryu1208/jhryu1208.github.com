---
layout: post
title:  "[Pandas] 데이터전처리 :중복 데이터 처리"
subtitle: "[Pandas] 데이터전처리 : 중복 데이터 처리"
categories: data
tags: pandas
comments: true
mathjax: true
---
#### Contents
- [중복 데이터 확인 : duplicated](#중복-데이터-확인--duplicated)
- [중복 데이터 처리 : drop_duplicates](#중복-데이터-처리--drop_duplicates)

본 포스팅은 중복 데이터의 전처리에 관하여 정리하였습니다.

<br>

---

## <span style="color:navy">중복 데이터 확인 : duplicated<span>

중복 데이터의 경우 `duplicated`메소드를 이용하여 확인할 수 있다. 하지만 해당 메소드를 이용해서 데이터프레임의 중복을 처리할 수는 없다. `duplicated`메소드는 <u>boolean Series를 반환</u>하며, <u>중복되는 행의 경우 </u>`True`, <u>중복되지 않는 행에 대해서는 </u>`False`를 반환한다. <br>

다음은 `duplicated`메소드의 기본 형식이다. 

```python
DataFrame.duplicated( keep = 'first'|'last'|False, subset = ... )
```

- `keep`
    - 중복되는 전과 후의 레코드 중 무엇을 중복으로 처리할지(`True`로 반환할지) 지정한다.
    - `'first'` : 두번째에 위치된 값을 중복(`True`)으로 처리한다. 만약 `keep`옵션을 따로 지정하지 않을 시 해당 옵션이 default이다.
    - `'last'` : 첫번째에 위치된 값을 중복(`True`)으로 처리한다.
    - `False` :  전후 값을 모두 중복(`True`)으로 처리한다.
- `subset`
    - 데이터 프레임에서 중복을 식별하기 위해 기준이 되는 컬럼들을 지정한다.
    - 단일 컬럼의 경우 상관 없지만 <u>기준을 2개 이상으로 지정할 경우 리스트로 묶어서 전달</u>한다.
    - <u>선택적으로 사용</u>되는 옵션이다.

<br>

예제로 사용될 데이터프레임을 정의한다.

```python
import pandas as pd

df \
= pd.DataFrame([['a', 2, 3], 
                ['a', 2, 3], 
                ['b', 3, 4], 
                ['c', 3, 5]], 
               columns = ['c1', 'c2', 'c3'], index = ['d0', 'd1', 'd2', 'd3'])
```

<br>

다음은 `keep` 옵션 사용 예제이다. `'first'`로 지정하였을 때,  먼저 발생된 값은 중복이 아닌 것으로 처리되며, 그 뒤에 발생한 값을 중복값으로 처리한 것을 확인할 수 있다. `'last'`의 경우에는 반대이다. 하지만, `False`인 경우에는 전후값 모두 `True`로 처리되었음을 확인할 수 있다.

```python
is_dup1 = df.duplicated(keep = 'first').rename('is duplicated?')
is_dup1 = df.join(is_dup1.to_frame())
print('\n[ keep=\'first\' 옵션 (default)]\n', is_dup1)

is_dup2 = df.duplicated(keep = 'last').rename('is duplicated?')
is_dup2 = df.join(is_dup2.to_frame())
print('\n[ keep=\'last\' 옵션]\n', is_dup2)

is_dup3 = df.duplicated(keep = False).rename('is duplicated?')
is_dup3 = df.join(is_dup3.to_frame())
print('\n[ keep=False 옵션 ]\n', is_dup3)
```

```
[ keep='first' 옵션 (default)]
    c1  c2  c3  is duplicated?
d0  a   2   3           False
d1  a   2   3            True
d2  b   3   4           False
d3  c   3   5           False

[ keep='last' 옵션]
    c1  c2  c3  is duplicated?
d0  a   2   3            True
d1  a   2   3           False
d2  b   3   4           False
d3  c   3   5           False

[ keep=False 옵션 ]
    c1  c2  c3  is duplicated?
d0  a   2   3            True
d1  a   2   3            True
d2  b   3   4           False
d3  c   3   5           False
```

<br>

다음은 `subset`옵션의 사용 예제이다. 첫번째 예제의 경우 c2열을 기준으로 중복을 판단하지만, 두번째 예제의 경우 c2와 c3열을 모두 고려하여 중복을 판단하는 것을 확인할 수 있다. 

```python
## 중복 데이터 확인 (특정 컬럼 기준 1개)
is_dup4 = df.duplicated(subset='c2')
is_dup4 = df['c2'].to_frame().join(is_dup4.to_frame())
print('\n[ 특정 열 기준 (1개) ]\n', is_dup4)

## 중복 데이터 확인 (특정 컬럼 기준 2개 이상)
is_dup5 = df.duplicated(subset=['c2', 'c3'])
is_dup5 = df[['c2', 'c3']].join(is_dup5.to_frame())
print('\n[ 특정 열 기준 (2개 이상) ]\n', is_dup5)
```

```
[ 특정 열 기준 (1개) ]
     c2      0
d0   2  False
d1   2   True
d2   3  False
d3   3   True

[ 특정 열 기준 (2개 이상) ]
     c2  c3      0
d0   2   3  False
d1   2   3   True
d2   3   4  False
d3   3   5  False
```

<br>

다음은 두 옵션을 모두 사용했을 때의 예제이다.

```python
is_dup6 = df.duplicated(subset=['c2', 'c3'], keep = False)
is_dup6 = df[['c2', 'c3']].join(is_dup6.to_frame())
print('\n[ 특정 열 기준 (2개 이상) + keep=False 옵션 ]\n', is_dup6)
```

```
[ 특정 열 기준 (2개 이상) + keep=False 옵션 ]
     c2  c3      0
d0   2   3   True
d1   2   3   True
d2   3   4  False
d3   3   5  False
```

<br>

## <span style="color:navy">중복 데이터 처리 : drop_duplicates<span>

중복 데이터의 경우 `duplicated`메소드를 이용하여 해당 반환값이 False인 값만 필터링해도 괜찮지만, `drop_duplicates`메소드를 이용하면 두 과정을 한꺼번에 처리할 수 있다.  <br>

다음은 `drop_duplicates`메소드의 기본 형식이다.

```python
DataFrame.drop_duplicates(  	keep = 'first'|'last'|False,
				subset = ..., 
				inplace= True|False )
```

- `keep` : `duplicated`메소드의 `keep`옵션과 동일한다.
- `subset` : `duplicated`메소드의 `subset` 옵션과 동일하다.
- `inplace` : `True`로 지정할 경우 원본 값을 변경한다. 기본 지정값은 `False`이다.

<br>

다음은 `drop_duplicates`메소드의 `keep`옵션에 관한 예제이다.  `duplicated`메소드와 옵션의 사용법이 동일하기 때문에 `duplicated`메소드의 반환결과가 `True`인 값들이 `drop_duplicated`를 통해 제거되어 NaN으로 표시되는 것을 확인할 수 있다.<br>더불어, keep=False로 지정할 경우 중복된 레코드 중 하나를 제거하는 것이 아닌 모두 제거하는 것을 확인할 수 있다.

```python
## 중복 데이터 제거 first (전체 컬럼 기준)
is_dup1 = df.duplicated(keep = 'first').rename('is duplicated?')
is_drop_dup1 = df.drop_duplicates(keep = 'first')
result_dup1 = is_drop_dup1.join(is_dup1.to_frame(), how = 'outer')

print('\n[ keep=\'first\' 옵션 (default)]\n', result_dup1)

## 중복 데이터 제거 last (전체 컬럼 기준)
is_dup2 = df.duplicated(keep = 'last').rename('is duplicated?')
is_drop_dup2 = df.drop_duplicates(keep = 'last')
result_dup2 = is_drop_dup2.join(is_dup2.to_frame(), how = 'outer')

print('\n[ keep=\'last\' 옵션 ]\n', result_dup2)

## 중복 데이터 제거 False (전체 컬럼 기준)
is_dup3 = df.duplicated(keep = False).rename('is duplicated?')
is_drop_dup3 = df.drop_duplicates(keep = False)
result_dup3 = is_drop_dup3.join(is_dup3.to_frame(), how = 'outer')

print('\n[ keep=\'last\' 옵션 ]\n', result_dup3)
```

```
[ keep='first' 옵션 (default)]
      c1   c2   c3  is duplicated?
d0    a  2.0  3.0           False
d1  NaN  NaN  NaN            True
d2    b  3.0  4.0           False
d3    c  3.0  5.0           False

[ keep='last' 옵션 ]
      c1   c2   c3  is duplicated?
d0  NaN  NaN  NaN            True
d1    a  2.0  3.0           False
d2    b  3.0  4.0           False
d3    c  3.0  5.0           False

[ keep='last' 옵션 ]
      c1   c2   c3  is duplicated?
d0  NaN  NaN  NaN            True
d1  NaN  NaN  NaN            True
d2    b  3.0  4.0           False
d3    c  3.0  5.0           False
```

<br>

다음은 `subset`옵션에 관한 예제이다. `keep`과 마찬가지로 `duplicated`메소드와 사용법이 똑같으며, `duplicated`메소드가 `True`를 반환한 레코드가 삭제되는 것을 확인할 수 있다.

```python
## 중복 데이터 확인 (특정 컬럼 기준 1개)
is_dup4 = df.duplicated(subset='c2').rename('is_duplicated?')
is_drop_dup4 = df.drop_duplicates(subset = 'c2')
result_dup4 = is_drop_dup4.join(is_dup4, how = 'outer')

print('\n[ 특정 열 기준 (1개) ]\n', result_dup4)

## 중복 데이터 확인 (특정 컬럼 기준 2개)
is_dup5 = df.duplicated(subset=['c2', 'c3']).rename('is_duplicated?')
is_drop_dup5 = df.drop_duplicates(subset = ['c2', 'c3'])
result_dup5 = is_drop_dup5.join(is_dup5, how = 'outer')

print('\n[ 특정 열 기준 (2개 이상) ]\n', result_dup5)
```

```
[ 특정 열 기준 (1개) ]
      c1   c2   c3  is_duplicated?
d0    a  2.0  3.0           False
d1  NaN  NaN  NaN            True
d2    b  3.0  4.0           False
d3  NaN  NaN  NaN            True

[ 특정 열 기준 (2개 이상) ]
      c1   c2   c3  is_duplicated?
d0    a  2.0  3.0           False
d1  NaN  NaN  NaN            True
d2    b  3.0  4.0           False
d3    c  3.0  5.0           False
```

<br>

다음은 언급한 옵션을 모두 사용했을 때의 예제이다.

```python
df.drop_duplicates(	subset = ['c2', 'c3'], 
			keep = False, 
			inplace = True	)

print('\n[ 특정 열 기준 (2개 이상) &  keep=\'last\' ]\n', df)
```

```
[ 특정 열 기준 (2개 이상) &  keep='last' ]
    c1  c2  c3
d2  b   3   4
d3  c   3   5
```

<br>

---

## <span style="color:navy">References<span>

- [pandas drop_duplicated](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.duplicated.html)
- [pandas drop_duplicates](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.drop_duplicates.html)
- 오승환, 『파이썬 머신러닝 판다스 데이터 분석』, 정보문화사 (2019)
