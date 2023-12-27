---
layout: post
title:  "[Pandas] 함수매핑1 : map, apply"
subtitle: "[Pandas] 함수매핑1 : map, apply"
categories: data
tags: pandas
comments: true
mathjax: true
---

#### Contents
- [map 메소드](#map-메소드)
- [apply 메소드](#apply-메소드)
  - [2-1) apply 메소드 in Series](#2-1-apply-메소드-in-series)
  - [2-2) apply 메소드 in DataFrame](#2-2-apply-메소드-in-dataframe)
    - [2-2-1) when axis=0](#2-2-1-when-axis0)
    - [2-2-2) when axis=1](#2-2-2-when-axis1)

본 포스팅은 `pandas`의 `map`과 `apply`메소드에 관한 내용을 정리하였습니다.

<br>

---



파이썬의 내장 함수에는 list와 같은 iterable객체의 각 원소를 매핑 함수에 대입하여 각 원소에 대하여 결과값을 출력하는 `map`함수가 존재한다. 이와 동일하게 pandas의 Series와  DataFrame객체에도 `map`, `apply`, `applymap`, 그리고 `pipe`와 같이 매핑을 위한 메소드들이 존재한다. 오늘은 이중 `map`과 `apply` 대해 정리해 보고자 한다.

<br>

---

## <span style="color:navy">map 메소드<span>

여기서 설명하는 `map`메소드는 파이썬 내장 함수의 `map`과는 다르지만, 기능은 거의 비슷하다. 이 메소드는 Series 클래스 객체에서 이용 가능한 메소드이다. 따라서, DataFrame 객체에는 사용할 수 없다.

해당 함수의 기본 format 부터 간단히 짚고 가도록 하자.

```python
[series 객체].map(arg)
```

- `arg` : 매핑 함수

<br>

다음은 Series객체의 각 원소에 10을 곱하는 함수를 매핑하는 예제이다.

```python
sr0 = pd.Series([1, 2, 3])

map_func = lambda x : x*10
mapped_sr = sr.map(map_func)

pd.DataFrame({'origin': sr, 'after_map': mapped_sr}) # 비교
```

```
   origin  after_map
0       1         10
1       2         20
2       3         30
```

<br>

---

## <span style="color:navy">apply 메소드<span>


`apply`메소드는 Series객체와 DataFrame객체에 모두 존재한다. Series에서의 `apply`는 위에서 확인한 `map`메소드와 동일한 기능을 수행하지만, `map`과 달리 함수에 별개의 인자를 전달할 수 있다는 큰 장점을 가진다. 물론 이는 DataFrame객체에서도 마찬가지이다.

<br>


### <span style="color:navy">2-1) apply 메소드 in Series<span>

다음은 Series의 apply메소드의 기본 format이다.

```python
[Series 객체].apply(mapping_func, args=(), **kwargs)
```

- `mapping_func` : 매핑 함수
- `args=(), **kwargs` : 함수에 전달할 인자, 인자 전달 방식은 기본 함수와 동일하다. 따라서 unpackage, package, 그리고 key: value 방식 등 편한 방법으로 이용이 가능하다.
    
<br>

다음은 Series객체의 `apply`메소드 사용 예제이다. `map`메소드를 보는 과정에서 대략적인 매핑함수 지정 및 선언 방법은 확인하였기에 예제에서는 함수에 인자를 전달하는 방법들에 관해서 초점을 두었다.

```python
sr1 = pd.Series([1, 2, 3])

def apply_sr_func0(x, arg1, arg2):
    return f'{x}, {arg1}, {arg2}'

print(sr1.apply(apply_sr_func0, args=(1, 3)), '\n')
print(sr1.apply(apply_sr_func0, arg1=1, arg2=3), '\n' )

def apply_sr_func1(x, **kwargs):
    return f'{x}, ' + '{arg1}, {arg2}'.format(**kwargs)

print(sr1.apply(apply_sr_func1, arg1=1, arg2=3))
```

```
0    1, 1, 3
1    2, 1, 3
2    3, 1, 3
dtype: object 

0    1, 1, 3
1    2, 1, 3
2    3, 1, 3
dtype: object 

0    1, 1, 3
1    2, 1, 3
2    3, 1, 3
dtype: object
```

<br>

### <span style="color:navy">2-2) apply 메소드 in DataFrame<span>

DataFrame의 `apply`메소드의 기본 format은 다음과 같다.

```python
[DataFrame 객체].apply(mapping_func, axis=0, result_type=None, args=(), **kwargs)
```

- `mapping_func` : 매핑 함수
- `axis`
    - `axis=0`
        
        매핑 함수를 DataFrame의 각 column단위에 적용한다. 매핑 함수에 전달된 각 column의 리턴값은 Series형태로 반환되며, 모든 column에 대해서 반환된 Series가 최종적으로 DataFrame으로 통합되는 과정을 수행한다.
        
    - `axis=1`
        
        매핑 함수를 DataFrame의 각 row단위에 적용한다. 매핑 함수에 각 row가 전달되며, DataFrame의 행 index가 Series의 index가 되고, 함수가 적용되어 리턴된 값은 Series의 value가 된다. 
        
- `result_type`
    - axis=1일 때, 즉 row단위에서만 매핑함수를 적용했을 때만 사용할 수 있는 옵션이다.
    - `expand` : 결과 column으로 반환하고 기존 DataFrame에 결과 column을 확장시킨다.
    - `broadcast` : mapping된 결과를 기존 column 형식대로 확장한다.
    - 기본값은 None이다.
- `args=(), **kwargs` : Series의 apply와 동일

<br>

#### <span style="color:navy">2-2-1) When axis=0<span>

DataFrame에 `apply`메소드의 `axis=0`일 때는 위에서 언급했듯이 column단위에 매핑 함수가 적용된다. 그리고, `axis=0`일 때, 매핑되는 함수가 반환되는 값에 따라서 `apply`메소드의 반환 타입이 다르다. 

예를 들어, `axis=0`일 때, 매핑함수가 Series를 반환하는 경우에는 아래와 같이 DataFrame을 반환한다. 
![Untitled](https://user-images.githubusercontent.com/53929665/155876330-d03a48ac-8ddf-4d0d-937c-fb257adfd3b2.png)

```python
test_df = pd.DataFrame([[1, 2, 3],
                    [4, 5, 6],
                    [7, 8, 9]],
                   columns=['col1', 'col2', 'col3'],
                   index=['row1', 'row2', 'row3'])

axis0_df0 = test_df.apply(lambda x: x.isnull(), axis = 0)

print("\n[ 시리즈를 반환하는 함수를 매핑했을 때 ]")
print(axis0_df0)
```

```
[ 시리즈를 반환하는 함수를 매핑했을 때 ]
       col1   col2   col3
row1  False  False  False
row2  False  False  False
row3  False  False  False
```

<br>

반면, 하나의 값을 반환하는 함수가 매핑함수에 사용될 경우에는 아래와 같이 Series를 반환한다.
![Untitled 1](https://user-images.githubusercontent.com/53929665/155876328-598bc4ab-c638-4669-83f3-b0dff7dc5e67.png)
```python
axis0_df1 = test_df.apply(np.sum, axis = 0)

print("\n[ 하나의 값을 반환하는 함수를 매핑했을 때 ]")
print(axis0_df1)
```

```
[ 하나의 값을 반환하는 함수를 매핑했을 때 ]
col1    12
col2    15
col3    18
dtype: int64
```

<br>

#### <span style="color:navy">2-2-2) When axis=1<span>

DataFrame의 `apply`메소드의 `axis=1`일 때는 row단위로 매핑함수가 적용된다. `axis =1`일 때, 시리즈를 반환하는 매핑함수를 사용할 경우에는 `axis=0`일 때와 큰 차이가 없다. 하지만, **하나의 값을 반환하는 매핑함수**가 사용될 경우에는 각 행의 요소를 매핑함수에 전달하여 얻은 값이 행마다 리턴된다. 

다음은 `apply`메소드가 `axis=1`일 때의 예제이다.
![Untitled 2](https://user-images.githubusercontent.com/53929665/155876329-bfbe5317-48cd-4b8b-b3f9-8236111fa80c.png)

```python
test_df = pd.DataFrame([[1, 2, 3],
                    [4, 5, 6],
                    [7, 8, 9]],
                   columns=['col1', 'col2', 'col3'],
                   index=['row1', 'row2', 'row3'])

axis1_df0 = test_df.apply(np.sum, axis = 1)

print(axis1_df0)
```

```
row1     6
row2    15
row3    24
dtype: int64
```

<br>

이제 `return_type`파라미터에 따른 결과를 확인해보도록 하자.

`return_type=’expand’`는 DataFrame의 column이 확장되는 케이스에서 활용하기에 매우 유용하다. 

먼저, 다음 코드는 두 개의 반환 결과를 담고 있는 tuple에 대한 Series를 DataFrame으로 치환하여 원본 DataFrame에 추가하는 3단계의 과정을 수행한다.

```python
test_df = pd.DataFrame([[1, 2],
                    [4, 5],
                    [7, 8]],
                   columns=['col1', 'col2'],
                   index=['row1', 'row2', 'row3'])

def _add_mul(x, arg1, arg2):
    return (x[arg1]+x[arg2], x[arg1]*x[arg2])

# Series반환
result_df = test_df.apply(_add_mul, axis = 1, args=('col1', 'col2'))
# 반환된 Series를 DataFrame으로 변환
result_df = pd.DataFrame([[a, b] for a, b in result_df.values], 
                        columns = ['add', 'mul'], index = result_df.index)
# 변환시킨 DataFrame을 기존 DataFrame에 추가
result_df = pd.concat([test_df, result_df], axis = 1)

print(result_df)
```

```
      col1  col2  add  mul
row1     1     2    3    2
row2     4     5    9   20
row3     7     8   15   56
```

<br>

하지만, `result_type`에 `‘expand’`를 사용하면 아래와 같이 간결하게 해결할 수 있다. 

```python
test_df = pd.DataFrame([[1, 2],
                    [4, 5],
                    [7, 8]],
                   columns=['col1', 'col2'],
                   index=['row1', 'row2', 'row3'])

def _add_mul(x, arg1, arg2):
    return (x[arg1]+x[arg2], x[arg1]*x[arg2])

test_df[['sum', 'mul']] = test_df.apply(_add_mul, 
                                    axis = 1, args=('col1', 'col2'), 
                                    result_type='expand')

print(test_df)
```

```
      col1  col2  sum  mul
row1     1     2    3    2
row2     4     5    9   20
row3     7     8   15   56
```

<br>

다음은 `result_type`이 `'broadcast'`로 지정된 경우이다. 해당 옵션을 지정했을 경우에는 매핑함수를 통해서 반드시 단일 결과로 이루어진 Series객체가 반환되어야한다. 그리고, 이렇게 반환된 Series는 원본 DataFrame의 열 형식대로 아래와 같이 확장된다.

```python
test_df = pd.DataFrame([[1, 2],
                    [4, 5],
                    [7, 8]],
                   columns=['col1', 'col2'],
                   index=['row1', 'row2', 'row3'])

print('[ 원본 ]\n', test_df)
print()
print('[ broadcast apply 결과 ]\n', test_df.apply(np.sum, axis = 1, result_type = 'broadcast'))
```

```
[ 원본 ]
       col1  col2
row1     1     2
row2     4     5
row3     7     8

[ broadcast apply 결과 ]
       col1  col2
row1     3     3
row2     9     9
row3    15    15
```


<br>

---

## <span style="color:navy">References<span>

- [pandas doc : pandas.Series.map](https://pandas.pydata.org/docs/reference/api/pandas.Series.map.html)
- [pandas doc : pandas.Series.apply](https://pandas.pydata.org/docs/reference/api/pandas.Series.apply.html?highlight=apply#pandas.Series.apply)
- [pandas doc : pandas.DataFrame.apply](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.apply.html?highlight=apply#pandas.DataFrame.apply)
- 오승환, 『파이썬 머신러닝 판다스 데이터 분석』, 정보문화사 (2019)