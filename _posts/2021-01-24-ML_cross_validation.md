---
layout: post
title:  "[ML with Python] 5.모델 평가와 성능 향상 - 교차 검증"
subtitle: "[ML with Python] 5.모델 평가와 성능 향상 - 교차 검증"
categories: data
tags: ml
comments: true
---
#### [ML with Python] 5. 모델 평가와 성능 향상 - 교차 검증
- 본 포스팅은 교차 검증에 관한 기본적인 내용에 관하여 다룹니다.


<h3>Table of Contents<span class="tocSkip"></span></h3>
<div class="toc"><ul class="toc-item"><li><span><a href="#0)-Introduction" data-toc-modified-id="0)-Introduction-1">0) Introduction</a></span></li><li><span><a href="#1)-교차-검증-(Cross-Validation)" data-toc-modified-id="1)-교차-검증-(Cross-Validation)-2">1) 교차 검증 (Cross-Validation)</a></span><ul class="toc-item"><li><span><a href="#1-1)-scikit-learn에서의-교차-검증-(cross_val_score-&amp;-cross_validate)" data-toc-modified-id="1-1)-scikit-learn에서의-교차-검증-(cross_val_score-&amp;-cross_validate)-2.1">1-1) scikit-learn에서의 교차 검증 (cross_val_score &amp; cross_validate)</a></span></li><li><span><a href="#1-2)-교차-검증의-장점" data-toc-modified-id="1-2)-교차-검증의-장점-2.2">1-2) 교차 검증의 장점</a></span></li></ul></li><li><span><a href="#2)-계층별-k-겹-교차-검증" data-toc-modified-id="2)-계층별-k-겹-교차-검증-3">2) 계층별 k-겹 교차 검증</a></span></li><li><span><a href="#3)-교차-검증-분할기-KFold" data-toc-modified-id="3)-교차-검증-분할기-KFold-4">3) 교차 검증 분할기 KFold</a></span><ul class="toc-item"><li><span><a href="#3-1)-cv-=-KFold-?--&amp;-cv-=-Integer-?" data-toc-modified-id="3-1)-cv-=-KFold-?--&amp;-cv-=-Integer-?-4.1">3-1) cv = KFold ?  &amp; cv = Integer ?</a></span></li><li><span><a href="#3-2)-KFold-Shuffle" data-toc-modified-id="3-2)-KFold-Shuffle-4.2">3-2) KFold Shuffle</a></span></li></ul></li><li><span><a href="#4)-그외-전략들" data-toc-modified-id="4)-그외-전략들-5">4) 그외 전략들</a></span><ul class="toc-item"><li><span><a href="#4-1)-LOOCV" data-toc-modified-id="4-1)-LOOCV-5.1">4-1) LOOCV</a></span></li><li><span><a href="#4-2)-임의-분할-교차-검증" data-toc-modified-id="4-2)-임의-분할-교차-검증-5.2">4-2) 임의 분할 교차 검증</a></span></li><li><span><a href="#4-3)-그룹별-교차-검증" data-toc-modified-id="4-3)-그룹별-교차-검증-5.3">4-3) 그룹별 교차 검증</a></span></li><li><span><a href="#4-4)-반복-교차-검증" data-toc-modified-id="4-4)-반복-교차-검증-5.4">4-4) 반복 교차 검증</a></span></li></ul></li><li><span><a href="#5)-References" data-toc-modified-id="5)-References-6">5) References</a></span></li></ul></div>

필요라이브러리 import


```python
import mglearn
from sklearn.datasets import make_blobs
from sklearn.linear_model import LogisticRegression
from sklearn.linear_model import LinearRegression
from sklearn.model_selection import train_test_split

from sklearn.model_selection import cross_val_score
from sklearn.model_selection import cross_validate

from sklearn.model_selection import KFold
from sklearn.model_selection import StratifiedKFold
from sklearn.model_selection import LeaveOneOut
from sklearn.model_selection import ShuffleSplit
from sklearn.model_selection import RepeatedStratifiedKFold

from collections import Counter
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import warnings
warnings.filterwarnings(action='ignore')
plt.rcParams['axes.unicode_minus'] = False 
plt.rc('font', family='Malgun Gothic') 
```

