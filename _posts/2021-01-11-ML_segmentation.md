---
layout: post
title:  "[ML with Python] 4.구간 분할/이산화 & 상호작용/다항식"
subtitle: "[ML with Python] 4.구간 분할/이산화 & 상호작용/다항식"
categories: data
tags: ml
comments: true
---
#### [ML with Python] 4. 구간 분할/이산화 & 상호작용/다항식
- 본 포스팅은 구간 분할/이산화 와 상호작용/다항식에 관한 기본적인 내용에 관하여 다룹니다.


<h3>Table of Contents<span class="tocSkip"></span></h3>
<div class="toc"><ul class="toc-item"><li><span><a href="#1)-구간-분할,-이산화-그리고-선형-모델,-트리-모델-(-feat.-KBinsDiscretiezer-클래스-)" data-toc-modified-id="1)-구간-분할,-이산화-그리고-선형-모델,-트리-모델-(-feat.-KBinsDiscretiezer-클래스-)-0">1) 구간 분할, 이산화 그리고 선형 모델, 트리 모델 ( feat. KBinsDiscretiezer 클래스 )</a></span></li><li><span><a href="#2)-상호작용" data-toc-modified-id="2)-상호작용-1">2) 상호작용</a></span></li><li><span><a href="#3)-다항식-(-feat.-PolynomialFeatures-)" data-toc-modified-id="3)-다항식-(-feat.-PolynomialFeatures-)-2">3) 다항식 ( feat. PolynomialFeatures )</a></span></li><li><span><a href="#4)-상호작용/다항식-적용-:-보스턴-주택-가격-데이터셋" data-toc-modified-id="4)-상호작용/다항식-적용-:-보스턴-주택-가격-데이터셋-3">4) 상호작용/다항식 적용 : 보스턴 주택 가격 데이터셋</a></span></li><li><span><a href="#5)-References" data-toc-modified-id="5)-References-4">5) References</a></span></li></ul></div>

