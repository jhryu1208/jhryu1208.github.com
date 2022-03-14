---
layout: post
title:  "[Pandas] DataFrame : Read&Update 인덱스/치환part"
subtitle: "[Pandas] DataFrame : Read&Update 인덱스/치환part"
categories: data
tags: pandas
comments: true
mathjax: true
---
#### Contents
- [열↔인덱스: set_index, reset_index](#열인덱스-set_index-reset_index)
- [인덱스 재배열: reindex](#인덱스-재배열-reindex)
- [인덱스 정렬: sort_index](#인덱스-정렬-sort_index)
- [데이터프레임 Transpose](#데이터프레임-transpose)

본 포스팅은 Pandas의 DataFrame : Read&Update부분 중 데이터프레임의 인덱스 수정 및 정렬과 치환에 관하여 중점적으로 정리하였습니다.

<br>

---

##  <span style="color:navy">열↔인덱스: set_index, reset_index<span>

`set_index` 메소드를 이용하면 특정 열의 값을 새로운 인덱스 라벨로 지정할 수 있다. 더불어, 해당 메소드는 변경된 새로운 값을 반환하며, `inplace=True` 로 지정할 경우에는 원본 객체를 변경시킨다.

```python
dict_data = { 'a0' : ['A', 'B', 'C'], 'a1' : [1, 2, 3], 'a2' : [4, 5, 6]}
df = pd.DataFrame(dict_data)

print('[ DataFrame 변경 전 ]\n', df)
print(df.index)

df.set_index('a0', inplace=True)
print('\n[ DataFrame 변경 후1 ]\n', df)
print(df.index)
```

```
[ DataFrame 변경 전 ]
   a0  a1  a2
0  A   1   4
1  B   2   5
2  C   3   6
RangeIndex(start=0, stop=3, step=1)

[ DataFrame 변경 후1 ]
     a1  a2
a0        
A    1   4
B    2   5
C    3   6
Index(['A', 'B', 'C'], dtype='object', name='a0')
```

<br>

`set_index` 메소드는 다음과 같이 <u>열 이름으로 이루어진 리스트</u>를 받아서, 
**멀티인덱스**를 생성시킬 수 있다.

```python
df.set_index(['a0', 'a1'], inplace=True)
print('\n[ DataFrame 변경 후2 ]\n', df)
print(df.index)
```

```
[ DataFrame 변경 후2 ]
        a2
a0 a1    
A  1    4
B  2    5
C  3    6
MultiIndex([('A', 1), ('B', 2), ('C', 3)], names=['a0', 'a1'])
```

<br>

또한,  `reset_index` 메소드를 이용하여 지정된 인덱스 라벨을 열로 전환시킬 수 있다. `set_index`와 마찬가지로 `inplace`옵션을 가지고 있다.

```python
df.reset_index(inplace=True)
print('\n[ DataFrame 변경 후3 ]\n', df)
print(df.index)
```

```
[ DataFrame 변경 후3 ]
   a0  a1  a2
0  A   1   4
1  B   2   5
2  C   3   6
RangeIndex(start=0, stop=3, step=1)
```

<br>

##  <span style="color:navy">인덱스 재배열: reindex<span>

`reindex`메소드를 이용하면, <u>행 인덱스를 새로운 배열로 재배치</u>할 수 있다.  원본 데이터프레임에 없던 새로운 인덱스 행을 추가시킬 경우 해당 행의 데이터 값은 `NaN`으로 기입된다. <br> 이는 `fill_value` 옵션을 추가하여 `NaN`이 아닌 유효한 다른 값으로 대체할 수 있다.  기본 형태는 다음과 같다.

```python
DataFrame.reindex(새로운 인덱스 배열, fill_value = ...)
```

```python
df = pd.DataFrame({'a0':[1, 2, 3], 
		   'a1':[4, 5, 6], 
		   'a2':[7, 8, 9]}, index = ['c0', 'c1', 'c2'])
print('[ 재배치 이전 ]\n', df)

df1 = df.copy().reindex(['c2', 'c2', 'c0'])
print('\n[ 인덱스 재배치1 ]\n', df1)

df2 = df.copy().reindex(['c0', 'c1', 'c1'])
print('\n[ 인덱스 재배치2 ]\n', df2)

df3 = df.copy().reindex(['c0', 'c1', 'c2', 'new c3'])
print('\n[ 새로운 인덱스 추가1 ]\n', df3)

df4 = df.copy().reindex(['c0', 'c1', 'c2', 'new c3'], fill_value = 999)
print('\n[ 새로운 인덱스 추가2 : fill_value이용 ]\n', df4)
```

```
[ 재배치 이전 ]
     a0  a1  a2
c0   1   4   7
c1   2   5   8
c2   3   6   9

[ 인덱스 재배치1 ]
     a0  a1  a2
c2   3   6   9
c2   3   6   9
c0   1   4   7

[ 인덱스 재배치2 ]
     a0  a1  a2
c0   1   4   7
c1   2   5   8
c1   2   5   8

[ 새로운 인덱스 추가1 ]
          a0   a1   a2
c0      1.0  4.0  7.0
c1      2.0  5.0  8.0
c2      3.0  6.0  9.0
new c3  NaN  NaN  NaN

[ 새로운 인덱스 추가2 : fill_value이용 ]
          a0   a1   a2
c0        1    4    7
c1        2    5    8
c2        3    6    9
new c3  999  999  999
```

<br>

##  <span style="color:navy">인덱스 정렬: sort_index<span>

데이터프레임은 `sort_index`메소드를 통해 행 인덱스를 기준으로 데이터프레임의 행을 정렬시킬 수 있다. 해당 메소드는 기본적으로 오름차순 정렬 결과를 반환하며, `asceding`옵션을 `False`로 지정할 경우 내림차순 정렬 결과를 반환한다.  <br>

(*참고로 비슷한 메소드인 `sort_values`가 있으며, 해당 메소드는 지정한 데이터프레임의 열 값 기준으로 행 인덱스를 정렬시킨다.)

```python
DataFrame.sort_index(ascending = ...)
```

```python
df = pd.DataFrame({'a0':[1, 2, 3], 
		   'a1':[4, 5, 6], 
	          'a2':[7, 8, 9]}, index = ['c0', 'c1', 'c2'])

df = df.iloc[::-1]
print('[ 정렬 이전 ]\n', df)

asc_df = df.copy().sort_index()
print('\n[ 오름차순 정렬 ]\n', asc_df)

desc_df = asc_df.sort_index(ascending= False)
print('\n[ 내림차순 정렬 ]\n', desc_df)
```

```
[ 정렬 이전 ]
     a0  a1  a2
c2   3   6   9
c1   2   5   8
c0   1   4   7

[ 오름차순 정렬 ]
     a0  a1  a2
c0   1   4   7
c1   2   5   8
c2   3   6   9

[ 내림차순 정렬 ]
     a0  a1  a2
c2   3   6   9
c1   2   5   8
c0   1   4   7
```

<br>

##  <span style="color:navy">데이터프레임 Transpose<span>

pandas의 데이터프레임 객체의 행과 열은 `transpose` 메소드를 이용해서 간단하게 치환시킬 수 있다.  해당 메소드와 동일하게 `T` 클래스 속성을 이용해서도 데이터프레임을 치환시킬 수 있다.

```python
DataFrame.transpose()
DataFrame.T
```

<br>

아래의 예제에서는 총 연속 치환시켰기 때문에 기존 데이터프레임과 동일한 데이터프레임을 반환하는 것을 확인할 수 있다.  더불어, 치환 메소드의 경우 원본의 값을 수정할 수 없기 때문에 새로운 변수를 선언해줘서 값을 넣어주어야한다.

```python
df = pd.DataFrame({'col0':[1, 2, 3], 
		   'col1':[4, 5, 6], 
	          'col2':[7, 8, 9]}, index = ['index0', 'index1', 'index2'])
print('[ 치환 전 ]\n', df)

first_trans = df.transpose()
print('\n[ 1st 치환 ]\n', first_trans)

second_trans = first_trans.T
print('\n[ 2nd 치환 ]\n', second_trans)
```

```
[ 치환 전 ]
         col0  col1  col2
index0     1     4     7
index1     2     5     8
index2     3     6     9

[ 1st 치환 ]
       index0  index1  index2
col0       1       2       3
col1       4       5       6
col2       7       8       9

[ 2nd 치환 ]
         col0  col1  col2
index0     1     4     7
index1     2     5     8
index2     3     6     9
```


