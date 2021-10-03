---
layout: post
title:  "[Pandas] 데이터전처리 : NaN값 처리"
subtitle: "[Pandas] 데이터전처리 : NaN값 처리"
categories: data
tags: pandas
comments: true
mathjax: true
---
#### Contents
- [누락 데이터 확인](#누락-데이터-확인)
	- [1-1) info 메소드](#1-1-info-메소드)
	- [1-2) value_counts 메소드](#1-2-value_counts-메소드)
	- [1-3) isnull, notnull 메소드](#1-3-isnull-notnull-메소드)
- [누락 데이터 처리](#누락-데이터-처리)
	- [2-1) 제거 : dropna](#2-1-제거--dropna)
	- [2-2) 치환 : fillna](#2-2-치환--fillna)

본 포스팅은 누락데이터의 전처리에 관하여 정리하였습니다.

<br>

---

## <span style="color:navy">누락 데이터 확인<span>

### <span style="color:navy">1-1) info 메소드<span>

`info` 메소드로 데이터프레임의 요약된 정보에는 <u>누락 데이터</u>(`NaN`)<u>가 아닌 유효 데이터의 개수</u>를 알 수 있다. 

- `RangeIndex` : 데이터프레임의 레코드(혹은 행) 수를 출력한다. 다음의 데이터셋에는 총 891개의 데이터 레코드가 존재하는 것을 알 수 있다.
- `Non-Null Count` : 데이터프레임의 필드에서 <u>누락 데이터가 아닌 유효 데이터의 개수</u>를 출력하여 보여준다.
- `Dtype` : 데이터프레임의 필드안에 들어있는 값의 데이터 형식이 무엇인지 확인할 수 있다.
- `dtypes` : 데이터프레임에 존재하는 데이터 형식들의 종류와 수를 확인할 수 있다.

<br>

`Non-Null Count`와 `RangeIndex`의 차이를 통해서 각 데이터 필드에 몇개의 누락 값이 존재하는지 확인할 수 있다.

- age필드의 누락 데이터 수 : 891-714 = 177개
- embarked필드의 누락 데이터 수 : 891-889 = 2개
- deck필드의 누락 데이터 수 : 891-203 = 688개

```python
import seaborn as sns
df = sns.load_dataset('titanic')
df.info()
```

```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 891 entries, 0 to 890
Data columns (total 15 columns):
 #   Column       Non-Null Count  Dtype   
---  ------       --------------  -----   
 0   survived     891 non-null    int64   
 1   pclass       891 non-null    int64   
 2   sex          891 non-null    object  
 3   age          714 non-null    float64 
 4   sibsp        891 non-null    int64   
 5   parch        891 non-null    int64   
 6   fare         891 non-null    float64 
 7   embarked     889 non-null    object  
 8   class        891 non-null    category
 9   who          891 non-null    object  
 10  adult_male   891 non-null    bool    
 11  deck         203 non-null    category
 12  embark_town  889 non-null    object  
 13  alive        891 non-null    object  
 14  alone        891 non-null    bool    
dtypes: bool(2), category(2), float64(2), int64(4), object(5)
memory usage: 80.7+ KB
```

<br>

### <span style="color:navy">(1-2) value_counts 메소드<span>

고유값을 세는 `value_counts` 메소드의 `dropna = False`옵션을 지정해서 특정 필드의 누락 데이터의 수를 카운팅할 수 있다. 해당 옵션을 지정하지 않는다면 `NaN`을 제외한 유효 데이터값의 개수만 카운팅한다.

```python
import seaborn as sns
df = sns.load_dataset('titanic')

dropna_true = df['deck'].value_counts().sort_index()
dropna_false = df['deck'].value_counts(dropna = False).sort_index()

print('[ dropna=True(기본값) ] \n', dropna_true, '\n')
print('[ dropna=False ] \n', dropna_false)
```

```
[ dropna=True(기본값) ] 
A    15
B    47
C    59
D    33
E    32
F    13
G     4
Name: deck, dtype: int64 

[ dropna=False ] 
A       15
B       47
C       59
D       33
E       32
F       13
G        4
NaN    688
Name: deck, dtype: int64
```

<br>

### <span style="color:navy">(1-3) isnull, notnull 메소드<span>

`isnull`, `notnull` 메소드를 활용해서 누락 데이터를 카운팅할 수 있다.

- `isnull()` : 누락 데이터일 경우 `True` 반환, 아닐 경우 `False`반환
- `notnull()` : 유효 데이터일 경우 `True` 반환, 아닐 경우 `False`반환

```python
import pandas as pd
import numpy as np

default_df = pd.DataFrame([[100, 100], [np.nan, np.nan]], 
                          columns = ['isnull()', 'notnull()'])

proc_df = pd.DataFrame() #빈 데이터프레임 생성
proc_df['isnull()'] = default_df['isnull()'].isnull()
proc_df['notnull()'] = default_df['notnull()'].isnull()

print('[ before apply method ]\n', default_df, '\n')
print('[ after apply method ]\n', proc_df)
```

```
[ before apply method ]
    isnull()  notnull()
0     100.0      100.0
1       NaN        NaN 

[ after apply method ]
    isnull()  notnull()
0     False      False
1      True       True
```

<br>

이때, `sum`메소드와 `isnull`혹은 `notnull`메소드를 함께 이용해서 위와 같이 반환된 `True`값을 카운팅하여 데이터프레임의 각 필드에 존재하는 누락값(`NaN`)을 카운팅할 수 있다.

```python
import seaborn as sns
df = sns.load_dataset('titanic')

isnull_sr = df.isnull().sum(axis=0).rename('[ isnull ]')

# boolean객체로 이루어진 데이터프레임 혹은 시리즈의 경우 
# 변수 앞에 ~을 붙여줌으로써 invert시킬 수 있다.
notnull_sr = (~df.notnull()).sum(axis=0).rename('[ notnull ]')

result_df = isnull_sr.to_frame().join(notnull_sr.to_frame())
print(result_df)
```

```
							[ isnull ]  [ notnull ]
survived              0            0
pclass                0            0
sex                   0            0
age                 177          177
sibsp                 0            0
parch                 0            0
fare                  0            0
embarked              2            2
class                 0            0
who                   0            0
adult_male            0            0
deck                688          688
embark_town           2            2
alive                 0            0
alone                 0            0
```

<br>

---

## <span style="color:navy">누락 데이터 처리<span>

### <span style="color:navy">(2-1) 제거 : dropna<span>

`dropna`메소드를 이용해서 데이터프레임의 <u>누락값을 제거</u>할 수 있다. 
더불어,  `dropna`의 기본형식 및 옵션은 다음과 같다.

```python
# 기본형식
DataFrame.dropna(axis=..., how=..., thresh=..., subset=..., inplace=...)
```

- `axis` : 누락값을 가지고있는 row를 삭제할지 column을 삭제할지 결정하기위한 옵션
    - `0 (default)` :   누락값을 가지고있는 <u>row를 삭제</u>한다.
    - `1` : 누락값을 가지고 있는 <u>column을 삭제</u>한다.
- `how`
    - `'any' (default)` : 누락값이 **최소** 하나라도 있을 경우 행 혹은 열을 삭제한다.
    - `'all'` :  **모든** 값이 누락값으로 채워진 행 혹은 열을 삭제한다.
- `thresh` : 특정 필드의 <u>유효값의 수가 지정한 옵션 수 미만</u>일 경우 해당  <u>column을 삭제</u>한다. `axis=1` 옵션과 동시에 사용을 추천한다.
- `subset` : 옵션에 <u>지정한 특정 column</u>의 row 중에서 <u>누락값이 있는 row를 모두 제거</u>한다.

```python
import seaborn as sns
df = sns.load_dataset('titanic')

# 최소 한개 이상의 누락값을 가지고있는 필드 이름과 유효값의 개수로 이루어진 데이터프레임 생성
nan_sr = df.notnull().sum()
nan_sr = nan_sr[nan_sr!=df.shape[0]]
nan_df = df[nan_sr.index]

print('[ 누락값을 가진 필드별 유효값 수 ] \n', dict(nan_sr))
print('\n[ 삭제 이전 데이터 필드 ]\n', nan_df.columns)

## thresh 예시
thresh600_nan_df = nan_df.dropna(axis = 1, thresh = 600)
print('\n[ thresh = 600, 유효값이 600개 미만의 column제거 ]\n', thresh600_nan_df.columns)

thresh800_nan_df = nan_df.dropna(axis = 1, thresh = 800)
print('\n[ thresh = 800, 유효값이 800개 미만의 column제거 ]\n', thresh800_nan_df.columns)

## subset 예시
subset_nan_df = nan_df.dropna(subset=['age'])
print(f'\n[ age필드에 NaN이 있을 경우 레코드 제거 ] \
        \n제거 이전 레코드 수 : {nan_df.shape[0]} \
        \n 제거 이후 레코드 수 : {subset_nan_df.shape[0]}')

subset_nan_df = nan_df.dropna(subset=['age', 'deck'])
print(f'\n[ deck 혹은 age필드에 NaN이 있을 경우 레코드 제거 ] \
        \n제거 이전 레코드 수 : {nan_df.shape[0]} \
        \n 제거 이후 레코드 수 : {subset_nan_df.shape[0]}')
```

```
[ 누락값을 가진 필드별 유효값 수 ] 
 {'age': 714, 'embarked': 889, 'deck': 203, 'embark_town': 889}

[ 삭제 이전 데이터 필드 ]
 Index(['age', 'embarked', 'deck', 'embark_town'], dtype='object')

[ thresh = 600, 유효값이 600개 미만의 column제거 ]
 Index(['age', 'embarked', 'embark_town'], dtype='object')

[ thresh = 800, 유효값이 800개 미만의 column제거 ]
 Index(['embarked', 'embark_town'], dtype='object')

[ age필드에 NaN이 있을 경우 레코드 제거 ]         
제거 이전 레코드 수 : 891         
제거 이후 레코드 수 : 714

[ deck 혹은 age필드에 NaN이 있을 경우 레코드 제거 ]         
제거 이전 레코드 수 : 891         
제거 이후 레코드 수 : 184
```

<br>

### <span style="color:navy">(2-2) 치환 : fillna<span>

`fillna`메소드를 이용해서 데이터프레임의 <u>누락값을 특정 값으로 치환</u>할 수 있다. <br> 더불어,  `fillna`의 기본형식 및 옵션은 다음과 같다.

```python
# 기본형식
# 아래의 옵션외에도 limit과 downcast도 있는데 자주 사용하지는 않는다.
DataFrame.fillna( value=..., method=..., axis=..., inplace=...)
```

- `value` : 누락값을 무엇으로 채울 것인지 지정하는 옵션이다.
- `method`
    - `ffill` or `pad` : 각 필드 별로 누락값 앞에 있는 값으로 누락값을 치환시킨다.
    - `backfill` or `bfill` : 각 필드 별로 누락값 뒤에 있는 값으로 누락값을 치환시킨다.
- `axis` : 누락값의 치환 기준을 지정한다. `method` 옵션과 같이 쓰면 유용하다.
    - `0` or `index` :   누락값을 채우는 기준을 인덱스 방향으로 지정한다.
    - `1` or `columns` : 누락값을 채우는 기준을 열 방향으로 지정한다.

```python
import numpy as np
import pandas as pd

df = \
pd.DataFrame([[1, 10, np.nan, 1000],
              [2, np.nan, 200, 2000],
              [3, np.nan, 300, np.nan],
              [4, 40, np.nan, 4000]])

print('[ 기본 dataframe ]\n', df)

print('\n-----------------------------------\n')

df_fillna0 = df.fillna(value=0)
print('\n[ value 옵션 (정수) ]\n', df_fillna0)

sr = pd.Series({0: 'A', 1:'B', 2:'C', 3:'D'})
df_fillna0 = df.fillna(value=sr)
print('\n[ value 옵션 (시리즈) ]\n', df_fillna0)\

print('\n-----------------------------------\n')

df_fillna1 = df.fillna(method = 'ffill')
print('\n[ ⇩ : method=\'ffill\' or \'pad\' ]\n', df_fillna1)

df_fillna2 = df.fillna(method = 'backfill')
print('\n[ ⇧ : method=\'backfill\' or \'bfill\' ]\n', df_fillna2)

print('\n-----------------------------------\n')

df_fillna3 = df.fillna(method = 'ffill', axis = 1)
print('\n[ ⇨ : method=\'ffill\' & axis=0 ]\n', df_fillna3)

df_fillna4 = df.fillna(method = 'bfill', axis = 1)
print('\n[ ⇦ : method=\'bfill\' & axis=0 ]\n', df_fillna4)
```

```
[ 기본 dataframe ]
   0     1      2       3
0  1  10.0    NaN  1000.0
1  2   NaN  200.0  2000.0
2  3   NaN  300.0     NaN
3  4  40.0    NaN  4000.0

-----------------------------------

[ value 옵션 (정수) ]
   0     1      2       3
0  1  10.0    0.0  1000.0
1  2   0.0  200.0  2000.0
2  3   0.0  300.0     0.0
3  4  40.0    0.0  4000.0

[ value 옵션 (시리즈) ]
   0     1      2       3
0  1  10.0      C  1000.0
1  2     B  200.0  2000.0
2  3     B  300.0       D
3  4  40.0      C  4000.0

-----------------------------------

[ ⇩ : method='ffill' or 'pad' ]
   0     1      2       3
0  1  10.0    NaN  1000.0
1  2  10.0  200.0  2000.0
2  3  10.0  300.0  2000.0
3  4  40.0  300.0  4000.0

[ ⇧ : method='backfill' or 'bfill' ]
   0     1      2       3
0  1  10.0  200.0  1000.0
1  2  40.0  200.0  2000.0
2  3  40.0  300.0  4000.0
3  4  40.0    NaN  4000.0

-----------------------------------

[ ⇨ : method='ffill' & axis=0 ]
     0     1      2       3
0  1.0  10.0   10.0  1000.0
1  2.0   2.0  200.0  2000.0
2  3.0   3.0  300.0   300.0
3  4.0  40.0   40.0  4000.0

[ ⇦ : method='bfill' & axis=0 ]
     0      1       2       3
0  1.0   10.0  1000.0  1000.0
1  2.0  200.0   200.0  2000.0
2  3.0  300.0   300.0     NaN
3  4.0   40.0  4000.0  4000.0
```

<br>

---

## References

- [pandas fillna docs](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.fillna.html)
- [pandas dropna docs](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.dropna.html)
- 오승환, 『파이썬 머신러닝 판다스 데이터 분석』, 정보문화사 (2019)
