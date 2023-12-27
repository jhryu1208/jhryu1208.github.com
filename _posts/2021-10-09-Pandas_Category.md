---
layout: post
title:  "[Pandas] 데이터전처리 : 범주형 데이터 처리"
subtitle: "[Pandas] 데이터전처리 : 범주형 데이터 처리"
categories: data
tags: pandas
comments: true
mathjax: true
---
#### Contents
- [구간 분할 : histogram](#구간-분할--histogram)
- [구간 별로 카테고리 지정 : cut](#구간-별로-카테고리-지정--cut)

본 포스팅은 `pandas`의 `cut`메소드를 이용한 데이터 카테고리화에 관하여 정리하였습니다.

<br>

---

## <span style="color:navy">구간 분할 : histogram<span>

범주형 카테고리를 지정하기 전에 구간 분할을 진행해야 한다. <br>

구간 분할의 경우 수동으로 지정할 수 있지만, `numpy`의 `histogram` 메소드를 이용하면 균일한 구간을 생성할 수 있다. `histogram`메소드의 경우 메소드 문자 그대로 히스토그램 그릴 때 많이 사용하며, 더불어 `normed`, `weights` , `denstiy` 등의 통계 관련 선택적 옵션이 존재한다. <br>

해당 포스팅에서는 구간을 균등하게 분할하기 위해서 `bins` 옵션만 이용하고자 한다. <br>

`bins`옵션은 히스토그램 차트의 막대 폭을 의미하며, 특정 범위를 균일한 구간을 몇개로 나눌지 지정할 때 사용한다. 기본값은 `bins=10`이다.<br>

그리고, 해당 메소드의 결과 `구간에 속하는 값의 개수`와 `경계값 리스트`를 순차적으로 반환한다.

<br>

먼저, 구간을 나누기 위한 예제 데이터를 생성한다. 아래의 예제에서는 0부터 12까지의 연속적인 정수를 가진 데이터프레임을 생성하였다.

```python
import pandas as pd
import numpy as np

df = pd.Series(list(range(0, 13))).rename('x').to_frame()
print(df)
```

```
     x
0    0
1    1
  .
  . 
  .
12  12
```

<br>

그리고, 생성한 데이터프레임을 총 4개의 구간으로 분할하기 위해서 `bins=4`로 지정하였다. 그 결과 구간에 속하는 값의 개수와 경계값 리스트를 반환하였다.  위의 정수 배열을 완벽하게 4분할 할 수 없기 때문에 마지막 구간에 4개가 할당된 것을 확인할 수 있다.

![category_fig1](https://user-images.githubusercontent.com/53929665/136663970-b670f591-5659-4fd1-9322-764070634509.png)

```python
cnt, bnd_list = np.histogram(df, bins = 4)
print(f'각 구간에 속하는 원소 개수 : {cnt}')
print(f'구간 범위 : {bnd_list}')
```

```
각 구간에 속하는 원소 개수 : [3 3 3 4]
구간 범위 : [ 0.  3.  6.  9. 12.]
```

<br>

## <span style="color:navy">구간 별로 카테고리 지정 : cut<span>

연속 변수를 범주형 이산 변수로 만들 때는 위에서 언급했던 것 처럼 특정 조건을 만족하는 행을 색인하여 추가할 수도 있지만, `pandas`의 `cut`메소드를 이용해서 이를 수행할 수 있다.

`cut`메소드의 기본 형식은 다음과 같다. 
(아래에 제시된 옵션외에도 다른 옵션들이 있으며, 참조링크를 통해 확인할 수 있다. 하지만, 아래에 제시된 옵션외에는 자주 사용될 것 같지는 않다.)

```python
pandas.cut(	x = ..., bins = ..., labels = ..., 
		include_lowest = True|False , 
		right = True|False )
```

- `x` : 구간 분할을 수행하고자하는 데이터 배열 대상을 입력받는다. (필수 입력 옵션)
- `bins` : numpy의 `bins`와 동일하다.
- `labels`
    - 각 구간에 대해 구체적인 라벨 명의 리스트를 입력받는다. (필수 입력 옵션)
    - `bins`옵션과 반드시 동일한 길이의 값을 받아야 한다.
- `include_lowest` 
    - `True`일 경우, 첫번째 구간의 반개구간을 폐구간으로 변경시킨다.
    - 첫번째 구간 : `(x, y]` → `[x, y]`, left-inclusive
    - `False`는 default이다.
- `right`
    - `True` : `(x, y]`,  default 옵션이다.
    - `False` :  `[x, y)`

<br>

다음은 `bins`메소드에 관한 예제이다. `bins` 옵션의 경우 위에서 생성한 `bnd_list`를 넘겨주었다.

- **ex1**과 달리 **ex2**에서는 `include_lowest = True` 옵션을 지정하였기 때문에 <u>첫번째 구간만 폐구간</u>이며 <u>나머지 구간의 경우 </u>`right`옵션을 별도로 지정하지 않았기 때문에 <u>왼쪽이 열려있는 반개구간</u>( `(x, y]` )범위를 따라서 범주화 된 것을 확인할 수 있다.
- **ex3**의 경우, `right = False` 옵션을 지정하였기 때문에 <u>왼쪽이 닫혀있으며 오른쪽이 열려있는 반개구간</u>( `[x, y)` )범위를 따라서 범주화 된 것을 확인할 수 있다.
- ex3에서`right = False`의 경우 객체의 큰 값인 12가 어떠한 범주에도 포함되지 않아서 `NaN`을 반환하는 것을 확인할 수 있다. 이러한 <u>큰 값을 마지막 구간에 포함시키기 위한 </u>`include_lowest`<u> 와 같은 옵션이 존재하지 않기 때문에 </u>`bins`<u>옵션이 받는 경계값 리스트의 가장 큰값에 1을 더해주어 마지막 구간이 12를 포괄하도록</u> 하였다.
- 더불어, 메소드에 의해 새로 생성된 컬럼은 `category`타입임을 확인할 수 있다.

```python
df1 = df.copy()

label_name1 = \
[
    f'🔴 : ({bnd_list[0]}, {bnd_list[1]}]', # 첫번째 구간
    f'🟡 : ({bnd_list[1]}, {bnd_list[2]}]', # 두번째 구간
    f'🟢 : ({bnd_list[2]}, {bnd_list[3]}]', # 세번쨰 구간
    f'🔵 : ({bnd_list[3]}, {bnd_list[4]}]'  # 네번째 구간
]

label_name2 = \
[
    f'🔴 : [{bnd_list[0]}, {bnd_list[1]}]', # 첫번째 구간
    f'🟡 : ({bnd_list[1]}, {bnd_list[2]}]', # 두번쨰 구간
    f'🟢 : ({bnd_list[2]}, {bnd_list[3]}]', # 세번째 구간
    f'🔵 : ({bnd_list[3]}, {bnd_list[4]}]'  # 네번째 구간
]

label_name3 = \
[
    f'🔴 : [{bnd_list[0]}, {bnd_list[1]})', # 첫번째 구간
    f'🟡 : [{bnd_list[1]}, {bnd_list[2]})', # 두번쨰 구간
    f'🟢 : [{bnd_list[2]}, {bnd_list[3]})', # 세번째 구간
    f'🔵 : [{bnd_list[3]}, {bnd_list[4]})'  # 네번째 구간
]  

label_name4 = \
[
    f'🔴 : [{bnd_list[0]}, {bnd_list[1]})', # 첫번째 구간
    f'🟡 : [{bnd_list[1]}, {bnd_list[2]})', # 두번쨰 구간
    f'🟢 : [{bnd_list[2]}, {bnd_list[3]})', # 세번째 구간
    f'🔵 : [{bnd_list[3]}, 13)'  # 네번째 구간
]  

 
# 예제1 : 기본     
df1['default'] = \
pd.cut(x = df['x'], bins = bnd_list, labels = label_name1)

# 예제2 : 첫번쨰 구간 : (] to []
df1['include_lowest=True'] = \
pd.cut(x = df['x'], bins = bnd_list, labels = label_name2, include_lowest = True)

# 예제3 : right = False : [)
df1['right=False'] = \
pd.cut(x = df['x'], bins = bnd_list, labels = label_name3, right = False)

# 예제4 : right = False & 마지막 구간 : [) to []
bnd_copy = list(bnd_list)
bnd_copy[bnd_copy.index(max(bnd_copy))] = max(bnd_copy)+1
df1['right=False & bins 최대값+1'] = \
pd.cut(x = df['x'], bins = bnd_copy, labels = label_name4, right = False) 

# 결과 출력
df1.set_index('x', inplace = True)
print(df1.info())
df1
```

![categor_fig2](https://user-images.githubusercontent.com/53929665/136663971-a6aff3cd-86b5-476a-923b-dfdcab443e96.png)

<br>

---

## References

- [numpy histogram]()
- [pandas  cut](https://pandas.pydata.org/docs/reference/api/pandas.cut.html)
- 오승환, 『파이썬 머신러닝 판다스 데이터 분석』, 정보문화사 (2019)
