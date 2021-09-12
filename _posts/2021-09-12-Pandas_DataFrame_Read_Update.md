---
layout: post
title:  "[Pandas] DataFrame : Read&Update 행/열part"
subtitle: "[Pandas] DataFrame : Read&Update 행/열part"
categories: data
tags: pandas
comments: true
mathjax: true
---
#### Contents
- [Index / columns / values 접근](#indexcolumnsvalues-접근)
- [행 인덱스와 열 이름 수정](#행-인덱스와-열-이름-수정)
- [행](#행)
	- [3-1) 행 선택](#3-1-행-선택)
	- [3-2) 행 추가/수정](#3-2-행-추가수정)
	- [3-3) 원소 선택/수정](#3-3-원소-선택수정)
- [열](#열)
	- [4-1) 열 선택](#4-1-열-선택)
	- [4-2) 열 추가/수정](#4-2-열-추가수정)

본 포스팅은 Pandas의 DataFrame : Read&Update부분 중 행과 열의 선택/추가/수정 등에 관하여 중점적으로 정리하였습니다.

<br>

---

##  <span style="color:navy">Index / columns / values 접근<span>

데이터프레임 객체는 다음의 속성을 이용해서 **행 인덱스**와 **열 이름**에 접근할 수 있다.

- `index` : DataFrame의 행 인덱스 접근
- `columns` :  DataFrame의 열 이름 접근
- `values` : DataFrame의 값에 접근 (2차원 배열 출력)

```python
dict_data = { 'a0' : [1, 2, 3], 'a1' : [4, 5, 6], 'a2' : [7, 8, 9]}
df = pd.DataFrame(dict_data, index = ['c0', 'c1', 'c2'])
print('[ DataFrame ]\n', df)

index_data = df.index
print('\n[ DataFrame의 index ]\n', index_data)

columns_data = df.columns
print('\n[ DataFrame의 columns ]\n', columns_data)

values_data = df.values
print('\n[ DataFrame의 values ]\n', values_data)
```

```
[ DataFrame ]
     a0  a1  a2
c0   1   4   7
c1   2   5   8
c2   3   6   9

[ DataFrame의 index ]
 Index(['c0', 'c1', 'c2'], dtype='object')

[ DataFrame의 columns ]
 Index(['a0', 'a1', 'a2'], dtype='object')

[ DataFrame의 values ]
 [[1 4 7]
 [2 5 8]
 [3 6 9]]
```

<br>

## <span style="color:navy">행 인덱스와 열 이름 수정<span>

데이터프레임의 **행 인덱스**와 **열 이름**은 위의 `index`와 `columns` 속성으로 수정할 수 있다.

```python
print('[ 행 인덱스/열 이름 수정 전 ]\n', df)

df_copy1 = df.copy()
df_copy1.columns = ['A0', 'A1', 'A2']
df_copy1.index = ['B0', 'B1', 'B2']
print('\n[ 행 인덱스/열 이름 수정 후 ]\n', df_copy1)
```

```
[ 행 인덱스/열 이름 수정 전 ]
     a0  a1  a2
c0   1   4   7
c1   2   5   8
c2   3   6   9

[ 행 인덱스/열 이름 수정 후 ]
     A0  A1  A2
B0   1   4   7
B1   2   5   8
B2   3   6   9
```

<br>

하지만, 위와 같은 방법의 경우 데이터프레임에서 특정 행 인덱스 값 혹은 특정 열 이름 값 하나만 수정하기에는 길이가 늘어날수록 불편해진다. 이와 같이 특정 값을 변경하기 위해서는 `rename` 메소드를 이용한다. 더불어, 해당 메소드는 `inplace` 옵션을 가지고 있기 때문에 새로운 데이터프레임 객체를 반환하거나 원본 객체를 변경할 수 있다. 기본형태는 다음과 같다.

```python
# 행 인덱스 변경
DataFrame.rename(index = {기존 인덱스 : 변경 인덱스, ...}, inplace = True)
# 열 이름 변경
DataFrame.rename(columns = {기존 인덱스 : 변경 인덱스, ...}, inplace = True)
```

```python
print('[ 행 인덱스/열 이름 수정 전 ]\n', df)

df_copy2 = df.copy()
df_copy2.rename(columns = {'a0':'A', 'a2':'C'}, inplace = True)
print('\n[ 특정 열 이름 수정 ]\n', df_copy2)

df_copy3 = df.copy()
df_copy3.rename(index = {'c0':'A', 'c2':'C'}, inplace = True)
print('\n[ 특정 행 인덱스 수정 ]\n', df_copy3)

df_copy4 = df.copy()
df_copy4.rename(columns = {'a0':'A', 'a2':'C'}, 
		index = {'c0':'A', 'c2':'C'}, inplace = True)
print('\n[ 동시 수정 ]\n', df_copy4)
```

```
[ 행 인덱스/열 이름 수정 전 ]
     a0  a1  a2
c0   1   4   7
c1   2   5   8
c2   3   6   9

[ 특정 열 이름 수정 ]
    A  a1  C
c0  1   4  7
c1  2   5  8
c2  3   6  9

[ 특정 행 인덱스 수정 ]
     a0  a1  a2
A    1   4   7
c1   2   5   8
C    3   6   9

[ 동시 수정 ]
    A  a1  C
A   1   4  7
c1  2   5  8
C   3   6  9
```

<br>

## <span style="color:navy">행<span>

###  <span style="color:navy">3-1) 행 선택<span>

데이터프레임의 행을 선택하기 위해서는 `loc`와 `iloc`메소드를 이용한다. 

- `loc` : **인덱스 라벨**을 기반으로 행을 선택한다.
- `iloc` : **정수형("i"nteger) 위치 인덱스**를 기반으로 행을 선택한다.

두 메소드 모두 리스트와 동일하게 슬라이싱을 이용하여 행을 선택할 수 있다.

<br>

두 메소드를 이용하여 **단일 행**을 선택할 경우 다음과 같으며, <br>
`Series` 객체를 반환하는 것을 확인할 수 있다.

```python
print('[ DataFrame ]\n', df)

first_row1 = df.loc['c0']
first_row2 = df.iloc[0]

print('\n[ loc로 c0행 선택 ]\n', first_row1)
print(type(first_row1)) # 출력 결과가 시리즈 객체인 것을 확인할 수 있다.

print('\n[ iloc로 0번째 행 선택 ]\n', first_row2)
print(type(first_row2)) # 출력 결과가 시리즈 객체인 것을 확인할 수 있다.
```

```
[ DataFrame ]
     a0  a1  a2
c0   1   4   7
c1   2   5   8
c2   3   6   9

[ loc로 c0행 선택 ]
a0    1
a1    4
a2    7
Name: c0, dtype: int64
<class 'pandas.core.series.Series'>

[ iloc로 0번째 행 선택 ]
a0    1
a1    4
a2    7
Name: c0, dtype: int64
<class 'pandas.core.series.Series'>
```

<br>

**특정 행만 선택**하고 싶을 경우에는 **리스트**를 이용한다. <br>
단일 행을 선택할 때와 달리 `DataFrame` 객체로 반환되는 것을 확인할 수 있다.

```python
print('[ DataFrame ]\n', df)

first_row1 = df.loc[['c0', 'c2']]
first_row2 = df.iloc[[0, 2]]

print('\n[ loc로 c0, c2 행 선택 ]\n', first_row1)
print(type(first_row1))

print('\n[ iloc로 0번째, 2번째 행 선택 ]\n', first_row2)
print(type(first_row2))
```

```
[ DataFrame ]
     a0  a1  a2
c0   1   4   7
c1   2   5   8
c2   3   6   9

[ loc로 c0, c2 행 선택 ]
     a0  a1  a2
c0   1   4   7
c2   3   6   9
<class 'pandas.core.frame.DataFrame'>

[ iloc로 0번째, 2번째 행 선택 ]
     a0  a1  a2
c0   1   4   7
c2   3   6   9
<class 'pandas.core.frame.DataFrame'>
```

<br>

**슬라이싱**을 이용하여 행을 선택할 경우에는 <br> `loc`와 `iloc`가 선택하는 범위에 차이가 있는 것을 확인할 수 있다.

- `loc` : 만약 ['a', 'c']을 선택할 경우 a, b, c행을 모두 선택한다.
- `iloc` : 만약 [0, 2]를 선택할 경우 0번째, 1번째 행만 선택한다.

```python
print('[ DataFrame ]\n', df)

first_row1 = df.loc['c0':'c1']
first_row2 = df.iloc[0:1]

print('\n[ loc로 \'c0\':\'c1\' 선택 ]\n', first_row1)
print(type(first_row1)) # c0, c1 모두 출력되는 것을 확인할 수 있다.

print('\n[ iloc로 0:1 선택 ]\n', first_row2)
print(type(first_row2)) # c0까지만 출력된다.
```

```
[ DataFrame ]
     a0  a1  a2
c0   1   4   7
c1   2   5   8
c2   3   6   9

[ loc로 'c0':'c1' 선택 ]
     a0  a1  a2
c0   1   4   7
c1   2   5   8
<class 'pandas.core.frame.DataFrame'>

[ iloc로 0:1 선택 ]
     a0  a1  a2
c0   1   4   7
<class 'pandas.core.frame.DataFrame'>
```

<br>

###  <span style="color:navy">3-2) 행 추가/수정<span> 

데이터프레임에 행은 `loc` 메소드를 이용하여 추가할 수 있다. 추가하는 방법은 행을 색인하는 방법과 똑같다. 단, 데이터 프레임에서 `loc`와 닮은 `iloc`의 메소드는 데이터프레임에 행을 추가시킬 수 없음을 유의하자!

```python
# 행추가
DataFrame.loc['새로운 행 이름'] = 새로운 행의 데이터 값 or 새로운 행의 데이터 리스트
```

<br>

리스트를 이용하여 데이터 프레임에 행을 추가할 경우, 리스트의 길이는 데이터 프레임의 열 개수와 동일해야만 한다.  만약 리스트가 아닌 값(예제에서는 999)을 기입할 경우, 모든 열에 동일한 값이 추가된다.

기존 데이터프레임에 존재하는 인덱스 라벨 값을 `loc`메소드에 넘겨주면 기존 데이터프레임의 원소값을 변경시킨다.

```python
print('[ DataFrame 변경 전 ]\n', df)

print('\n==========================================')

df.loc['c3'] = 999
print('\n[ c3행 추가 ]\n', df)

df.loc['c4'] = [100, 200, 300]
print('\n[ c4행 추가 ]\n', df)

print('\n==========================================')

df.loc['c0'] = [11, 22, 33]
print('\n[ c0행 수정 ]\n', df)

# 리스트 형태로 특정 행들만 선택해서 동시에 수정할 수도 있다.
# 하지만, 해당 방법으로는 동시에 행을 추가할 수는 없다.
df.loc[['c1', 'c2']] = [44, 55, 66], [77, 88, 99]
print('\n[ c1행, c2행 동시 수정 ]\n', df)
```

```
[ DataFrame 변경 전 ]
     a0  a1  a2
c0   1   4   7
c1   2   5   8
c2   3   6   9

==========================================

[ c3행 추가 ]
      a0   a1   a2
c0    1    4    7
c1    2    5    8
c2    3    6    9
c3  999  999  999

[ c4행 추가 ]
      a0   a1   a2
c0    1    4    7
c1    2    5    8
c2    3    6    9
c3  999  999  999
c4  100  200  300

==========================================

[ c0행 수정 ]
      a0   a1   a2
c0   11   22   33
c1    2    5    8
c2    3    6    9
c3  999  999  999
c4  100  200  300

[ c1행, c2행 동시 수정 ]
      a0   a1   a2
c0   11   22   33
c1   44   55   66
c2   77   88   99
c3  999  999  999
c4  100  200  300
```

<br>

### <span style="color:navy">3-3) 원소 선택/수정<span>

데이터프레임에서 `loc` 와 `iloc` 메소드에 특정 원소의 좌표(`[행 인덱스, 열이름]`)를 전달하면 해당 위치에 있는 원소가 반환된다. 이는 특정 원소 뿐만 아니라 특정 범위의 원소, 특정 다중 원소 등 모두 반환시킬 수 있다.

```python
# loc 이용
DataFrame.loc[행 인덱스, 열 이름]
DataFrame.loc[행 인덱스][열 이름]

# iloc 이용
DataFrame.iloc[행 번호, 열 번호]
DataFrame.iloc[행 번호][열 번호]
```

<br>

1행 n열 혹은 n행 1열의 원소들을 선택할 경우에는 `series` 객체를 반환한다.

```python
print('[ DataFrame ]\n', df)

a = df.loc['c0', ['a0', 'a1']]
print('\n[ 1행 2열 선택 ]\n', a)
print(type(a))

b = df.loc[['c0', 'c1'], 'a0']
print('\n[ 2행 1열 선택 ]\n', b)
print(type(b))
```

```
[ DataFrame ]
     a0  a1  a2
c0   1   4   7
c1   2   5   8
c2   3   6   9

[ 1행 2열 선택 ]
a0    1
a1    4
Name: c0, dtype: int64
<class 'pandas.core.series.Series'>

[ 2행 1열 선택 ]
c0    1
c1    2
Name: a0, dtype: int64
<class 'pandas.core.series.Series'>
```

<br>

**슬라이싱**을 이용해서 다중 범위 원소를 반환할 수도 있다.

```python
# 딕셔너리 데이터
df = pd.DataFrame({ 'a0' : [1, 2, 3, 4], 'a1' : [5, 6, 7, 8], 'a2' : [9, 10, 11, 12], 'a3' : [13, 14, 15, 16]}, 
		    index = ['c0', 'c1', 'c2', 'c3'])

print('[ DataFrame ]\n', df) # 다른 예제와는 다른 4x4데이터프레임

a = df.loc['c1':'c2', 'a1':'a2']
print('\n[ loc : 1~2행, 1~2열 선택 ]\n', a)
print(type(a))

b = df.iloc[:, :4]
print('\n[ iloc : 전체 행, 0~3열 선택 ]\n', b)
print(type(b))
```

```
[ DataFrame ]
     a0  a1  a2  a3
c0   1   5   9  13
c1   2   6  10  14
c2   3   7  11  15
c3   4   8  12  16

[ loc : 1~2행, 1~2열 선택 ]
     a1  a2
c1   6  10
c2   7  11
<class 'pandas.core.frame.DataFrame'>

[ iloc : 전체 행, 0~3열 선택 ]
     a0  a1  a2  a3
c0   1   5   9  13
c1   2   6  10  14
c2   3   7  11  15
c3   4   8  12  16
<class 'pandas.core.frame.DataFrame'>
```

<br>

## <span style="color:navy">열<span>

###  <span style="color:navy">4-1) 열 선택<span>

데이터프레임에서 열을 선택할 때는 `[](대괄호)`를 이용하거나, 메소드처럼 `.(도트)`를 이용하여 열 이름을 선택할 수 있다. 단, `.(도트)`를 이용한 방법의 경우 단일 열을 선택할 때만 사용한다.  <br>

다음은 **단일 열**을 출력하는 예제이다. 단일 행을 출력할 때와 동일하게 `series` 객체가 반환되는 것을 확인할 수 있다.

```python
print('[ DataFrame ]\n', df)

first_col1 = df['a0']
first_col2 = df.a0 # 따옴표를 안붙이는 것이 포인트이다!

print('\n[ a0열 선택 by [] ]\n', first_col1)
print(type(first_col1))

print('\n[ a0열 선택 by . ]\n', first_col2)
print(type(first_col2))
```

```
[ DataFrame ]
     a0  a1  a2
c0   1   4   7
c1   2   5   8
c2   3   6   9

[ a0열 선택 by [] ]
c0    1
c1    2
c2    3
Name: a0, dtype: int64
<class 'pandas.core.series.Series'>

[ a0열 선택 by . ]
c0    1
c1    2
c2    3
Name: a0, dtype: int64
<class 'pandas.core.series.Series'>
```

<br>

**특정 다중 열**을 출력할 때는 다음과 같으며, `DataFrame` 객체가 반환되는 것을 확인할 수 있다.

```python
print('[ DataFrame ]\n', df)

first_col1 = df[['a0', 'a2']]

print('\n[ a0, a2열 선택 ]\n', first_col1)
print(type(first_col1))
```

```
[ DataFrame ]
     a0  a1  a2
c0   1   4   7
c1   2   5   8
c2   3   6   9

[ a0, a2열 선택 ]
     a0  a2
c0   1   7
c1   2   8
c2   3   9
<class 'pandas.core.frame.DataFrame'>
```

<br>

### <span style="color:navy">4-2) 열 추가/수정<span>

데이터프레임에 열을 추가하는 방법은 데이터프레임의 열을 색인하는 방법과 똑같다.

```python
# 열추가 
DataFrame['새로운 열 이름'] = 새로운 열의 데이터 값 or 새로운 열의 데이터 리스트
```

<br>

리스트로 값을 기입할 경우,  리스트의 길이는 데이터프레임의 행의 길이와 동일해야만 한다. 
만약 리스트가 아닌 값(예제에서는 999)을 기입할 경우, 모든 행에 동일한 값이 추가된다. 

행과 마찬가지로,  기존 데이터프레임에 존재하는 열을 대괄호 안에 기입할 경우 해당 열의 값들이 수정된다. 더불어, 여러 열도 동시에 수정가능하다.

```python
print('[ DataFrame 변경 전 ]\n', df)

print('\n==========================================')

df['a3'] = 999
print('\n[ a3열 추가 ]\n', df)

df['a4'] = [100, 200, 300]
print('\n[ a4열 추가 ]\n', df)

print('\n==========================================')

df['a0'] = [11, 22, 33]
print('\n[ a0열 수정 ]\n', df)

df[['a1', 'a2']] = [[44,55], [66, 77], [88, 99]] 
# [44, 55], [66, 77], [88, 99]로 기입가능
print('\n[ a1열, a2열 수정 ]\n', df)
```

```
[ DataFrame 변경 전 ]
     a0  a1  a2
c0   1   4   7
c1   2   5   8
c2   3   6   9

==========================================

[ a3열 추가 ]
     a0  a1  a2   a3
c0   1   4   7  999
c1   2   5   8  999
c2   3   6   9  999

[ a4열 추가 ]
     a0  a1  a2   a3   a4
c0   1   4   7  999  100
c1   2   5   8  999  200
c2   3   6   9  999  300

==========================================

[ a0열 수정 ]
     a0  a1  a2   a3   a4
c0  11   4   7  999  100
c1  22   5   8  999  200
c2  33   6   9  999  300

[ a1열, a2열 수정 ]
     a0  a1  a2   a3   a4
c0  11  44  55  999  100
c1  22  66  77  999  200
c2  33  88  99  999  300
```

