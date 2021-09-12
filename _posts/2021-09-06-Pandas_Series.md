---
layout: post
title:  "[Pandas] Series (CRUD)"
subtitle: "[Pandas] Series (CRUD)"
categories: data
tags: pandas
comments: true
mathjax: true
---
#### Contents
- [Create](#create)
	- 1-1)  List to Series
	- 1-2)  Dict to Series
- [Read & Update](#readupdate)
	- 2-1) Index와 Values
	- 2-2) 원소 선택 및 수정
- [Delete](#delete)

본 포스팅은 Pandas의 Series에 관하여 정리하였습니다.

<br>

---

<br>

`pandas`의 `Series`객체는 다음과 같은 특징을 가진다.

- 데이터가 순차적으로 나열된 1차원 배열의 형태이다.
- `index`와 데이터 `value`가 일대일로 대응된다.
- `index`는 데이터 `value`의 위치를 나타내는 주소역할을 한다.

`Series` 객체를 CRUD(Create, Read, Update, Delete)순서로 알아보자.

<br>

## <span style="color:navy">Create<span>

`Series`객체는 **딕셔너리**혹은 **리스트** 객체를 다음의 함수 인자에 전달하여 생성할 수 있다.

```bash
# Series의 대소문자에 유의하자!
pandas.Series( [딕셔너리 or 리스트], index = [인덱스 라벨 리스트])  
```

<br>

### <span style="color:navy">1-1) Create : List to Series<span>

다음과 같이 **리스트 객체**를 `Series`의 함수 인자에 기입하여 생성할 수 있다. 출력된 결과에서 좌측의 경우 현재 별도의 index값을 전달하지 않았기 때문에 인덱스에 `정수형 위치 인덱스(integer position)`가 자동으로 지정된 것을 확인할 수 있으며 우측의 경우 각 인덱스에 대응되는 값이다. 정수형 위치 인덱스의 경우 0부터 자동 생성된다.

이때, 데이터 값의 자료형(dtype)은 정수형(int64)인 것을 확인할 수 있다.  

더불어, `type()`함수로 list_to_sr에 저장된 객체의 자료형을 확인해 보면 `Series` 클래스인 것을 확인할 수 있다.

```python
list_to_sr = pd.Series([1, 2, 3])
print(list_to_sr)
print(type(list_to_sr))
```

```
0    1
1    2
2    3
dtype: int64
<class 'pandas.core.series.Series'>
```

<br>

위와 달리 별도의 index리스트를 함수 내부에서 지정할 경우 다음과 같이 정수형 위치 인덱스가 아닌  `인덱스 라벨(index label)`이 지정된 것을 확인할 수 있다.

```python
list_to_sr = pd.Series([1, 2, 3], index = ['A', 'B', 'C'])
print(list_to_sr)
print(type(list_to_sr))
```

```
A    1
B    2
C    3
dtype: int64
<class 'pandas.core.series.Series'>
```

<br>

### <span style="color:navy">1-2) Create : Dict to Series<span>

`pandas`의 `series` 함수에는 시리즈 객체와 비슷하게 key와 value를 사용하는  **dict객체**도 기입하여 생성할 수 있다. 

```python
dict_to_sr = pd.Series({'A':1, 'B':2, 'C':3})
print(dict_to_sr)
print(type(dict_to_sr))
```

```
A    1
B    2
C    3
dtype: int64
<class 'pandas.core.series.Series'>
```

<br>

## <span style="color:navy">Read & Update<span>


### <span style="color:navy">2-1) Index와 Values<span>

`Series`객체는 다음의 속성들을 통해서 인덱스배열과 데이터 값 배열만을 따로 선택할 수 있다. 

- `index` : 인덱스 배열 선택
    - Series객체의 인덱스가 정수형 위치 인덱스의 경우,  <br>인덱스 범위인 `RangeIndex` 객체를 반환한다.
    - 인덱스 라벨이 지정되어 있을 경우, 시리즈의 인덱스 배열을 반환한다.
- `values` : 데이터값 배열 선택
- *`name` : 시리즈에 이름을 지정한다.

<br>

이를 이용하여,  위에서 생성된 dict_to_sr과 list_to_sr의 인덱스와 값에 접근할 경우 다음과 같다.

```python
# 리스트
list_sr_index = list_to_sr.index
list_sr_value = list_to_sr.values
print('< 리스트 to 시리즈 >')
print(f'index : {list_sr_index}', '\n', 
      f'type(index) : {type(list_sr_index)}', '\n',
      f'value : {list_sr_value }', '\n',
      f'type(value) : {type(list_sr_value)}')

print() # 개행

# 딕셔너리
dict_sr_index = dict_to_sr.index
dict_sr_value = dict_to_sr.values
print('< 딕셔너리 to 시리즈 >')
print(f'index : {dict_sr_index }', '\n', 
      f'type(index) : {type(dict_sr_index)}', '\n',
      f'value : {dict_sr_value }', '\n',
      f'type(value) : {type(dict_sr_value)}')
```

```
< 리스트 to 시리즈 >
index : RangeIndex(start=0, stop=3, step=1) 
type(index) : <class 'pandas.core.indexes.range.RangeIndex'> 
value : [1 2 3] 
type(value) : <class 'numpy.ndarray'>

< 딕셔너리 to 시리즈 >
index : Index(['A', 'B', 'C'], dtype='object') 
type(index) : <class 'pandas.core.indexes.base.Index'> 
value : [1 2 3] 
type(value) : <class 'numpy.ndarray'>
```

<br>

더불어, `index`속성을 이용해서 인덱스 라벨을 변경하거나 추가할 수 있다. 하지만, 시리즈 value의 경우 `values`속성으로 수정이 불가능하며, 만약 시도할 경우 `AttributeError`를 반환한다.

```python
exam_sr = pd.Series({'A':1, 'B':2, 'C':3})
print(f'기본 : {exam_sr.index}')

exam_sr.index = ['가', '나', '다']
print(f'\n인덱스 변경 : {exam_sr.index}')

# AttributeError발생
try:
    exam_sr.values = [100, 200, 300]
    print(f'\n데이터 값 변경 : {exam_sr.values}')
except AttributeError as e:
		print('\n','AttributeError :', e)
```

```
기본 : Index(['A', 'B', 'C'], dtype='object')

인덱스 변경 : Index(['가', '나', '다'], dtype='object')

AttributeError : can't set attribute
```

<br>

### <span style="color:navy">2-2) 원소 선택 및 수정<span>

원소의 주소를 의미하는 인덱스를 이용하여 시리즈의 원소를 하나만 선택하거나 여러 원소를 한꺼번에 선택할 수 있다.

- **정수형 위치 인덱스**로 지정된 시리즈의 경우 대괄호[]안에 위치를 나타내는 숫자를 입력한다.
- **인덱스 라벨**이 지정된 시리즈의 경우 대괄호[]안에 숫자를 입력하거나 이름과 함께 따옴표를 입력한다.

그리고, 여러 개의 인덱스를 리스트 형태로 대괄호[]안에 입력하면 각 인덱스와 대응되는 데이터를 모두 반환한다. 그리고, 인덱스 범위를 슬라이싱하여 선택할 수도 있다. 

정수형 인덱스를 이용할 경우에는 파이썬의 리스트 슬라이싱과 동일하게 [0: n]으로 슬라이싱할 경우 n-1까지만 반환한다. 하지만, 인덱스 라벨을 이용하여 ['A':'D']으로 슬라이싱할 경우 'D'까지 반환된다.

```python
exam_sr = pd.Series({'A':1, 'B':2, 'C':3})

# 정수형 위치 인덱스를 이용하여 색인
a0 = exam_sr[0] # 한개의 원소
a1 = exam_sr[[0, 2]] # n개의 원소
a2 = exam_sr[0:2] # 슬라이싱 

print(f'특정 원소 1개 선택 : \n{a0}\n')
print(f'특정 원소 n개 선택 : \n{a1}\n')
print(f'슬라이싱으로 원소 선택 : \n{a2}\n')
```

```
특정 원소 1개 선택 : 
1

특정 원소 n개 선택 : 
A    1
B    2
dtype: int64

슬라이싱으로 원소 선택 : 
A    1
B    2
dtype: int64
```

<br>

```python
exam_sr = pd.Series({'A':1, 'B':2, 'C':3})

# 인덱스 라벨을 이용하여 색인
a0 = exam_sr['A'] # 한개의 원소
a1 = exam_sr[['A', 'B']] # n개의 원소
a2 = exam_sr['A':'B'] # 슬라이싱 

print(f'특정 원소 1개 선택 : \n{a0}\n')
print(f'특정 원소 n개 선택 : \n{a1}\n')
print(f'슬라이싱으로 원소 선택 : \n{a2}\n')
```

```
특정 원소 1개 선택 : 
1

특정 원소 n개 선택 : 
A    1
B    2
dtype: int64

슬라이싱으로 원소 선택 : 
A    1
B    2
dtype: int64
```

<br>

더불어, 이를 이용하여 `values` 속성이 할 수 없었던 시리즈의 데이터 값을 수정할 수 있다. 

```python
exam_sr = pd.Series({'A':1, 'B':2, 'C':3})
print(f'기본 : {exam_sr.values}')

exam_sr[0] = 100
print(f'한 개의 값만 수정 : {exam_sr.values}')

exam_sr[[0, 1]] = [10, 20]
print(f'n개의 값만 수정 : {exam_sr.values}')

exam_sr[0:3] = [10, 20, 30]
print(f'n개의 값만 수정 : {exam_sr.values}')
```

```
기본 : [10 20  3]
한 개의 값만 수정 : [100   2   3]
n개의 값만 수정 : [10 20  3]
슬라이싱을 이용한 수정 : [10 20 30]
```

<br>

## <span style="color:navy">Delete<span>

시리즈의 데이터는 다음을 사용하여 제거할 수 있다. 

- `del` 명령어
    - 인덱스 라벨이 지정된 시리즈일 경우에는 `series객체[index 번호]`이런 식으로 사용하면 에러가 발생한다
- `drop` 메소드
    - `inplace = True`  :기존 객체의 값을 변경할 수 있다.
    - `inplace = False` :새로운 값을 반환한다. (Default)
    - 인덱스 라벨이 지정된 시리즈일 경우에는 `series객체[index 번호]`이런 식으로 사용하면 에러가 발생한다

```python
exam_sr = pd.Series({'A':1, 'B':2, 'C':3})
print(f'< 기본 >\n {exam_sr}\n')

sr1 = exam_sr.copy()
del sr1['A']
print(f'< del 명령어를 이용한 데이터 제거 >\n {sr1}\n')

sr2 = exam_sr.copy()
sr2.drop('B', inplace = True)
print(f'< drop : 단일 데이터 제거 >\n {sr2}\n')

sr3 = exam_sr.copy()
sr3.drop(['A', 'C'], inplace = True)
print(f'< drop : 다중 데이터 제거 >\n {sr3}\n')
```

```
< 기본 >
A    1
B    2
C    3
dtype: int64

< del 명령어를 이용한 데이터 제거 >
B    2
C    3
dtype: int64

< drop : 단일 데이터 제거 >
A    1
C    3
dtype: int64

< drop : 다중 데이터 제거 >
B    2
dtype: int64
```