<br>

---

### 0) Introduction

<br>

이전에 사용했던 `score`메서드는 <u>정확히 분류된 샘플의 비율</u>을 계산하였다.


```python
from sklearn.datasets import make_blobs
from sklearn.linear_model import LogisticRegression
from sklearn.model_selection import train_test_split

X, y = make_blobs(random_state = 0)
X_train, X_test, y_train, y_test = train_test_split(X, y, random_state = 0)
logreg = LogisticRegression().fit(X_train, y_train)
print("테스트 점수 : {:.2f}".format(logreg.score(X_test, y_test)))
```

    테스트 점수 : 0.88
    

위와 같이 데이터를 `훈련 세트`와 `테스트 세트`로 나누었던 이유는 <u>새로운 데이터에 대해 모델이 얼마나 잘 일반화시키는지</u>에 관하여 측정하기 위해서이다.

<br>

여기서 더 나아가 이번 공부(포스팅) 목표는 

- `score`메소드보다 더욱 안정적인 **일반화 성능**을 **측정**하는 방법인 `교차 검증`
- **일반화 성능**을 **향상**시키기위해 <u>매개변수를 조정하는데 유용한</u> `그리드 서치`

에 관해서 학습하는 것이다.

<br>

---

### 1) 교차 검증 (Cross-Validation)

<br>

`교차 검증`이란?

- 통계적인 평가 방법
- 주어진 데이터셋에 학습된 알고리즘이 <u>얼마나 잘 일반화되어있는지 평가</u>하는 데 이용
- 데이터를 <u>여러 번 반복해서 나누고</u> 여러 모델을 학습
- 가장 많이 사용되는 교차 검증 방법 : `k-겹 교차 검증(k-ford-cross-validation)`

<br>

교차 검증 중에서 많이 사용되는 `k-겹 교차 검증`(when k = 5, 즉  5-겹 교차 검증)은 다음과 같이 이루어진다.
- step1) 데이터를 `폴드(fold)`라는 비슷한 크기의 부분 집합 다섯 개로 나눈다.
- step2) 각 데이터마다 일련의 (총 다섯 가지의)모델을 생성한다.
    - 1st 모델 => `테스트 세트` : 1번째 폴드 & `훈련세트` : 나머지 폴드
    - 2nd 모델 => `테스트 세트` : 2번째 폴드 & `훈련세트` : 나머지 폴드
    - 3rd 모델 => `테스트 세트` : 3번째 폴드 & `훈련세트` : 나머지 폴드
    - 4th 모델 => `테스트 세트` : 4번째 폴드 & `훈련세트` : 나머지 폴드
    - 5th 모델 => `테스트 세트` : 5번째 폴드 & `훈련세트` : 나머지 폴드
- step3) 각 모델과 각 모델에서의 테스트 세트를 이용해 (총 다섯 가지의)정확도를 측정한다.


```python
mglearn.plots.plot_cross_validation()
```


