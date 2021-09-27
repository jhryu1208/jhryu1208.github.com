---
layout: post
title:  "[Pandas] DataFrame 연산"
subtitle: "[Pandas] DataFrame 연산"
categories: data
tags: pandas
comments: true
mathjax: true
---
#### Contents
- [with 숫자](#with-숫자)
- [with 데이터프레임](#with-데이터프레임)
- [with 시리즈](#with-시리즈)
- [메소드를 이용한 연산](#메소드를-이용한-연산)

본 포스팅은 Pandas의 DataFrame 연산에 관하여 정리하였습니다.

<br>

---

## <span style="color:navy">with 숫자<span>

데이터프레임과 숫자의 연산도 시리즈의 연산과 동일하게 <u>데이터프레임의 모든 원소에 숫자를 연산</u>시킨다. 더불어, 연산 클래스의 결과로 <u>데이터프레임을 반환</u>시킨다.

```python
df = \
pd.DataFrame({'c0':[10, 20, 30], 
              'c1':[40, 50, 60],
              'c2':[70, 80, 90]}, index = ['r0','r1','r2'])

df_add = df+100
df_sub = df-100
df_div = df/100
df_mul = df*100

print('[ 덧셈 ]\n', df_add, '\n', type(df_add), '\n')
print('[ 뺄셈 ]\n', df_sub, '\n', type(df_sub), '\n')
print('[ 나눗셈 ]\n', df_div, '\n', type(df_div), '\n')
print('[ 곱셈 ]\n', df_mul, '\n', type(df_mul), '\n')
```

```
[ 덧셈 ]
     c0   c1   c2
r0  110  140  170
r1  120  150  180
r2  130  160  190 
<class 'pandas.core.frame.DataFrame'> 

[ 뺄셈 ]
    c0  c1  c2
r0 -90 -60 -30
r1 -80 -50 -20
r2 -70 -40 -10 
<class 'pandas.core.frame.DataFrame'> 

[ 나눗셈 ]
     c0   c1   c2
r0  0.1  0.4  0.7
r1  0.2  0.5  0.8
r2  0.3  0.6  0.9 
<class 'pandas.core.frame.DataFrame'> 

[ 곱셈 ]
      c0    c1    c2
r0  1000  4000  7000
r1  2000  5000  8000
r2  3000  6000  9000 
<class 'pandas.core.frame.DataFrame'>
```

<br>

## <span style="color:navy">with 데이터프레임<span>

데이터프레임끼리의 연산의 경우 **동일한 행과 열에 위치한 원소**끼리 계산되어진다. 시리즈 끼리의 계산과 마찬가지로 <u>어느 한쪽에 동일 위치의 원소가 존재하지 않을 경우 </u>`NaN`<u>으로 처리</u>된다. <br>

다음의 예제에서는 각 데이터프레임의 동일 행과 열( [c1행, r1열], [c2행,  r2열], [c3행,  r1열], [c3행,  r2열] )에 위치된 원소의 경우 연산 값을 반환하며, 그외의 동일한 행과 열이 아닐 경우에는 `NaN`으로 처리되어지며, <u>두 데이터프레임의 합집합인 4행 4열의 데이터프레임을 결과로 반환</u>한 것을 확인할 수 있다.

```python
df1 = \
pd.DataFrame({'c0':[10, 20, 30], 
              'c1':[40, 50, 60],
              'c2':[70, 80, 90]}, index = ['r0','r1','r2'])

df2 = \
pd.DataFrame({'c1':[100, 200, 300], 
              'c2':[400, 500, 600],
              'c3':[700, 800, 900]}, index = ['r1','r2','r3'])

df_add = df1+df2
df_sub = df1-df2
df_div = df1/df2
df_mul = df1*df2

print('[ 덧셈 ]\n', df_add, '\n')
print('[ 뺄셈 ]\n', df_sub, '\n')
print('[ 나눗셈 ]\n', df_div, '\n')
print('[ 곱셈 ]\n', df_mul, '\n')
```

```
[ 덧셈 ]
    c0     c1     c2  c3
r0 NaN    NaN    NaN NaN
r1 NaN  150.0  480.0 NaN
r2 NaN  260.0  590.0 NaN
r3 NaN    NaN    NaN NaN 

[ 뺄셈 ]
    c0     c1     c2  c3
r0 NaN    NaN    NaN NaN
r1 NaN  -50.0 -320.0 NaN
r2 NaN -140.0 -410.0 NaN
r3 NaN    NaN    NaN NaN 

[ 나눗셈 ]
    c0   c1    c2  c3
r0 NaN  NaN   NaN NaN
r1 NaN  0.5  0.20 NaN
r2 NaN  0.3  0.18 NaN
r3 NaN  NaN   NaN NaN 

[ 곱셈 ]
    c0       c1       c2  c3
r0 NaN      NaN      NaN NaN
r1 NaN   5000.0  32000.0 NaN
r2 NaN  12000.0  45000.0 NaN
r3 NaN      NaN      NaN NaN
```

<br>

## <span style="color:navy">with 시리즈<span>

데이터프레임은 시리즈와도 연산을 수행할 수 있다. <u>데이터프레임의  행(axis=1)축을 기준으로 모든 행의 열 이름과 시리즈의 인덱스 이름이 동일한 위치인 원소끼리 계산</u>되어진다.  더불어, 다른 계산들과 마찬가지로 짝이 없을 경우 `NaN`으로 처리되어지며, 반환된 결과로 데이터프레임에 없던 열이 추가되어 데이터프레임 객체로 반환되어진다.

```python
df = \
pd.DataFrame({'c0':[10, 20, 30], 
              'c1':[40, 50, 60],
              'c2':[70, 80, 90]}, index = ['r0','r1','r2'])

sr = \
pd.Series({'c1':100, 'c2':200, 'c3':300})

df_add = df+sr
df_sub = df-sr
df_div = df/sr
df_mul = df*sr

print('[ 덧셈 ]\n', df_add, '\n')
print('[ 뺄셈 ]\n', df_sub, '\n')
print('[ 나눗셈 ]\n', df_div, '\n')
print('[ 곱셈 ]\n', df_mul, '\n')
```

```
[ 덧셈 ]
    c0     c1     c2  c3
r0 NaN  140.0  270.0 NaN  -> c1의 모든 행에 100이 더해진 것을 확인할 수 있다. 
r1 NaN  150.0  280.0 NaN  -> c2의 모든 행에 200이 더해진 것을 확인할 수 있다.
r2 NaN  160.0  290.0 NaN 

[ 뺄셈 ]
    c0    c1     c2  c3
r0 NaN -60.0 -130.0 NaN
r1 NaN -50.0 -120.0 NaN
r2 NaN -40.0 -110.0 NaN 

[ 나눗셈 ]
    c0   c1    c2  c3
r0 NaN  0.4  0.35 NaN
r1 NaN  0.5  0.40 NaN
r2 NaN  0.6  0.45 NaN 

[ 곱셈 ]
    c0      c1       c2  c3
r0 NaN  4000.0  14000.0 NaN
r1 NaN  5000.0  16000.0 NaN
r2 NaN  6000.0  18000.0 NaN
```

<br>

## <span style="color:navy">메소드를 이용한 연산<span>

시리즈와 동일한 메소드를 이용하여 데이터프레임의 연산을 수행할 수 있다. 물론, 시리즈와 데이터프레임의 연산에도 동일한 메소드를 이용할 수 있다. 

- 객체 순서의 상관없이 항상 데이터프레임 객체를 반환한다.
- `fill_value` 옵션을 이용해서 연산의 결과가 `NaN`으로 처리되는 것을 방지할 수 있다.
    - <u>데이터프레임과 시리즈의 연산에서는 </u>`fill_value`<u> 옵션을 사용하면 에러가 반환</u>된다.
    - 따라서, 데이터프레임과 시리즈의 연산에서 `fill_value` 옵션을 이용하여 `NaN` 을 방지하길 원할 경우, 시리즈 객체를 `to_frame`메소드를 이용해서 데이터프레임으로 변환시켜 연산해야한다(이때, `Transpose`가 필요할 수도 있다.).
    - 하지만, `fill_value`<u>는 완전히 새로 생겨난 데이터프레임 셀에 위치한 </u>`NaN`<u>의 관해서는 연산으로 생겨난 셀이 아니기 때문에 처리하지 못한다.</u>
- 데이터프레임과 시리즈의 연산에서는 `axis` 옵션을 이용해서 연산의 축 방향을 자유롭게 지정할 수 있다.

```python
# 메소드를 이용한 데이터프레임끼리의 연산
[데이터프레임 객체1].[add|sub|mul|div]([데이터프레임 객체2], fill_value=..., axis=...)

# 메소드를 이용한 데이터프레임과 시리즈의 연산
[데이터프레임 객체1|시리즈 객체1].[add|sub|mul|div]([데이터프레임 객체2|시리즈 객체2], fill_value=..., axis=...)
```

<br>

먼저 데이터프레임끼리의 연산 예제를 확인해보자.

```python
df1 = \
pd.DataFrame({'c0':[10, 20, 30], 
              'c1':[40, 50, 60],
              'c2':[70, 80, 90]}, index = ['r0','r1','r2'])

df2 = \
pd.DataFrame({'c0':[100, 200, 300], 
              'c1':[400, 500, 600],
              'c2':[700, 800, 900]}, index = ['r1','r2','r3'])

print('[ fill_value옵션 X ]\n', df1.add(df2), '\n')
print('[ fill_value옵션 O ]\n', df1.add(df2, fill_value = 10))
```

```
[ fill_value옵션 X ]
       c0     c1     c2
r0    NaN    NaN    NaN
r1  120.0  450.0  780.0
r2  230.0  560.0  890.0
r3    NaN    NaN    NaN 

[ fill_value옵션 O ]
       c0     c1     c2
r0   20.0   50.0   80.0
r1  120.0  450.0  780.0
r2  230.0  560.0  890.0
r3  310.0  610.0  910.0
```

<br>

다음은 데이터프레임과 시리즈끼리의 연산이다. 동일 행과 열에 위치한 원소끼리 연산되는 것을 확인할 수 있으며, 동일한 행과 열이 없을 경우에는 `NaN`으로 처리되는 것을 확인할 수 있다.

```python
df = \
pd.DataFrame({'c0':[10, 20, 30], 
              'c1':[40, 50, 60],
              'c2':[70, 80, 90]}, index = ['r0','r1','r2'])

sr = \
pd.Series({'c1':100, 'c2':200, 'c3':300,
           'r1':400, 'r2':500, 'r3':600})

df_axis1 = df.add(sr) # axis = 1은 default다
df_axis0 = df.add(sr, axis = 0)

print('[ axis = 1 (default) ]\n', df_axis1, '\n')
print('[ axis = 0 ]\n', df_axis0)
```

```
[ axis = 1 (default) ]
    c0     c1     c2  c3  r1  r2  r3
r0 NaN  140.0  270.0 NaN NaN NaN NaN
r1 NaN  150.0  280.0 NaN NaN NaN NaN
r2 NaN  160.0  290.0 NaN NaN NaN NaN 

[ axis = 0 ]
       c0     c1     c2
c1    NaN    NaN    NaN
c2    NaN    NaN    NaN
c3    NaN    NaN    NaN
r0    NaN    NaN    NaN
r1  420.0  450.0  480.0
r2  530.0  560.0  590.0
r3    NaN    NaN    NaN
```

<br>

하지만, 메소드를 이용한 시리즈와 데이터프레임의 연산에서 `fill_value` 옵션을 이용할 경우 아직 구현되지 않았다는 의미의 에러(`NotImplementedError`)를 반환하는 것을 확인할 수 있다.

```python
try:
    df_axisError = df.add(sr, fill_value = 0)
    print('[ Error ]\n', df_axisError)
except NotImplementedError:
    print('NotImplementedError 에러 반환')
```

```
NotImplementedError 에러 반환
```

<br>

하지만, 적절히 변환을 시도해서 데이터프레임과 시리즈의 연산에서 발생된 `NaN`을 `fill_value`옵션으로 방지할 수 있다.

```python
df = \
pd.DataFrame({'c0':[10, 20, 30], 
              'c1':[40, 50, 60],
              'c2':[70, 80, 90]}, index = ['r0','r1','r2'])

sr = \
pd.Series({'c1':100, 'c2':200, 'c3':300})

# 시리즈의 이름을 지정해준다.
sr.rename('r0', inplace = True)

# 시리즈를 데이터프레임객체로 변환시키고 치환을 진행한다.
# 치환의 경우 시리즈의 인덱스 값에 따라서 이용하지 않아도 된다.
sr_to_df = sr.to_frame().T

# 모든 행의 특정 열 이름에 위치한 원소의 값들과 연산시키기 위해
# 행을 증가시키고, 비어있는 값을 이전 행 값으로 모두 채운다.
sr_to_df = sr_to_df.reindex(index=['r0', 'r1', 'r2']).fillna(method = 'ffill')

print('[ 시리즈의 데이터프레임 변환 결과 ]\n', sr_to_df)

# 데이터프레임객체와 시리즈객체 끼리의 연산이므로 fill_value를 사용할 수 없다.
normal_calc_result = df.add(sr)
# 데이터프레임객체 끼리의 연산이므로 fil_value옵션을 이용할 수 있다.
proc_calc_reuslt = df.add(sr_to_df, fill_value=0)

print('\n=======================비교=======================\n')
print('[ sr을 df와 그냥 연산했을 경우 ]\n',normal_calc_result, '\n')
print('[ sr을 df로 전처리시켜서 연산을 진행했을 경우]\n',  proc_calc_reuslt)
```

```
[ 시리즈의 데이터프레임 변환 결과 ]
       c1     c2     c3
r0  100.0  200.0  300.0
r1  100.0  200.0  300.0
r2  100.0  200.0  300.0

=======================비교=======================

[ sr을 df와 그냥 연산했을 경우 ]
    c0     c1     c2  c3
r0 NaN  140.0  270.0 NaN       -> NaN으로 처리되어 출력됨
r1 NaN  150.0  280.0 NaN
r2 NaN  160.0  290.0 NaN 

[ sr을 df로 전처리시켜서 연산을 진행했을 경우]
      c0     c1     c2     c3
r0  10.0  140.0  270.0  300.0  -> 공통된 위치의 원소가 없을 경우, 0을 더해서 처리됨
r1  20.0  150.0  280.0  300.0
r2  30.0  160.0  290.0  300.0
```
