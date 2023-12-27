---
layout: post
title:  "[Pandas] DataFrame : Create"
subtitle: "[Pandas] DataFrame : Create"
categories: data
tags: pandas
comments: true
mathjax: true
---

#### Contents
- [DataFrame](#dataframe)
- [Series To DataFrame](#series-to-dataframe)
- [Dict To DataFrame](#dict-to-dataframe)
- [2Dim List To DataFrame](#2dim-list-to-dataframe)

본 포스팅은 Pandas의 DataFrame : Create부분에 관하여 정리하였습니다.

<br>

---

## <span style="color:navy">DataFrame<span>

`pandas`의 `DataFrame`은 다음과 같은 특징을 가진다.

- `DataFrame`은 각 **열**이 `Series`객체로 이루어진 **2차원 배열**이다.
- `DataFrame`의 **행**은 다양한 속성 데이터들의 모음인 **레코드(record)**이다.

`DataFrame` 객체를 CRUD(Create, Read, Update, Delete)순서대로 나누어서 알아볼 것이며, <br> 양이 많기에 포스팅마다 나누어 정리하였다. 해당 포스팅에서는 Create부분에 관해서 정리하였다.

<br>

`DataFrame`을 만들려면 다음과 같은 `DataFrame()`함수를 사용하며,  <br>
data 매개변수에는 다음의 것들이 인자로 들어갈 수 있다.

- 같은 길이로 이루어진 `Series`<u>객체로 이루어진 리스트</u>
- 같은 길이로 이루어진 <u>1차원 배열들을 값으로 가지는 딕셔너리</u>
- 같은 길이로 이루어진 <u>1차원 배열로 이루어진 2차원 리스트</u>

더불어, 시리즈와 동일하게 `index`와 `columns` 매개변수에 리스트 값을 전달하여 인덱스 라벨과 열  이름을 지정할 수 있다.

```python
pandas.DataFrame(data= ..., index = ..., columns = ...)
```

<br>

##  <span style="color:navy">Series To DataFrame<span>

`Series` 객체를 이용하여 `DataFrame`을 생성할 경우, <u>Series의 공통된 index값이 column값으로 전환되어 이것을 기준으로 데이터 값이 위치</u>되는 것을 확인할 수 있다.  더불어, DataFrame의 index값을 별도로 전달하지 않았기 때문에 정수형 위치 인덱스가 자동 생성되었다.

```python
# 시리즈로 이루어진 배열 데이터
Series_data1 = pd.Series({'a0':1, 'a1':4, 'a2':7})
Series_data2 = pd.Series({'a0':2, 'a1':5, 'a2':8})
Series_data3 = pd.Series({'a0':3, 'a1':6, 'a2':9})
Series_list_data = [Series_data1, Series_data2, Series_data3]

df1 = pd.DataFrame(Series_list_data)
print('[ 시리즈로 이루어진 배열 데이터 ]\n', df1)
print(type(df1))
```

```
[ 시리즈로 이루어진 배열 데이터 ]
    a0  a1  a2
0   1   4   7
1   2   5   8
2   3   6   9
<class 'pandas.core.frame.DataFrame'>
```

<br>

## <span style="color:navy">Dict To DataFrame<span>

`Dict`데이터을 이용하여  `DataFrame`을 생성할 경우, 딕셔너리의 <u>key값이 데이터프레임의 열로 위치</u>되는 것을 확인할 수 있으며 , <u>각 열에는 해당 키값에 따른 리스트의 데이터 원소가 순차적으로 위치</u>되었다.  더불어, 위에서와 달리 index값을 지정해주었기 때문에 인덱스 라벨이 지정되었다.

```python
# 딕셔너리 데이터
dict_data = { 'a0' : [1, 2, 3], 'a1' : [4, 5, 6], 'a2' : [7, 8, 9]}

df2 = pd.DataFrame(dict_data, index = ['c0', 'c1', 'c2'])
print('[ 딕셔너리 데이터 ]\n', df2)
print(type(df2))
```

```
[ 딕셔너리 데이터 ]
     a0  a1  a2
c0   1   4   7
c1   2   5   8
c2   3   6   9
<class 'pandas.core.frame.DataFrame'>
```

<br>

##  <span style="color:navy">2Dim List To DataFrame<span>

`2차원 리스트`로 `DataFrame` 을 만드는 경우 순차적인 열 백터를 생성하는 다른 방법들과 조금 다르다. <u>2차원 리스트 내부의 1차원 리스트 원소는 각각 데이터프레임에서 하나의 행(혹은 레코드)에 배치</u>되는 것을 확인할 수 있다. 더불어, 열 이름을 따로 지정하지 않았으며 정수가 자동 지정되었을 것이지만, columns 매개변수를 통해서 열 이름을 지정해주었다.

```python
# 2차원 리스트 데이터
list_data = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]

df2 = pd.DataFrame(list_data, columns = ['a0', 'a1', 'a2'])
print('[ 2차원 리스트 데이터 ]\n', df3)
print(type(df3))
```

```
[ 2차원 배열 데이터 ]
    a0  a1  a2
0   1   2   3
1   4   5   6
2   7   8   9
<class 'pandas.core.frame.DataFrame'>
```