![ML_cross_validation_10_0](https://user-images.githubusercontent.com/53929665/105630250-64d3cf80-5e8b-11eb-84a8-2c51a464dafd.png)


<br>

#### 1-1) scikit-learn에서의 교차 검증 (cross_val_score & cross_validate)

scikit-learn에서 `교차 검증`은 `model_selection` 모듈의 `cross_val_score`함수와 `cross_validate` 함수로 구현되어있다.

- cross_val_score : [[참조]](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.cross_val_score.html)
- cross_validate : [[참조]](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.cross_validate.html?highlight=cross_validate)

<br>

scikit-learn의 교차 검증을 이용하여 iris데이터셋에 적용한 LogisticRegression을 평가해보자.


```python
from sklearn.model_selection import cross_val_score
from sklearn.datasets import load_iris
from sklearn.linear_model import LogisticRegression

iris = load_iris()
logreg = LogisticRegression()

# scikit-learn 0.22 버전부터 기본적으로 5-겹 교차 검증으로 바뀌었다.
basic_scores = cross_val_score(logreg, iris.data, iris.target)
# 물론 cv 매개변수를 이용하여 k겹의 k를 변경가능하다.
# 하지만, 교차검증에서는 대게 5겹 교차 검증을 자주 사용한다.
cv3_scores = cross_val_score(logreg, iris.data, iris.target, cv = 3)


print('기본 교차 검증 점수 : ', basic_scores)
print('3-겹 교차 검증 점수 : ', cv3_scores)
```

    기본 교차 검증 점수 :  [0.96666667 1.         0.93333333 0.96666667 1.        ]
    3-겹 교차 검증 점수 :  [0.98 0.96 0.98]
    

<br>

보통 위와 같은 점수들을 통틀어 `교차 검증의 정확도`를 간단하게 나타낼 때는 `평균`을 이용한다.


```python
print("교차 검증 평균 점수 : {:.2f}".format(basic_scores.mean()))
```

    교차 검증 평균 점수 : 0.97
    

<br>

또한 교차 검증에서는 `cross_val_score`외에 `cross_validate`함수를 사용할 수 있다. 이 함수는 `cross_val_score`와 비슷하지만, <u> 분할마다 훈련과 테스트에 걸린 시간을 담은 딕셔너리를 반환</u>한다.
    
- fit_time : 훈련 시간
- score_time : 테스트 시간
- test_score : 훈련 점수
- train_score : 테스트 점수


```python
from sklearn.model_selection import cross_validate
res = cross_validate(logreg, iris.data, iris.target, cv = 5, return_train_score=True)

df = pd.DataFrame(res, index = ['case1', 'case2', 'case3', 'case4', 'case5'])
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
      <th>fit_time</th>
      <th>score_time</th>
      <th>test_score</th>
      <th>train_score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>case1</th>
      <td>0.015004</td>
      <td>0.0</td>
      <td>0.966667</td>
      <td>0.966667</td>
    </tr>
    <tr>
      <th>case2</th>
      <td>0.018004</td>
      <td>0.0</td>
      <td>1.000000</td>
      <td>0.966667</td>
    </tr>
    <tr>
      <th>case3</th>
      <td>0.014004</td>
      <td>0.0</td>
      <td>0.933333</td>
      <td>0.983333</td>
    </tr>
    <tr>
      <th>case4</th>
      <td>0.015003</td>
      <td>0.0</td>
      <td>0.966667</td>
      <td>0.983333</td>
    </tr>
    <tr>
      <th>case5</th>
      <td>0.014003</td>
      <td>0.0</td>
      <td>1.000000</td>
      <td>0.975000</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 모든 케이스의 평균
df.describe().loc[['mean'], :]
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
      <th>fit_time</th>
      <th>score_time</th>
      <th>test_score</th>
      <th>train_score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>mean</th>
      <td>0.015203</td>
      <td>0.0</td>
      <td>0.973333</td>
      <td>0.975</td>
    </tr>
  </tbody>
</table>
</div>



<br>

#### 1-2) 교차 검증의 장점

데이터를 `훈련 세트`와 `테스트 세트`로 ~~한 번 나누는 것~~보다 `교차 검증`을 사용하면 몇가지 장점이 있다.

- 테스트 세트에 각 폴드가 정확하게 한 번씩 들어가게되어, 한 번씩(여러번) 테스트 가능하다.
    - train_test_split으로 인해, 분류하기 좋은 데이터와 분류하기 쉬운 데이터가 극단적으로 나누어지는 경우를 방지할 수 있다.
- 모델이 훈련 데이터에 얼마나 민감한지 알 수 있다.
    - 위에서 봤던 iris데이터의 경우 90~100%사이의 정확도를 얻었기에, 어떤 데이터 그룹에 민감한지 예측가능하다.
- 분할을 한 번 했을 때 보다 데이터를 더 효과적으로 사용할 수 있다.
    - train_test_split은 데이터의 75%를 가지고 학습하지만, 5겹 교차 검증은 매 모델마다 4/5(즉 80%)의 모델을 가지고 학습할 수 있다. 뿐만아니라 k를 늘릴 수록 모델의 데이터 학습량이 많아진다.
 
교차 검증에서 주요 단점은 연산 비용이 늘어난다는 것이다.

<br>

---

### 2) 계층별 k-겹 교차 검증

<br>

하지만, 이때 <u>데이터 셋을 나열 순서대로 k개의 폴드로 나누어 교차 검증하는 것이 항상 좋지는 않다</u>.

이유는 아래의 예제를 확인해보자.


```python
from sklearn.datasets import load_iris
from collections import Counter

iris = load_iris()
print("- Iris 레이블 : \n", iris.target, '\n')
print("- 각 레이블 갯수 : \n", Counter(list(iris.target)))
```

    - Iris 레이블 : 
     [0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
     0 0 0 0 0 0 0 0 0 0 0 0 0 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
     1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 2 2 2 2 2 2 2 2 2 2 2
     2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2
     2 2] 
    
    - 각 레이블 갯수 : 
     Counter({0: 50, 1: 50, 2: 50})
    

위에서 확인할 수 있듯이 iris 데이터셋은 순차적으로 레이블이 나열되어 있으며 갯수 비율 또한 1:1:1이다. 이런 경우 `3-겹 교차 검증`을 적용할 경우 정확도가 교차 검증의 세가지 모델 모두 0이 된다. 따라서 이런 특이 케이스에서는 데이터 셋을 나열 순서대로 k개의 폴드로 나누는 교차 검증을 사용하는 것은 바람직하지 않은 것을 알 수 있다.

<br>

이런 경우를 타파하기 위한 대책으로 사용되는 수단이 `계층별 k-겹 교차 검증(stratified k-fold cross-validation)`이다. 이 검증에서는 다음과 같이 <u>클래스 비율이 전체 데이터셋의 클래스 비율과 같도록 데이터를 나눈다</u>.


```python
mglearn.plots.plot_stratified_cross_validation()
```


![ML_cross_validation_28_0](https://user-images.githubusercontent.com/53929665/105630251-656c6600-5e8b-11eb-86ac-0d909f946465.png)


<br>

`cross_validate`와 `cross_val_score`는 우선 데이터 셋을 나열순서대로 분류할 수도, 데이터를 계층적으로 분류할 수도 있다. 이를 나누는 주요 지표는 함수에 입력되는 모델이 `회귀`이냐 아니면 `분류`이냐인 것이다. 일반적으로는 아래와 같다.

- `회귀 모델` => `cross_validate`, `cross_val_score` => `k-겹 교차 검증 (KFold)` : 데이터 셋을 나열 순서대로 분류
- `분류 모델` => `cross_validate`, `cross_val_score` => `계층별 k-겹 교차 검증 (StratifiedKFold)` : 데이터 셋을 계층적 방식으로 분류

<br>

따라서, 위에서본 iris모델에서 `cross_validate`(혹은 `cross_val_score`) 함수를 사용할 때, `회귀 모델`을 사용하여 분류하게 될 경우 세 가지 모델의 교차 검증 점수가 모두 0으로 출력될 것이다.

<br>

---

### 3) 교차 검증 분할기 KFold

<br>

#### 3-1) cv = KFold ?  & cv = Integer ? 

앞에서는 cv 매개변수에 정수를 넣어 폴드의 개수를 조정했었다. 여기서 더 나아가 cv 매개변수에 `교차 검증 분할기(cross-validation splitter)`를 전달함으로써 폴드의 개수를 조정할 수 있다. 정수를 넣었을 때와 비교했을 때 생기는 장점은 좀 더 <u>데이터를 분할할 때 정교하게 제어할 수 있게 된다는 것</u>이다. 
<br>

우선 간단하게 iris 데이터에서 `KFold`분할기를 이용하여 회귀 모델(LinearRegression)과 분류 모델(Logistic Regression)에서 총 5개의 폴드로 분류해보고 위에서 사용했던 함수 cv에 정수를 넣었을 때의 `cross_val_score`와 비교해보자.


```python
from sklearn.model_selection import KFold
from sklearn.linear_model import LinearRegression
kfold = KFold(n_splits = 5)
reg = LinearRegression()

# Logistic Regression (분류)
print("case1 : 분류 모델 교차 검증 점수 (분할기 사용): \n", cross_val_score(logreg, iris.data, iris.target, cv=kfold))
print()
# Linear Regression (회귀)
print("case2 : 회귀 모델 교차 검증 점수 (분할기 사용): \n", cross_val_score(reg, iris.data, iris.target, cv=kfold))
print()
# Linear Regression (분류) => 계층별 k-겹 교차 검증 : 데이터 셋을 계층적 방식으로 분류
print("case3 : 분류 교차 검증 점수 (정수 사용): \n", cross_val_score(logreg, iris.data, iris.target, cv=5))
print()
# Linear Regression (회귀) => 데이터 셋을 나열 순서대로 분류
print("case4 : 회귀 교차 검증 점수 (정수 사용): \n", cross_val_score(reg, iris.data, iris.target, cv=5))
```

    case1 : 분류 모델 교차 검증 점수 (분할기 사용): 
     [1.         1.         0.86666667 0.93333333 0.83333333]
    
    case2 : 회귀 모델 교차 검증 점수 (분할기 사용): 
     [0.         0.85124923 0.         0.76155439 0.        ]
    
    case3 : 분류 교차 검증 점수 (정수 사용): 
     [0.96666667 1.         0.93333333 0.96666667 1.        ]
    
    case4 : 회귀 교차 검증 점수 (정수 사용): 
     [0.         0.85124923 0.         0.76155439 0.        ]
    

동일한 회귀 모델을 이용한 case2(분할기 사용)와 case3(정수 사용)에서는 동일한 결과를 보여주는 것을 확인할 수 있다. 하지만 동일한 분류 모델을 사용한 case1(분할기 사용)와 case4(정수 사용)에서는 다른 결과를 보여주는 것을 확인할 수 있다. 여기서 주목해야하는 것은 case1과 case4의 차이이다.

<br>

case1의 평균 교차검증 점수는 case4보다 낮은 것을 또한 확인할 수 있다. 왜냐하면, case1에서는 `분류` 모델을 사용했음에도 불구하고 cv 매개변수에 `교차 검증 분할기`인 `Kfold`를 입력하여 `분류 & 5-겹 교차 검증`의 조합으로 교차 검증이 진행되었기 때문이다.

<br>

따라서, 

- `cv 매개변수`에 `정수`가 사용될 경우, 
    - `cross_val_score`(혹은 `cross_validate`)함수는 모델(회귀/분류)에 따라서 `KFold`와 `StratifiedKFold`를 기본값으로 사용한다.

- `cv 매개변수`에 `교차 검증 분할기(KFold)`가 사용될 경우,
    - `cross_val_score`(혹은 `cross_validate`)함수는 모델(회귀/분류)에 상관없이 `KFold`가 적용된다.

라고 정리할 수 있다.

<br>

#### 3-2) KFold Shuffle

위에서 iris 데이터셋을 나열 순서대로 분류하게 될 경우 `3-겹 교차검증`이 적절하지 않다는 것을 언급했었다. 실제로 아래를 참조하면 iris 데이터셋의 클래스 중 하나에 대응하기에 아무것도 학습할 수 없는 것을 확인할 수 있다. 


```python
kfold = KFold(n_splits=3)
# kfold는 iris데이터셋을 순차적으로 분류하기 위해 사용하였다.
print('교차 검증 점수 : ', cross_val_score(logreg, iris.data, iris.target, cv=kfold))
```

    교차 검증 점수 :  [0. 0. 0.]
    

<br>

cv에 정수를 넘겨주어 `계층별 폴드`를 만들어서 위의 문제를 해결할 수 있지만 `KFold`를 전달할 때 <u>데이터를 섞어서 샘플의 순서를 뒤죽박죽으로 만들어</u>이를 해결할 수도 있다. 이는 간단하게 `KFold`의 `shuffle` 매개변수를 `True`로 주어 수행할 수 있다. 또한, 데이터를 섞을 때 `random_state`를 고정해서 똑같은 작업을 재현할 수 있다. (더불어, 데이터를 분할하기 전에 섞어주면 더 좋은 결과를 만들게 할 수 있다.)


```python
kfold = KFold(n_splits=3, shuffle = True)
# kfold는 iris데이터셋을 순차적으로 분류하기 위해 사용하였다.
print('교차 검증 점수 : ', cross_val_score(logreg, iris.data, iris.target, cv=kfold))
```

    교차 검증 점수 :  [0.92 0.96 1.  ]
    

<br>

---

### 4) 그외 전략들

<br>

#### 4-1) LOOCV

다른 교차 검증 방법으로 `LOOCV(Leave-One-Out Cross-Validation)`도 자주 이용된다. 

<br>

`LOOCV 교차 검증`이란?

- 폴드 하나에 샘플 하나만 들어 있는 k-겹 교차 검증
- 각 반복에서 <u>하나의 데이터 포인트</u>를 선택해 `테스트 세트`로 사용한다.
- **큰 데이터셋**에서는 시간이 매우 오래 걸리지만, **작은 데이터셋**에서는 이따금 좋은 결과를 만들어낸다.


```python
from sklearn.model_selection import LeaveOneOut

loo = LeaveOneOut()
scores = cross_val_score(logreg, iris.data, iris.target, cv=loo)

# 실제로 실행할 때 다른 교차 검증에 비해 오래 걸렸다.
print("교차 검증 분할 횟수 : ", len(scores))
print("평균 정확도 : {:.2f}".format(scores.mean()))
```

    교차 검증 분할 횟수 :  150
    평균 정확도 : 0.97
    

<br>

#### 4-2) 임의 분할 교차 검증

**매우 유연한** 또 다른 교차 검증 전략에는 `임의 분할 교차 검증(shuffle-split cross-validation)`이 있다.

<br>

`임의 분할 교차 검증`이란?

- 매개변수 인자 `train_size`만큼의 포인트로 훈련 세트를 만든다.
- 매개변수 인자 `test_size`만큼의 포인트로 테스트 세트를 만든다. (단, 훈련 세트와 중첩되지 않는다!)

- `train_size`와 `test_size`에서
    - 정수 입력 : 데이터 포인트의 절대 개수를 의미
    - 실수 입력 : 전체 데이터에서의 비율을 의미
    
- 매개변수 인자 `n_splits` 횟수만큼 분할을 반복시킬 수 있다.

<br>

`임의 분할 교차 검증`의 장점은?
- 반복 횟수를 훈련 세트나 테스트 세트의 크기와 독립적으로 조절할 때 유용하다.
- train_size와 test_size의 합을 전체와 다르게 함으로써 전체 데이터셋의 일부만 사용할 수 있다.
- 위와 같은 부분 샘플링 방식 덕에 대규모 데이터셋으로 작업할 때 큰 도움이 된다.

<br>

예를 들어 10개의 데이터셋이 있다 가정하고 이를 train_size = 5, test_size = 2, n_splits = 4로 설정할 경우 5개의 포인트의 훈련 세트, 2개 포인트의 테스트 세트로 4번 반복하여 나누는 것을 보여준다. (당연히 선택되지 않는 데이터도 존재할 수 있다.)


```python
mglearn.plots.plot_shuffle_split()
```


![ML_cross_validation_45_0](https://user-images.githubusercontent.com/53929665/105630252-6604fc80-5e8b-11eb-8979-e65314e37716.png)


<br> 다음 코드는 데이터셋의 50%를 훈련세트로, 50%를 테스트 세트로 10번 반복 분할시킨다.


```python
from sklearn.model_selection import ShuffleSplit
shuffle_split = ShuffleSplit(test_size = .5, train_size = .5, n_splits=10)
scores = cross_val_score(logreg, iris.data, iris.target, cv=shuffle_split)

# 실제로 실행할 때 다른 교차 검증에 비해 오래 걸렸다.
print("교차 검증 점수 : \n", np.round(scores, 3))
```

    교차 검증 점수 : 
     [0.933 0.947 1.    0.973 0.973 0.973 0.92  0.92  0.947 0.907]
    

<br>

더불어 `ShufleSplit`은 분류 작업에 적합하도록 계층별 버전인 `StratifiedShuffleSplit`[[참조]](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.StratifiedShuffleSplit.html)도 있다.

<br>

#### 4-3) 그룹별 교차 검증

데이터 안에 **매우 연관된 그룹**이 있을 때는 `그룹별 교차 검증`을 자주 사용한다.

<br>

다음의 상황을 가정해보자.

> 얼굴 사진에서 표정을 인식하는 시스템을 만들기 위해 100명의 사진을모았다. <br>
> <br>
> 각 사진은 사람 한명 한명에 관하여 여러 가지 표정을 담고 있다.<br>
> <br>
> 이 데이터셋에 없는 사람의 표정을 정확히 구분할 수 있는 분류기를 만드는 것이 목표다.<br>

여기서 기본적인 교차 검증을 사용할 경우 같은 사람의 사진이 훈련 세트와 테스트 세트에 모두 나타날 수 있다. 따라서 기본적인 교차 검증 사용시 새 얼굴에 대한 일반화 성능까지 정확하게 평가할 수 없게 된다. 그러므로 훈련 세트와 테스트 세트에 서로 다른 사람의 사진이 들어가도록 해야 한다.

<br>

이를 위해 사진의 사람이 누구인지 기록한 배열을 `groups` 매개변수로 전달 받을 수 있는 `GroupKFold`를 사용할 수 있다.
- `groups`배열은 훈련 세트와 테스트 세트를 만들 때 분리되지 않아야 할 그룹을 지정하는 것이다.
- `groups`는 각 데이터 포인트에 대해 각 포인트가 어떤 그룹에 속하는지를 나타낸다.



```python
mglearn.plots.plot_group_kfold()
```


![ML_cross_validation_50_0](https://user-images.githubusercontent.com/53929665/105630253-6604fc80-5e8b-11eb-9e24-add3de13b100.png)

<br>

다음의 예제에서는 4개의 그룹을 나타내고 있으며 처음 3개의 데이터 샘플은 첫 번째 그룹, 다음 4개의 샘플은 두 번째 그룹 등을 나타낸다.

<br>

#### 4-4) 반복 교차 검증

**데이터 셋의 크기가 크지 않은 경우** <u>(중요)교차 검증을 반복</u>하여  안정된 검증 점수를 얻기 위해 `RepeatedKFold`와 `RepeatedStratifiedKFold` 분할기가 있다.

- RepeatedKFold
    - 회귀에서 사용
    - KFold 클래스를 사용하여 훈련 폴드와 테스트 폴드를 분할
- RepeatedStratifiedKFold
    - 분류에서 사용
    - StratifiedKFold 클래스를 사용
- RepeatedKFold & RepeatedStratifiedKFold 공통 매개변수
    - n_splits : 분할 폴드 수(default = 5)
    - n_repeats : 반복 횟수 (<u>반복 할때마다 데이터를 다시 섞는다</u>, default = 10)

<br>

iris 데이터셋에 반복 교차 검증을 적용해보자.


```python
from sklearn.model_selection import cross_val_score, KFold, StratifiedKFold
from sklearn.datasets import load_iris
from sklearn.linear_model import LogisticRegression
from sklearn.model_selection import RepeatedStratifiedKFold

iris = load_iris()
logreg = LogisticRegression()

rskfold = RepeatedStratifiedKFold(random_state=42)
scores = cross_val_score(logreg, iris.data, iris.target, cv = rskfold)

print("교차 검증 점수 : \n", scores)
print()
print("평균 정확도 : {:.3f}".format(scores.mean()))
```

    교차 검증 점수 : 
     [1.         0.96666667 0.93333333 1.         0.93333333 0.96666667
     0.96666667 0.93333333 1.         0.96666667 0.93333333 1.
     1.         0.96666667 0.96666667 0.9        1.         1.
     0.93333333 0.96666667 0.93333333 0.96666667 0.96666667 1.
     0.96666667 1.         0.96666667 0.96666667 0.9        1.
     0.96666667 0.96666667 0.96666667 0.96666667 0.93333333 0.96666667
     0.96666667 1.         1.         0.9        0.96666667 1.
     0.9        0.96666667 0.96666667 0.9        0.96666667 0.96666667
     1.         0.96666667]
    
    평균 정확도 : 0.965
    

<br>

---

### 5) References

- 안드레아스 뮐러, 세라 가이도, 『파이썬 라이브러리를 활용한 머신러닝』, 박해선, 한빛미디어(2017)
- [scikit-learn.org](https://scikit-learn.org/stable/)

