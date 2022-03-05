---
layout: post
title:  "[Pandas] 데이터 병합/연결1 : concat"
subtitle: "[Pandas] 데이터 병합/연결1 : concat"
categories: data
tags: pandas
comments: true
mathjax: true
---
#### Contents
- [concat 함수](#concat-함수)
  - [1) axis](#1-axis)
  - [2) join](#2-join)
  - [3) ignore_index](#3-ignore_index)
  - [4) verify_integrity](#4-verify_integrity)

pandas의 `concat`함수에 대해서 정리하였습니다.

<br>

---

관계형 테이블과 같이 서로 종속성을 가지고 있는 데이터, 거래성 테이블 중 카테고리 별로 분류되어있는 데이터, 혹은  파티셔닝된 데이터를 다루는 경우에 하나로 합치거나 데이터를 연결해야하는 경우가 많다. 대표적으로 SQL에는 `JOIN`과 `UNION`이 존재한다. 그리고, pandas에서도 이와 동일한 기능을 제공하는 `concat()`, `merge()`, `join()`이 존재한다. 그 중 해당 포스팅에서는 `concat()`함수에 대해서 먼저  소개하고자 한다.

<br>

---

## <span style="color:navy">concat 함수<span>

`concat`의 기본 format은 다음과 같다.

```python
pandas.concat(objs, axis=0, join='outer', 
              ignore_index=False, verify_integrity=False)
```

- `obj` *(**sequence, mapping**)*
    - 연결 대상인 DataFrame혹은 Series객체로 이루어진 sequence혹은 mapping
    - DataFrame끼리의 묶음,  Series끼리의 묶음, DataFrame과 Series끼리의 묶음 모두 가능하다.
    - 연결되는 DataFrame혹은 Series객체의 수에는 제한이 없다.
- `axis` *(**{0/’index’, 1/’columns’}, default 0**)*
    - `axis = 0` (default) : obj에 명시된 객체들을 <u>(상, 하)로 병합</u>한다.
    - `axis = 1` : obj에 전달된 객체들을 <u>(좌, 우)로 연결</u>한다.
- `join` (***{’inner’, ’outer’}, default ‘outer’***)
    - 조인의 경우 `inner`(intersect)와 `outer`(union)을 지정할 수 있다.
    - 그외의 조인 방식은 `concat`에서는 지원하지 않는다.
- `ignore_index` (***bool, default False***)
    - `axis = 0`인 경우 concat으로 <u>생성된 결과의 index를 0, ..., n-1로 초기화</u>한다.
    - `axis = 1`인 경우 concat으로 <u>생성된 결과의 col을 0,...,n-1로 초기화</u>한다.
- `verify_integrity` (***bool, default False***)
    - `axis =0`인 경우 <u>Index의 중복이 확인</u>될 경우 **ValueError**를 raise시킨다.
    - `axis =1`인 경우 <u>Column의 중복이 확인</u>될 경우 **ValueError**를 raise시킨다.
- `copy`파라미터도 존재한다는데 대부분의 `concat`사용 예시를 보면 해당 파라미터는 사용하는 케이스를 아직 본 적이 없다. 하지만, `False`로 설정시 타겟 DataFrame의 복사를 방지하여 메모리 사용량에 있어서 긍정적인 영향을 준다고 한다.

<br>

다음으로 각 파라미터의 변화에 따른 케이스들을 확인해보자.

<br>

### <span style="color:navy">1) axis<span>

`concat`은 DataFrame끼리의 연결, Series끼리의 연결, 그리고 DataFrame과 Series끼리의 연결도 가능하다.

<br>

**DataFrame + DataFrame**

`axis=0`인 경우, DataFrame끼리 결합했을 때는 두 DataFrame 객체가 
<u>위, 아래로 연결된 DataFrame 객체가 반환</u>되는 것을 확인할 수 있다.

```python
df1 = pd.DataFrame([[1, 2, 3], [4, 5, 6], [7, 8, 9]], 
                    index = ['r1', 'r2', 'r3'], 
                    columns=['col1', 'col2', 'col3'])
df2 = pd.DataFrame([[10, 20, 30], [40, 50, 60], [70, 80, 90]], 
                    index = ['r1', 'r2', 'r3'], 
                    columns=['col1', 'col2', 'col3'])

result = pd.concat([df1, df2], axis =0)
print(result)
```

```
    col1  col3  col2
r1     1     2     3
r2     4     5     6
r3     7     8     9
r1    10    30    20
r2    40    60    50
r3    70    90    80
```

<br>

반면, `axis=1`인 경우에는 <u>좌, 우로 연결된 DataFrame객체가 반환</u>되는 것을 확인할 수 있다.

```python
result = pd.concat([df1, df2], axis =1)
print(result)
```

```
     col1  col3  col5  col2  col4  col6
r1     1     2     3    10    20    30
r2     4     5     6    40    50    60
r3     7     8     9    70    80    90
```

<br>

**Series + Series**

다음으로 Series끼리 연결했을 때, `axis=0`인 경우에도 두 객체가 <u>위, 아래로 연결된 Series를 반환</u>하는 것을 확인할 수 있다.

```python
sr1 = pd.Series([1, 2, 3], index = ['r1', 'r2', 'r3'])
sr2 = pd.Series([4, 5, 6], index = ['r1', 'r2', 'r3'])

result = pd.concat([sr1, sr2], axis=0)
print(result)
print(type(result))
```

```
r1    1
r2    2
r3    3
r1    4
r2    5
r3    6
dtype: int64
<class 'pandas.core.series.Series'>
```

<br>

반면, `axis=1`일 경우에는 <u>좌, 우로 연결되면서 정수형 Column 라벨로 초기화된 DataFrame객체를 반환</u>하는 것을 확인할 수 있다.  

```python
result = pd.concat([sr1, sr2], axis=1)
print(result)
print(type(result))
```

```
    0  1
r1  1  4
r2  2  5
r3  3  6
<class 'pandas.core.frame.DataFrame'>
```

<br>

**DataFrame + Series**

해당 케이스의 경우 `axis=1`인 예제 부터 확인해보자.  `axis=1`인 경우에는 두 객체가 다른 케이스들 과 마찬가지로 <u>좌, 우로 연결된 DataFrame을 반환</u>하는 것을 확인할 수 있다.

```python
sr1 = pd.Series([1, 2, 3], index = ['r1', 'r2', 'r3'])
df1 = pd.DataFrame([[1, 2, 3], [4, 5, 6], [7, 8, 9]], 
                    index = ['r1', 'r2', 'r3'], 
                    columns=['col1', 'col2', 'col3'])

result = pd.concat([df1, sr1], axis = 1)
print(result)
```

```
    col1  col3  col2  0
r1     1     2     3  1
r2     4     5     6  2
r3     7     8     9  3
```

<br>

`axis =0`인 경우에는 두 개체가 위, 아래로 연결된 DataFrame이 반환되었지만, 결과를 보게되면 지금까지와는 다르게 col1, col2, col3로만 이루어진 DataFrame이 아니라 0이라고 명칭된 Column도 추가된 것을 확인할 수 있다. 이는 `concat`함수의 `join`파라미터의 기본값이 `outer`로 지정되어있기 때문에 나타난 케이스이다.

```python
result = pd.concat([df1, sr1], axis =0)
print(result)
```

```
      0  col1  col2  col3
r1  NaN   1.0   3.0   2.0
r2  NaN   4.0   6.0   5.0
r3  NaN   7.0   9.0   8.0
r1  1.0   NaN   NaN   NaN
r2  2.0   NaN   NaN   NaN
r3  3.0   NaN   NaN   NaN
```

<br>

이에 대해 이해하기 위하여 `join`파라미터에 대한 예제를 살펴보자.

<br>

### <span style="color:navy">2) join<span>

join파라미터는 SQL에서 사용하는 join과 유사하며 그 중 `inner`와 `outer`를  지정할 수 있다.

`inner`는 교집합이라고 이해하고, `outer`의 경우 합집합으로 이해하면 편한다.

예를 들어, 다음과 같이 동일한 이름의 Index row2와 동일한 이름의 Column col2를 가졌으며 이외에는 모두 다른 DataFrame을 inner join시켰을 때와 outer join시켰을 때를 비교해보자.

```python
df1 = pd.DataFrame([[1, 2, 3], [4, 5, 6], [7, 8, 9]], 
                    index = ['r1', 'r2', 'r3'], 
                    columns=['col1', 'col3', 'col5'])
df2 = pd.DataFrame([[2, 20, 30], [5, 50, 60], [8, 80, 90]], 
                    index = ['r4', 'r2', 'r5'], 
                    columns=['col3', 'col4', 'col6'])

print(df1); print(df2)
```

```
# df1
    col1  col3  col5
r1     1     2     3
r2     4     5     6
r3     7     8     9

# df2
    col3  col4  col6
r4     2    20    30
r2     5    50    60
r5     8    80    90
```

<br>

`join`을 `outer`로 지정하게 될 경우 위에서 보았던 예제들 처럼 DataFrame이 통째로 연결될 것이다. 이때 <u>Column라벨과 Row라벨이 outer join되었기 때문에 전체적으로 합집합 Index와 Column으로 DataFrame의 구조가 확장</u>되며, 기존 <u>DataFrame이 가지고 있는 요소의 위치 이외에는 `NaN`으로 채워</u>지는 것을 확인할 수 있다. 위의 DataFrame과 Series를 `axis = 0`으로 지정하여 합쳤을 때의 결과도 이와 마찬가지이다.

```python
result_axis0 = pd.concat([df1, df2], axis =0, join ='outer')
result_axis1 = pd.concat([df1, df2], axis =1, join ='outer')

print('[ axis = 0 & join = \'outer\']\n', result_axis0)
print()
print('[ axis = 1 & join = \'outer\']\n', result_axis1)
```

```
[ axis = 0 & join = 'outer']
    col1  col3  col5  col4  col6
r1   1.0     2   3.0   NaN   NaN
r2   4.0     5   6.0   NaN   NaN
r3   7.0     8   9.0   NaN   NaN
r4   NaN     2   NaN  20.0  30.0
r2   NaN     5   NaN  50.0  60.0
r5   NaN     8   NaN  80.0  90.0

[ axis = 1 & join = 'outer']
     col1  col3  col5  col3  col4  col6
r1   1.0   2.0   3.0   NaN   NaN   NaN
r2   4.0   5.0   6.0   5.0  50.0  60.0
r3   7.0   8.0   9.0   NaN   NaN   NaN
r4   NaN   NaN   NaN   2.0  20.0  30.0
r5   NaN   NaN   NaN   8.0  80.0  90.0
```

<br>

다음으로, `join`파라미터를 `inner`로 지정하게 될 경우, `axis = 0`일때는 <u>동일한 index이름의 교집합 끼리만 위, 아래로 연결</u>되는 것을 확인할 수 있으며, `axis = 1`일때는 <u>동일한 Column이름의 교집합 끼리만 좌, 우로 연결</u>되는 것을 확인할 수 있다.

```python
result_axis0 = pd.concat([df1, df2], axis =0, join ='inner')
result_axis1 = pd.concat([df1, df2], axis =1, join ='inner')

print('[ axis = 0 & join = \'inner\']\n', result_axis0)
print()
print('[ axis = 1 & join = \'inner\']\n', result_axis1)
```

```
[ axis = 0 & join = 'inner']
     col3
r1     2
r2     5
r3     8
r4     2
r2     5
r5     8

[ axis = 1 & join = 'inner']
    col1  col3  col5  col3  col4  col6
r2     4     5     6     5    50    60
```

<br>

### <span style="color:navy">3) ignore_index<span>

먼저, `axis = 0`으로 지정되었을 때, `ignore_index=False`인 케이스의 경우 위에서 계속 봐왔던 것 처럼 <u>기존 두 객체가 연결되고 나서도 두 객체의 인덱스가 유지</u>되는 것을 확인할 수 있다.

```python
df1 = pd.DataFrame([[1, 2, 3], [4, 5, 6], [7, 8, 9]],
                    index = ['r1', 'r2', 'r3'],
                    columns=['col1', 'col3', 'col5'])
df2 = pd.DataFrame([[2, 20, 30], [5, 50, 60], [8, 80, 90]],
                    index = ['r4', 'r2', 'r5'],
                    columns=['col3', 'col4', 'col6'])

result = pd.concat([df1, df2], axis =0, ignore_index=False)
print(result)
```

```
    col1  col3  col5  col4  col6
r1   1.0     2   3.0   NaN   NaN
r2   4.0     5   6.0   NaN   NaN
r3   7.0     8   9.0   NaN   NaN
r4   NaN     2   NaN  20.0  30.0
r2   NaN     5   NaN  50.0  60.0
r5   NaN     8   NaN  80.0  90.0
```

<br>

하지만, `ignore_index`를 `True`로 지정했을 시에는 병합된  DataFrame의 <u>Index가 0부터 시작되는 정수형 인덱스로 초기화</u>되는 것을 확인할 수 있다.

```python
result = pd.concat([df1, df2], axis =0, ignore_index=True)
print(result)
```

```
   col1  col3  col5  col4  col6
0   1.0     2   3.0   NaN   NaN
1   4.0     5   6.0   NaN   NaN
2   7.0     8   9.0   NaN   NaN
3   NaN     2   NaN  20.0  30.0
4   NaN     5   NaN  50.0  60.0
5   NaN     8   NaN  80.0  90.0
```

<br>

그리고, `axis = 1`경우에  `ignore_index`가 `True`로 지정될 경우, <u>Column라벨이 0부터 시작되는 정수형 Column으로 초기화</u>되는 것을 확인할 수 있다. 

```python
result = pd.concat([df1, df2], axis =1, ignore_index=True)
print(result)
```

```
      0    1    2    3     4     5
r1  1.0  2.0  3.0  NaN   NaN   NaN
r2  4.0  5.0  6.0  5.0  50.0  60.0
r3  7.0  8.0  9.0  NaN   NaN   NaN
r4  NaN  NaN  NaN  2.0  20.0  30.0
r5  NaN  NaN  NaN  8.0  80.0  90.0
```

<br>

### <span style="color:navy">4) verify_integrity<span>

`verify_integrity`파라미터는 두 객체가 연결될 때 지정된 axis 축을 기준으로 Index 혹은 Column의 중복이 있는지 확인할 때 사용할 수 있다.  axis축을 기준으로 한다는 의미는 `axis = 0`일 때는 <u>Index의 중복이 있는지</u>, `axis = 1`일 때는 <u>Column의 중복이 있는지</u>를 의미한다. 이때, <u>중복이 있을 경우에는 </u>`Value_Error`<u>를 반환</u>한다.

예제를 구성하기 위해서 두 DataFrame을 준비했으며, 두 DataFrame은 공통된 Index인 “com_row”와 공통된 Column인 “com_col”을 가지고 있다. 하지만, 두 DataFrame의 “com_row”행과 “com_col”열에 각각 속한 요소들은 동일하지 않는 것을 확인할 수 있다.

```python
df1 = pd.DataFrame([[11, 20, 33],[40, 50, 60],[77, 80, 99]], 
                    index = ['row1', 'com_row', 'row2'], 
                    columns = ['col1', 'com_col', 'col2'])
df2 = pd.DataFrame([[10, 22, 30],[44, 55, 66],[70, 88, 90]], 
                    index = ['row3', 'com_row', 'row4'], 
                    columns = ['col3', 'com_col', 'col4'])

print(df1)
print()
print(df2)
```

```
         col1  com_col  col2
row1       11       20    33
com_row    40       50    60
row2       77       80    99

         col3  com_col  col4
row3       10       22    30
com_row    44       55    66
row4       70       88    90
```

<br>

먼저, Index기준으로 두 DataFrame을 병합한 케이스를 확인해 보자. 이때,  `verify_integrity`를 `True`로 지정하고 출력된 결과에서 com_row에 의해서 Index가 overlapping되었다며 에러를 반환하는 것을 확인할 수 있다.

더불어, 이 `ValueError`는 중복된 Index가 가지고 있는 <u>요소가 모두 일치하지 않아도 Index 이름만 중복한다면 반환</u>되는 것을 알 수 있다.

```python
try:
	result = pd.concat([df1, df2], axis = 0, verify_integrity=True)
	print(result)
except ValueError as e:
	print('❗', getattr(e, 'message', str(e)))
```

```
❗ Indexes have overlapping values: Index(['com_row'], dtype='object')
```

<br>

그리고, Column을 기준으로 두 DataFrame을 연결하는 경우도 동일한 `ValueError`를 반환하는 것을 확인할 수 없으며, 여기서는 두 DataFrame이 가지고 있는 공통된 열 이름인 com_col에 의해서 오류가 반환되었음을 알 수 있다. 이때도 마찬가지로 요소가 모두 일치하지 않아도 Column 이름만 중복된다면 에러가 반환되는 것을 알 수 있다.

```python
try:
	result = pd.concat([df1, df2], axis = 1, verify_integrity=True)
	print(result)
except ValueError as e:
	print('❗', getattr(e, 'message', str(e)))
```

```
❗ Indexes have overlapping values: Index(['com_col'], dtype='object')
```

<br>

`verify_integrity`파라미터를 사용하면 SQL의 `UNION(DISTINCT)`와 동일한 기능을 수행하도록 코드를 구성할 수 있을 것이다. 하지만, 요소가 아닌 Index이름으로 중복을 판단하기에 코드가 길어질 수 있어서 비효율적이라고 생각한다.  차라리,  `verify_integrity=False`로 지정한 `concat`함수를 사용하여 두 객체를 병합시키고 `drop_duplicates`메소드를 통해서 중복을 제거하는 편이 더 간단하게 `UNION(DISTINCT)`를 구사할 수 있을 것이다.

하지만, 고유 값을 가지는 특정 column을 기준으로 지속적으로 추가되는 row와 업데이트되는 요소가 있는 데이터(ex. 고객 정보)를 다룰 경우에는 유용하게 사용할 수 있을 것 같다. 

<br>

---

## <span style="color:navy">References<span>

- [pandas doc : pandas.concat](https://pandas.pydata.org/docs/reference/api/pandas.concat.html)
- 오승환, 『파이썬 머신러닝 판다스 데이터 분석』, 정보문화사 (2019)