![gaga](https://user-images.githubusercontent.com/53929665/104126544-83709b80-53a0-11eb-8870-684326136826.JPG)

---

필요라이브러리 import


```python
import numpy as np
import pandas as pd
import mglearn
import matplotlib.pyplot as plt
from sklearn.datasets import load_boston
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import MinMaxScaler
from sklearn.linear_model import LinearRegression
from sklearn.tree import DecisionTreeRegressor
from sklearn.linear_model import Ridge
from sklearn.svm import SVR
from sklearn.preprocessing import KBinsDiscretizer
from sklearn.preprocessing import PolynomialFeatures
import warnings
warnings.filterwarnings(action='ignore')
plt.rcParams['axes.unicode_minus'] = False 
plt.rc('font', family='Malgun Gothic') 
```

<br>

---

### 1) 구간 분할, 이산화 그리고 선형 모델, 트리 모델 ( feat. KBinsDiscretiezer 클래스 )

데이터를 가장 잘 표현하는 방법은 <u>데이터가 가진 의미</u> 뿐 아니라 <u>어떤 모델을 사용하는지</u>에 따라 다르다. 폭넓게 사용되어지는 두 알고리즘인 `선형 모델`과 `트리 기반 모델`은 <u>특성의 표현 방식</u>으로 인해 미치는 영향이 매우 다르다.

<br>

입력 특성이 하나 뿐인 wave 데이터셋을 이용해 `선형 회귀 모델`과 `결정 트리 회귀`를 비교해보자.


```python
from sklearn.linear_model import LinearRegression
from sklearn.tree import DecisionTreeRegressor

X, y = mglearn.datasets.make_wave(n_samples=120)
line = np.linspace(-3, 3, 1000, endpoint=False).reshape(-1, 1)

tree_reg = DecisionTreeRegressor(min_samples_leaf=3).fit(X, y)
line_reg = LinearRegression().fit(X, y)

plt.plot(line, tree_reg.predict(line), label = '결정 트리')
plt.plot(line, line_reg.predict(line), label = '선형 회귀')
plt.plot(X[:, 0], y, 'o', c='k')
plt.ylabel('회귀 출력')
plt.xlabel('입력 특성')
plt.legend(loc = 'best')
```




    <matplotlib.legend.Legend at 0x261ab443c08>




![ML_segmentation_6_1](https://user-images.githubusercontent.com/53929665/104126547-84a1c880-53a0-11eb-88e8-e42b2eca374e.png)


`선형 모델`은 선형 관계로만 모델링하므로 특성이 하나일 경우 직선으로만 나타나는 것을 확인할 수 있다. `결정 트리`의 경우는 같은 데이터로 복잡한 모델을 만들었다. 그러나 <u>이는 데이터의 표현 형태에 따라 굉장히 달라질 수 있다.</u>

<br>

연속형 데이터에 아주 강력한 `선형 모델`을 만드는 방법 하나는 <u>한 특성을 여러 특성으로 나누는 </u>`구간분할(or 이산화)(bining)`<u>이 있다.</u> 쉽게 말하자면, 연속적인 데이터를 하나의 구간으로 묶는 것이다. `KBinsDiscretizer` 클래스를 통하여 이를 구현해보자.

<br>

먼저 wave의 input 구간은 "-3 ~ 3(데이터에서 가장 작은 값 : -2.967, 데이터에서 가장 큰 값 : 2.92132162" 이다. 이를 `KBinsDiscreteizer`클래스를 이용하여 균일한 너비로 10개의 구간(`n_bis = 10`)으로 나누면 다음과 같다.


```python
from sklearn.preprocessing import KBinsDiscretizer

kb = KBinsDiscretizer(n_bins=10, strategy='uniform')
kb.fit(X)
#  이때 `bin_edges_` 메소드를 이용하여 저장되어진 경계값을 확인할 수 있다.
print("bin edges :\n", kb.bin_edges_ )
```

    bin edges :
     [array([-2.9668673 , -2.37804841, -1.78922951, -1.20041062, -0.61159173,
           -0.02277284,  0.56604605,  1.15486494,  1.74368384,  2.33250273,
            2.92132162])]
    

<br>

`KBinsDiscretizer`클래스로 형성된 경계값을 기준으로 형성된 구간마다 다음과 같이 데이터 포인트를 나눌수 있다.
- 첫 번째 구간은 특성의 값이 -2.967부터 -2.378까지의 모든 데이터 포인트를 담는다.
- 두 번째 구간은 특성의 값이 -2.237부터 -1.789까지의 모든 데이터 포인트를 담는다.
- 두 번째 구간은 특성의 값이 -1.789부터 -1.200까지의 모든 데이터 포인트를 담는다.


```python
X_result = kb.bin_edges_[0].tolist()
X_input = np.sort(X.reshape(-1))

for i in range(3):
    print('property_{0} :'.format(i+1) , X_result[i], '<= X_input <',X_result[i+1])
    print()
    print('X_input값 : \n', X_input[np.where(np.logical_and(X_input >= X_result[i], X_input < X_result[i+1]))])
    print()
    print('------------------------------------------------------------------------------------')

print()
print('and so on...')
```

    property_1 : -2.9668672972583856 <= X_input < -2.378048405572237
    
    X_input값 : 
     [-2.9668673  -2.87649303 -2.84748524 -2.81142489 -2.79366887 -2.72863627
     -2.72129752 -2.65149833 -2.6186499  -2.60969044 -2.55573209 -2.55269614
     -2.53812054 -2.46904499 -2.41396732]
    
    ------------------------------------------------------------------------------------
    property_2 : -2.378048405572237 <= X_input < -1.7892295138860876
    
    X_input값 : 
     [-2.35265144 -2.30478564 -2.28243452 -2.26777059 -2.16303684 -2.15445465
     -2.06403288 -2.06388816 -2.03267228 -1.97685526 -1.9090502  -1.89957294
     -1.89087327 -1.88057965 -1.82410283 -1.80770591 -1.80195731]
    
    ------------------------------------------------------------------------------------
    property_3 : -1.7892295138860876 <= X_input < -1.2004106221999387
    
    X_input값 : 
     [-1.72596534 -1.62721101 -1.50424663 -1.44732011 -1.37190581 -1.31439294
     -1.26149128 -1.25262516 -1.24713211]
    
    ------------------------------------------------------------------------------------
    
    and so on...
    

<br>

여기서 `transform`메소드를 이용하면 <u>각 데이터 포인트를 해당되는 구간으로 인코딩</u>시킬 수 있다. 


```python
X_binned = kb.transform(X)
print(X_binned.toarray().shape)
X_binned.toarray()[:10]
```

    (120, 10)
    




    array([[0., 0., 0., 1., 0., 0., 0., 0., 0., 0.],
           [0., 0., 0., 0., 0., 0., 0., 0., 0., 1.],
           [0., 0., 0., 0., 0., 0., 0., 1., 0., 0.],
           [0., 0., 0., 0., 0., 0., 1., 0., 0., 0.],
           [0., 1., 0., 0., 0., 0., 0., 0., 0., 0.],
           [0., 1., 0., 0., 0., 0., 0., 0., 0., 0.],
           [1., 0., 0., 0., 0., 0., 0., 0., 0., 0.],
           [0., 0., 0., 0., 0., 0., 0., 0., 1., 0.],
           [0., 0., 0., 0., 0., 0., 1., 0., 0., 0.],
           [0., 0., 0., 0., 0., 0., 0., 1., 0., 0.]])




```python
df = pd.DataFrame(X_binned.toarray())
df.columns = ['property_{0}'.format(i) for i in range(1,11)]
df['X_input'] = X.copy().reshape(-1)
df = df.set_index('X_input')
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>property_1</th>
      <th>property_2</th>
      <th>property_3</th>
      <th>property_4</th>
      <th>property_5</th>
      <th>property_6</th>
      <th>property_7</th>
      <th>property_8</th>
      <th>property_9</th>
      <th>property_10</th>
    </tr>
    <tr>
      <th>X_input</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>-0.752759</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2.704286</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1.391964</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>0.591951</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>-2.063888</th>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>2.228764</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1.822032</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>-1.880580</th>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2.355354</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>0.236053</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
<p>120 rows × 10 columns</p>
</div>



<br>

`원-핫-인코딩`된 데이터로 `선형 회귀 모델`과 `결정 트리 모델`을 새로 만들어보자.


```python
from sklearn.preprocessing import KBinsDiscretizer

# 예제를 간단하게 만들기 위해 encode = 'onehot-dense'로 지정하여 원-핫-인코딩된 밀집 행렬로 리턴하여 사용할 것이다.
kb = KBinsDiscretizer(n_bins=10, strategy='uniform', encode='onehot-dense')
# wave데이터셋을 학습데이터로 하여금 만든 원-핫-인코딩 배열을 X_binned에 저장
kb.fit(X); X_binned = kb.transform(X)

# test데이터셋이라고 볼 수 있는 line
line_binned = kb.transform(line)

# 특성구간으로 나누어진 train데이터로 각 모델을 학습시킨다.
tree_reg = DecisionTreeRegressor(min_samples_leaf=3).fit(X_binned, y)
line_reg = LinearRegression().fit(X_binned, y)

# 학습된 모델을 이용하여 특성구간으로 나누어진 test데이터를 예측
plt.plot(line, tree_reg.predict(line_binned), label = '구간 결정 트리')
plt.plot(line, line_reg.predict(line_binned), label = '구간 선형 회귀')

plt.plot(X[:, 0], y, 'o', c='k')
plt.vlines(kb.bin_edges_[0], -3, 3, linewidth=1, alpha=.2)
plt.ylabel('회귀 출력')
plt.xlabel('입력 특성')
plt.legend(loc = 'best')
```




    <matplotlib.legend.Legend at 0x261a40af9c8>




![ML_segmentation_16_1](https://user-images.githubusercontent.com/53929665/104126549-853a5f00-53a0-11eb-9f09-aed77bf8e07b.png)


`선형 회귀 모델`과 `결정 트리`가 같은 예측을 만들어내서 파선과 실선이 완전히 겹쳐진 것을 확인할 수 있다. 구간별로 이 두 모델이 예측한 것은 (실수)상수값이다. 각 구간 안에서는 특성의 값이 상수값이기 때문에 어떤 모델이든 그 구간의 포인트에 대해서는 같은 값을 예측할 것이다.

<br>

구간으로 나눈 특성을 사용하기 전과 비교하면, `선형 모델`의 경우 <u>매우 유연해진 것</u>을 알 수 있다. 반면 `결정 트리`는 <u>덜 유연</u>해졌다. `트리 모델`의 경우는 구간으로 나누는 것은 아무런 득이 없지만, `선형 모델`은 이러한 변환으로부터 큰 이득을 얻었다.

<br>

따라서, 일부 특성과 출력이 `비선형 관계`이지만, 용량이 매우 크고 고차원 데이터셋이라 `선형 모델`을 사용해야 한다면 `구간 분할`이 모델의 성능을 높이는데 아주 탁월한 방법이 될 수 있다.

<br>

---

### 2) 상호작용

특성을 풍부하게 나타내는 또 하나의 방법은 원본 데이터에 

- `상호작용(interaction)` 
- `다항식(polynomial)`

을 추가하는 것이다.

<br>

이전의 선형 모델을 `KBinsDiscretizer`클래스를 이용해서 wave데이터셋의 각 구간에 대해 상숫값을 학습했었다. 여기서 더 나아가, 이러한 `선형 모델`<u>은 이런 절편 외에도 <b>기울기</b>도 학습할 수 있다</u>. 선형 모델에 기울기를 추가하는 방법은 <u>구간으로 분할된 데이터에 원래 특성을 다시 추가하는 것</u>이다.

<br>

이를 확인하기 위해, 다음 처럼 `hstack`을 이용해서 구간별로 상숫값을 학습했던 데이터셋에 원본 특성을 다시 추가해보자. 추가 결과 기존의 10차원 데이터셋에서 11차원 데이터셋으로 증가하였음을 확인할 수 있다.


```python
X_combined = np.hstack([X, X_binned])
print(X_combined.shape)
```

    (120, 11)
    

<br>

원본 특성을 추가한 데이터셋으로 학습시킨 선형회귀 모델은 각 구간의 `절편(X)`과 `기울기(X_binned)`를 학습하였다. 


```python
reg = LinearRegression().fit(X_combined, y)

line_combined = np.hstack([line, line_binned])
plt.plot(line, reg.predict(line_combined), label = '원본 특성을 더한 선형 회귀')

plt.vlines(kb.bin_edges_[0], -3, 3, linewidth=1, alpha=.2)
plt.legend(loc = 'best')
plt.ylabel('회귀 출력')
plt.xlabel('입력 특성')
plt.plot(X[:, 0], y, 'o', c='k')
```




    [<matplotlib.lines.Line2D at 0x261a8e884c8>]



![ML_segmentation_22_1](https://user-images.githubusercontent.com/53929665/104126550-853a5f00-53a0-11eb-87b6-7a3ce268c1d1.png)


<br>

학습된 기울기는 모든 구간에 걸쳐 동일 하지만, 이러한 모든 구간에서 동일하기에 큰 이점은 없다. <u>오히려 각 구간에서 다른 기울기를 가지는 것이 더 이점이 있다</u>. 이러한 효과를 보기위해서는 `데이터 포인트가 있는 구간(X_binned)`과 `x축 사이의 상호작용 특성(구간 특성<X_binned> * 원본 특성<X>)`을 추가할 수 있다.


```python
X_product = np.hstack([X_binned, X*X_binned])
# 데이터셋은 데이터 포인트가 속한 구간과 이 구간에 원본 특성을 곱한 값을 더해 20개의 특성을 가지게된다.
print(X_product.shape)
```

    (120, 20)
    

<br>

데이터 포인트가 속한 구간과 원본 특성을 곱한 값(X*X_binned)은 구간 안에서는 원본 특성이고 다른 곳에서는 0인 것을 확인할 수 있다.


```python
df = pd.DataFrame(X*X_binned)
df.columns = ['property_{0}'.format(i) for i in range(1,11)]
df['X_input'] = X.copy().reshape(-1)
df = df.set_index('X_input')
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>property_1</th>
      <th>property_2</th>
      <th>property_3</th>
      <th>property_4</th>
      <th>property_5</th>
      <th>property_6</th>
      <th>property_7</th>
      <th>property_8</th>
      <th>property_9</th>
      <th>property_10</th>
    </tr>
    <tr>
      <th>X_input</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>-0.752759</th>
      <td>-0.0</td>
      <td>-0.000000</td>
      <td>-0.0</td>
      <td>-0.752759</td>
      <td>-0.0</td>
      <td>-0.000000</td>
      <td>-0.000000</td>
      <td>-0.000000</td>
      <td>-0.000000</td>
      <td>-0.000000</td>
    </tr>
    <tr>
      <th>2.704286</th>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>2.704286</td>
    </tr>
    <tr>
      <th>1.391964</th>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>1.391964</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>0.591951</th>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.591951</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>-2.063888</th>
      <td>-0.0</td>
      <td>-2.063888</td>
      <td>-0.0</td>
      <td>-0.000000</td>
      <td>-0.0</td>
      <td>-0.000000</td>
      <td>-0.000000</td>
      <td>-0.000000</td>
      <td>-0.000000</td>
      <td>-0.000000</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>2.228764</th>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>2.228764</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>1.822032</th>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>1.822032</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>-1.880580</th>
      <td>-0.0</td>
      <td>-1.880580</td>
      <td>-0.0</td>
      <td>-0.000000</td>
      <td>-0.0</td>
      <td>-0.000000</td>
      <td>-0.000000</td>
      <td>-0.000000</td>
      <td>-0.000000</td>
      <td>-0.000000</td>
    </tr>
    <tr>
      <th>2.355354</th>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>2.355354</td>
    </tr>
    <tr>
      <th>0.236053</th>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.236053</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
  </tbody>
</table>
<p>120 rows × 10 columns</p>
</div>



<br>

아래는 이 데이터를 사용해 만든 선형 모델의 결과이다. <br>
아래 그림에서 볼 수 있듯이 이 모델에서는 각 구간에서 절편과 기울기가 모두 다르다. 


```python
reg = LinearRegression().fit(X_product, y)

line_product = np.hstack([line_binned, line*line_binned])
plt.plot(line, reg.predict(line_product), label = '원본 특성을 곱한 선형 회귀')

plt.vlines(kb.bin_edges_[0], -3, 3, linewidth=1, alpha=.2)
plt.legend(loc = 'best')
plt.ylabel('회귀 출력')
plt.xlabel('입력 특성')
plt.plot(X[:, 0], y, 'o', c='k')
```




    [<matplotlib.lines.Line2D at 0x261aa66a788>]




![ML_segmentation_28_1](https://user-images.githubusercontent.com/53929665/104126553-85d2f580-53a0-11eb-8885-2a57f88067f9.png)


<br>

---

### 3) 다항식 ( feat. PolynomialFeatures )

특성을 풍부하게 나타내는 또 하나의 방법에는 이전에 언급했던 것과 같이 `상호작용`뿐만 아니라 원본 특성의 `다항식`을 추가하는 방법도 있다. 예를 들자면, `x^2`, `x^3`, `x^4`와 같은 특성을 추가하는 것이다. 해당 방식은 `preprocessing` 모듈의 `PolynomialFeatures`에 구현되어있다. wave데이터셋에 `PolynomialFeatures`를 사용해보자.


```python
from sklearn.preprocessing import PolynomialFeatures

# x^10까지 고차항을 추가한다.
# 기본값인 "include_bias = True"는 절편에 해당하는 1인 특성을 추가한다.
poly = PolynomialFeatures(degree=10, include_bias=False)
poly.fit(X)
X_poly = poly.transform(X)

# 10차원까지 고차항을 추가하였기 때문에 10개의 특성이 만들어진다.
print("X_poly.shape: ", X_poly.shape)
```

    X_poly.shape:  (120, 10)
    

<br>

X와 X_poly값을 비교하면 다음과 같다. index인 x_input은 기존 wave데이터셋의 데이터포인트를 의미하며, 각 열은 해당 데이터포인트의 거듭제곱을 나타낸다. 예를들어 x0의 경우 (x_input)^1, x0^2의 경우 (x_input)^2에 해당한다.


```python
df_poly = pd.DataFrame(X_poly)
df_poly['x_input'] = X

#각 특성의 차수를 알려주는 get_feature_names메서드를 사용해 특성의 의미를 알 수 있다.
column_name = poly.get_feature_names()
column_name.append('x_input')
df_poly.columns = column_name

df_poly = df_poly.set_index('x_input')
df_poly
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>x0</th>
      <th>x0^2</th>
      <th>x0^3</th>
      <th>x0^4</th>
      <th>x0^5</th>
      <th>x0^6</th>
      <th>x0^7</th>
      <th>x0^8</th>
      <th>x0^9</th>
      <th>x0^10</th>
    </tr>
    <tr>
      <th>x_input</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>-0.752759</th>
      <td>-0.752759</td>
      <td>0.566647</td>
      <td>-0.426548</td>
      <td>0.321088</td>
      <td>-0.241702</td>
      <td>0.181944</td>
      <td>-0.136960</td>
      <td>0.103098</td>
      <td>-0.077608</td>
      <td>5.841996e-02</td>
    </tr>
    <tr>
      <th>2.704286</th>
      <td>2.704286</td>
      <td>7.313162</td>
      <td>19.776880</td>
      <td>53.482337</td>
      <td>144.631526</td>
      <td>391.124988</td>
      <td>1057.713767</td>
      <td>2860.360362</td>
      <td>7735.232021</td>
      <td>2.091828e+04</td>
    </tr>
    <tr>
      <th>1.391964</th>
      <td>1.391964</td>
      <td>1.937563</td>
      <td>2.697017</td>
      <td>3.754150</td>
      <td>5.225640</td>
      <td>7.273901</td>
      <td>10.125005</td>
      <td>14.093639</td>
      <td>19.617834</td>
      <td>2.730731e+01</td>
    </tr>
    <tr>
      <th>0.591951</th>
      <td>0.591951</td>
      <td>0.350406</td>
      <td>0.207423</td>
      <td>0.122784</td>
      <td>0.072682</td>
      <td>0.043024</td>
      <td>0.025468</td>
      <td>0.015076</td>
      <td>0.008924</td>
      <td>5.282711e-03</td>
    </tr>
    <tr>
      <th>-2.063888</th>
      <td>-2.063888</td>
      <td>4.259634</td>
      <td>-8.791409</td>
      <td>18.144485</td>
      <td>-37.448187</td>
      <td>77.288869</td>
      <td>-159.515582</td>
      <td>329.222321</td>
      <td>-679.478050</td>
      <td>1.402367e+03</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>2.228764</th>
      <td>2.228764</td>
      <td>4.967387</td>
      <td>11.071131</td>
      <td>24.674933</td>
      <td>54.994591</td>
      <td>122.569939</td>
      <td>273.179411</td>
      <td>608.852310</td>
      <td>1356.987831</td>
      <td>3.024405e+03</td>
    </tr>
    <tr>
      <th>1.822032</th>
      <td>1.822032</td>
      <td>3.319802</td>
      <td>6.048788</td>
      <td>11.021087</td>
      <td>20.080779</td>
      <td>36.587831</td>
      <td>66.664215</td>
      <td>121.464364</td>
      <td>221.312014</td>
      <td>4.032377e+02</td>
    </tr>
    <tr>
      <th>-1.880580</th>
      <td>-1.880580</td>
      <td>3.536580</td>
      <td>-6.650820</td>
      <td>12.507397</td>
      <td>-23.521156</td>
      <td>44.233407</td>
      <td>-83.184444</td>
      <td>156.434973</td>
      <td>-294.188426</td>
      <td>5.532448e+02</td>
    </tr>
    <tr>
      <th>2.355354</th>
      <td>2.355354</td>
      <td>5.547692</td>
      <td>13.066779</td>
      <td>30.776891</td>
      <td>72.490474</td>
      <td>170.740726</td>
      <td>402.154851</td>
      <td>947.217033</td>
      <td>2231.031419</td>
      <td>5.254869e+03</td>
    </tr>
    <tr>
      <th>0.236053</th>
      <td>0.236053</td>
      <td>0.055721</td>
      <td>0.013153</td>
      <td>0.003105</td>
      <td>0.000733</td>
      <td>0.000173</td>
      <td>0.000041</td>
      <td>0.000010</td>
      <td>0.000002</td>
      <td>5.371599e-07</td>
    </tr>
  </tbody>
</table>
<p>120 rows × 10 columns</p>
</div>



<br>

위와 같은 `다항식 특성`을 `선형 모델`과 함께 사용하면 전형적인 `다항 회귀(polynomial regression)`모델이 된다. 다항식 특성은 1차원 데이터셋에서도 매우 부드러운 곡선을 만든다. 그러나 <u>(주의) 고차원 다항식은 데이터가 부족한 영역에서는 너무 민감하게 동작할 때가 있다</u>. 


```python
reg = LinearRegression().fit(X_poly, y)

line_poly = poly.transform(line)
plt.plot(line, reg.predict(line_poly), label = '다항 회귀')
plt.plot(X[:, 0], y, 'o', c='k')
plt.legend(loc = 'best')
plt.ylabel('회귀 출력')
plt.xlabel('입력 특성')
plt.show()


# 비교를 위해 아무런 변환도 거치지 않은 원본 데이터에 `커널SVM`모델을 학습시켜보았다.
from sklearn.svm import SVR

for gamma in [1, 10]:
    svr = SVR(gamma = gamma).fit(X, y)
    plt.plot(line, svr.predict(line), label = 'SVR gamma={0}'.format(gamma))

plt.plot(X[:, 0], y, 'o', c='k')
plt.legend(loc = 'best')
plt.ylabel('회귀 출력')
plt.xlabel('입력 특성')
plt.show()
```


![ML_segmentation_35_0](https://user-images.githubusercontent.com/53929665/104126554-85d2f580-53a0-11eb-869c-0d4e48af7086.png)



![ML_segmentation_35_1](https://user-images.githubusercontent.com/53929665/104126555-866b8c00-53a0-11eb-9b88-427dfc55dea4.png)


<br>

### 4) 상호작용/다항식 적용 : 보스턴 주택 가격 데이터셋

`상호작용`과 `다항식`을 실제로 적용해보기 위해 보스턴 주택 가격 데이터셋을 이용할 것이다.


```python
from sklearn.datasets import load_boston
from sklearn.model_selection import train_test_split

# 데이터 로드
boston = load_boston()

# test데이터셋 train데이터셋 분류 
X_train, X_test, y_train, y_test = train_test_split(boston.data, boston.target, random_state = 0)

df_boston = pd.DataFrame(boston.data)
df_boston['target'] = boston.target
df_boston = df_boston.set_index('target')
df_boston
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
      <th>7</th>
      <th>8</th>
      <th>9</th>
      <th>10</th>
      <th>11</th>
      <th>12</th>
    </tr>
    <tr>
      <th>target</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>24.0</th>
      <td>0.00632</td>
      <td>18.0</td>
      <td>2.31</td>
      <td>0.0</td>
      <td>0.538</td>
      <td>6.575</td>
      <td>65.2</td>
      <td>4.0900</td>
      <td>1.0</td>
      <td>296.0</td>
      <td>15.3</td>
      <td>396.90</td>
      <td>4.98</td>
    </tr>
    <tr>
      <th>21.6</th>
      <td>0.02731</td>
      <td>0.0</td>
      <td>7.07</td>
      <td>0.0</td>
      <td>0.469</td>
      <td>6.421</td>
      <td>78.9</td>
      <td>4.9671</td>
      <td>2.0</td>
      <td>242.0</td>
      <td>17.8</td>
      <td>396.90</td>
      <td>9.14</td>
    </tr>
    <tr>
      <th>34.7</th>
      <td>0.02729</td>
      <td>0.0</td>
      <td>7.07</td>
      <td>0.0</td>
      <td>0.469</td>
      <td>7.185</td>
      <td>61.1</td>
      <td>4.9671</td>
      <td>2.0</td>
      <td>242.0</td>
      <td>17.8</td>
      <td>392.83</td>
      <td>4.03</td>
    </tr>
    <tr>
      <th>33.4</th>
      <td>0.03237</td>
      <td>0.0</td>
      <td>2.18</td>
      <td>0.0</td>
      <td>0.458</td>
      <td>6.998</td>
      <td>45.8</td>
      <td>6.0622</td>
      <td>3.0</td>
      <td>222.0</td>
      <td>18.7</td>
      <td>394.63</td>
      <td>2.94</td>
    </tr>
    <tr>
      <th>36.2</th>
      <td>0.06905</td>
      <td>0.0</td>
      <td>2.18</td>
      <td>0.0</td>
      <td>0.458</td>
      <td>7.147</td>
      <td>54.2</td>
      <td>6.0622</td>
      <td>3.0</td>
      <td>222.0</td>
      <td>18.7</td>
      <td>396.90</td>
      <td>5.33</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>22.4</th>
      <td>0.06263</td>
      <td>0.0</td>
      <td>11.93</td>
      <td>0.0</td>
      <td>0.573</td>
      <td>6.593</td>
      <td>69.1</td>
      <td>2.4786</td>
      <td>1.0</td>
      <td>273.0</td>
      <td>21.0</td>
      <td>391.99</td>
      <td>9.67</td>
    </tr>
    <tr>
      <th>20.6</th>
      <td>0.04527</td>
      <td>0.0</td>
      <td>11.93</td>
      <td>0.0</td>
      <td>0.573</td>
      <td>6.120</td>
      <td>76.7</td>
      <td>2.2875</td>
      <td>1.0</td>
      <td>273.0</td>
      <td>21.0</td>
      <td>396.90</td>
      <td>9.08</td>
    </tr>
    <tr>
      <th>23.9</th>
      <td>0.06076</td>
      <td>0.0</td>
      <td>11.93</td>
      <td>0.0</td>
      <td>0.573</td>
      <td>6.976</td>
      <td>91.0</td>
      <td>2.1675</td>
      <td>1.0</td>
      <td>273.0</td>
      <td>21.0</td>
      <td>396.90</td>
      <td>5.64</td>
    </tr>
    <tr>
      <th>22.0</th>
      <td>0.10959</td>
      <td>0.0</td>
      <td>11.93</td>
      <td>0.0</td>
      <td>0.573</td>
      <td>6.794</td>
      <td>89.3</td>
      <td>2.3889</td>
      <td>1.0</td>
      <td>273.0</td>
      <td>21.0</td>
      <td>393.45</td>
      <td>6.48</td>
    </tr>
    <tr>
      <th>11.9</th>
      <td>0.04741</td>
      <td>0.0</td>
      <td>11.93</td>
      <td>0.0</td>
      <td>0.573</td>
      <td>6.030</td>
      <td>80.8</td>
      <td>2.5050</td>
      <td>1.0</td>
      <td>273.0</td>
      <td>21.0</td>
      <td>396.90</td>
      <td>7.88</td>
    </tr>
  </tbody>
</table>
<p>506 rows × 13 columns</p>
</div>



<br>

먼저 `MinMaxScaler`를 사용해 스케일을 0에서 1사이로 조정한다.


```python
from sklearn.preprocessing import MinMaxScaler

scaler = MinMaxScaler()
X_train_scaled = scaler.fit_transform(X_train)
X_test_scaled = scaler.fit_transform(X_test)

df_train_boston = pd.DataFrame(X_train_scaled)
df_train_boston['train_target'] = y_train
df_train_boston = df_train_boston.set_index('train_target')
df_train_boston
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
      <th>7</th>
      <th>8</th>
      <th>9</th>
      <th>10</th>
      <th>11</th>
      <th>12</th>
    </tr>
    <tr>
      <th>train_target</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>18.5</th>
      <td>0.002079</td>
      <td>0.22</td>
      <td>0.197947</td>
      <td>0.0</td>
      <td>0.094650</td>
      <td>0.391646</td>
      <td>0.693100</td>
      <td>0.619112</td>
      <td>0.260870</td>
      <td>0.272901</td>
      <td>0.691489</td>
      <td>0.980407</td>
      <td>0.474610</td>
    </tr>
    <tr>
      <th>19.6</th>
      <td>0.001090</td>
      <td>0.25</td>
      <td>0.171188</td>
      <td>0.0</td>
      <td>0.139918</td>
      <td>0.453344</td>
      <td>0.456231</td>
      <td>0.525716</td>
      <td>0.304348</td>
      <td>0.185115</td>
      <td>0.755319</td>
      <td>1.000000</td>
      <td>0.212482</td>
    </tr>
    <tr>
      <th>33.2</th>
      <td>0.001106</td>
      <td>0.40</td>
      <td>0.218109</td>
      <td>1.0</td>
      <td>0.127572</td>
      <td>0.710098</td>
      <td>0.474768</td>
      <td>0.329885</td>
      <td>0.130435</td>
      <td>0.127863</td>
      <td>0.531915</td>
      <td>0.980710</td>
      <td>0.122553</td>
    </tr>
    <tr>
      <th>13.1</th>
      <td>0.097903</td>
      <td>0.00</td>
      <td>0.646628</td>
      <td>0.0</td>
      <td>0.633745</td>
      <td>0.557578</td>
      <td>0.987642</td>
      <td>0.050355</td>
      <td>1.000000</td>
      <td>0.914122</td>
      <td>0.808511</td>
      <td>0.987594</td>
      <td>0.436596</td>
    </tr>
    <tr>
      <th>7.5</th>
      <td>0.121703</td>
      <td>0.00</td>
      <td>0.646628</td>
      <td>0.0</td>
      <td>0.604938</td>
      <td>0.617168</td>
      <td>0.905252</td>
      <td>0.058919</td>
      <td>1.000000</td>
      <td>0.914122</td>
      <td>0.808511</td>
      <td>0.053583</td>
      <td>0.682553</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>18.5</th>
      <td>0.003120</td>
      <td>0.00</td>
      <td>0.253666</td>
      <td>0.0</td>
      <td>0.222222</td>
      <td>0.411381</td>
      <td>0.735324</td>
      <td>0.323850</td>
      <td>0.173913</td>
      <td>0.190840</td>
      <td>0.744681</td>
      <td>0.985451</td>
      <td>0.283972</td>
    </tr>
    <tr>
      <th>36.4</th>
      <td>0.000903</td>
      <td>0.45</td>
      <td>0.109238</td>
      <td>0.0</td>
      <td>0.106996</td>
      <td>0.693045</td>
      <td>0.240989</td>
      <td>0.484428</td>
      <td>0.173913</td>
      <td>0.402672</td>
      <td>0.276596</td>
      <td>0.983837</td>
      <td>0.032340</td>
    </tr>
    <tr>
      <th>19.2</th>
      <td>0.001626</td>
      <td>0.00</td>
      <td>0.350073</td>
      <td>0.0</td>
      <td>0.333333</td>
      <td>0.471355</td>
      <td>0.820803</td>
      <td>0.143641</td>
      <td>0.217391</td>
      <td>0.467557</td>
      <td>0.553191</td>
      <td>0.993973</td>
      <td>0.243121</td>
    </tr>
    <tr>
      <th>16.6</th>
      <td>0.002506</td>
      <td>0.00</td>
      <td>0.236437</td>
      <td>0.0</td>
      <td>0.129630</td>
      <td>0.473079</td>
      <td>0.850669</td>
      <td>0.412260</td>
      <td>0.086957</td>
      <td>0.087786</td>
      <td>0.563830</td>
      <td>0.989510</td>
      <td>0.484255</td>
    </tr>
    <tr>
      <th>23.1</th>
      <td>0.001493</td>
      <td>0.00</td>
      <td>0.131598</td>
      <td>0.0</td>
      <td>0.257202</td>
      <td>0.385323</td>
      <td>0.881565</td>
      <td>0.129827</td>
      <td>0.173913</td>
      <td>0.208015</td>
      <td>0.425532</td>
      <td>1.000000</td>
      <td>0.367660</td>
    </tr>
  </tbody>
</table>
<p>379 rows × 13 columns</p>
</div>



<br>

이제 차수를 2로 하여 다항식 특성을 뽑는다. 해당 데이터는 기존에는 특성이 13개 였는데, 다항식 특성을 추가하여 105개까지 교차 특성으로 확장된 것을 확인할 수 있다. <u> 새로운 특성은 원래 특성의 제곱은 물론 가능한 두 특성의 조합을 모두 포함한다</u>. 


```python
poly = PolynomialFeatures(degree=2).fit(X_train_scaled)
X_train_poly = poly.transform(X_train_scaled)
X_test_poly = poly.transform(X_test_scaled)

print("X_train.shape : ", X_train.shape)
print("X_train_poly.shape : ", X_train_poly.shape)
print()
print('다항 특성 이름 : \n', poly.get_feature_names())
```

    X_train.shape :  (379, 13)
    X_train_poly.shape :  (379, 105)
    
    다항 특성 이름 : 
     ['1', 'x0', 'x1', 'x2', 'x3', 'x4', 'x5', 'x6', 'x7', 'x8', 'x9', 'x10', 'x11', 'x12', 'x0^2', 'x0 x1', 'x0 x2', 'x0 x3', 'x0 x4', 'x0 x5', 'x0 x6', 'x0 x7', 'x0 x8', 'x0 x9', 'x0 x10', 'x0 x11', 'x0 x12', 'x1^2', 'x1 x2', 'x1 x3', 'x1 x4', 'x1 x5', 'x1 x6', 'x1 x7', 'x1 x8', 'x1 x9', 'x1 x10', 'x1 x11', 'x1 x12', 'x2^2', 'x2 x3', 'x2 x4', 'x2 x5', 'x2 x6', 'x2 x7', 'x2 x8', 'x2 x9', 'x2 x10', 'x2 x11', 'x2 x12', 'x3^2', 'x3 x4', 'x3 x5', 'x3 x6', 'x3 x7', 'x3 x8', 'x3 x9', 'x3 x10', 'x3 x11', 'x3 x12', 'x4^2', 'x4 x5', 'x4 x6', 'x4 x7', 'x4 x8', 'x4 x9', 'x4 x10', 'x4 x11', 'x4 x12', 'x5^2', 'x5 x6', 'x5 x7', 'x5 x8', 'x5 x9', 'x5 x10', 'x5 x11', 'x5 x12', 'x6^2', 'x6 x7', 'x6 x8', 'x6 x9', 'x6 x10', 'x6 x11', 'x6 x12', 'x7^2', 'x7 x8', 'x7 x9', 'x7 x10', 'x7 x11', 'x7 x12', 'x8^2', 'x8 x9', 'x8 x10', 'x8 x11', 'x8 x12', 'x9^2', 'x9 x10', 'x9 x11', 'x9 x12', 'x10^2', 'x10 x11', 'x10 x12', 'x11^2', 'x11 x12', 'x12^2']
    

<br>

`상호작용 특성이 있는 데이터`와 `없는 데이터`에 대해 `Ridge`를 사용해 성능을 비교해보았을 때 상호작용 특성을 넣은 경우의 score가 높은 것을 확인할 수 있다.


```python
from sklearn.linear_model import Ridge
ridge = Ridge().fit(X_train_scaled, y_train)
print("상호작용 특성이 없을 때 점수: {:.3f}".format(ridge.score(X_test_scaled, y_test)))
ridge = Ridge().fit(X_train_poly, y_train)
print("상호작용 특성이 있을 때 점수: {:.3f}".format(ridge.score(X_test_poly, y_test)))
```

    상호작용 특성이 없을 때 점수: 0.577
    상호작용 특성이 있을 때 점수: 0.741
    

<br>

하지만, `랜덤 포레스트`와 같이 <u>복잡한 모델에서 </u>`상호작용`<u>을 사용하면 오히려 점수가 떨어지는 것</u>을 확인할 수 있다.


```python
from sklearn.ensemble import RandomForestRegressor
rf = RandomForestRegressor(n_estimators=100, random_state=0).fit(X_train_scaled, y_train)
print("상호작용 특성이 없을 때 점수: {:.3f}".format(rf.score(X_test_scaled, y_test)))
rf = RandomForestRegressor(n_estimators=100, random_state=0).fit(X_train_poly, y_train)
print("상호작용 특성이 있을 때 점수: {:.3f}".format(rf.score(X_test_poly, y_test)))
```

    상호작용 특성이 없을 때 점수: 0.785
    상호작용 특성이 있을 때 점수: 0.761
    

<br>

---

### 5) References

- 안드레아스 뮐러, 세라 가이도, 『파이썬 라이브러리를 활용한 머신러닝』, 박해선, 한빛미디어(2017)

