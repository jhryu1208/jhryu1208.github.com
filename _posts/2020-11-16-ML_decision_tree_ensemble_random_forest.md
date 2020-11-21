---
layout: post
title:  "[ML with Python] 2. 지도 학습 알고리즘(4-3) 앙상블-랜덤 포레스트"
subtitle: "[ML with Python] 2. 지도 학습 알고리즘(4-3) 앙상블-랜덤 포레스트"
categories: data
tags: ml
comments: true
---
#### [ML with Python] 2. 지도 학습 알고리즘 (4) 앙상블 - 랜덤 포레스트
- 본 포스팅은 지도 학습 알고리즘인 앙상블-랜덤 포레스트에 관한 기본적인 내용에 관하여 다룹니다.
- 앙상블 (`ensemble`)
- 랜덤 포레스트(`random forest`)
- 랜덤 포레스트 구축 및 분석
- 랜덤 포레스트의 장단점과 매개변수

___

필요 라이브러리 import

```python
import mglearn
import numpy as np
from matplotlib import pyplot as plt
from sklearn.ensemble import RandomForestClassifier
from sklearn.datasets import make_moons
from sklearn.model_selection import train_test_split
from sklearn.datasets import load_breast_cancer
```

---

#### <u>앙상블(ensemble)</u>

- 여러 머신러닝 모델을 연결하여 더 강력한 모델을 만드는 기법이다.
- `분류`와 `회귀 문제`의 다양한 데이터셋에서 효과적인 `앙상블 모델`
    - `랜덤 포레스트(random forest)`
    - `그래디언트 부스팅(gradient boosting)`

<br>

---

#### <u>랜덤 포레스트(random forest)</u>

- `결정 트리`의 주요 단점인 `훈련 데이터`에 `과대적합`되는 경향을 회피할 수 있는 방법
- 기본적으로 조금씩 다른 여러 `결정 트리의 묶음`


`랜덤 포레스트`의 기본 근원은<br>
<b>비교적</b> 예측을 잘 할 수 있지만 데이터의 <b>일부</b>에 `과대적합`하는 경향을 가진다는데 기초한다.<br>
예컨대 잘 작동하되 서로 다른 방향으로 `과대적합`된 트리를 많이 만들고<br>
그 결과를 평균냄으로써 과대적합된 양을 줄일 수 있다.<br>
이렇게 하면 모델의 예측 성능은 유지되면서 `과대적합`이 줄어드는 것이 수학적으로 증명되어있다.

<br>

이런 전략을 구현하기 위해서는
- `결정 트리`를 많이 만들어야 한다.
- 각각의 `트리`는 타깃 예측을 잘 해야하고 동시에 다른 `트리`와 구분되어야 한다.
- `트리`를 랜덤하게 만든다.
    - how 1 : `트리`를 만들 때 사용하는 <u>데이터 포인트를 무작위</u>로 선택하는 방법
    - how 2 : 분할 테스트에서 <u>특성을 무작위</u>로 선택하는 방법
    
<br>

---

#### <u>랜덤 포레스트 구축</u>

해당 모델을 만들기 위해서는 <u>(1)`트리`의 개수를 먼저 지정</u>해야 한다.<br>
(`RandomForestRegressor` or `RandomForestClassifier`의 `n_estimators` 매개변수를 사용)

<br>

지정된 `트리`들은 완전히 <b>독립적</b>으로 만들어져야 하므로<br>
알고리즘은 각 `트리`가 고유하게 만들어지도록 무작위한 선택을 한다.<br>
즉 `트리`를 만들기 위해 먼저 <u>(2)데이터의 `부트스트랩 샘플(bootstrap sample)`을 생성</u>해야한다.<br>

> <b>부트스트랩 샘플(bootstrap sample)</b><br>
> <br>
> n_samples개의 데이터 포인트 중에서 무작위로 데이터를 n_samples 횟수만큼 반복 추출한다.<br>
> (한 샘플이 여러 번 중복 추출될 수 있다.)<br>
> <br>
> <b>Example</b><br>
> 리스트 [‘a', ‘b', ‘c', ‘d’]에서 부트스트랩 샘플을 만들면,<br>
> 가능한 부트스트랩 샘플은 [‘b’, ‘d’, ‘d’, ‘c’]도 될 수 있고<br>
> [‘d’, ‘a’, ‘d’, ‘a’]와 같은 샘플도 만들어질 수 있습니다.

<br>

이렇게 <u>(3)만든 데이터셋으로 `결정 트리`를 만든다.</u><br>

<br>

하지만, 기존 결정트리와 달리 `랜덤 포레스트`는 각 노드에서 전체 특성을 대상으로 최선의 `테스트`를 찾는 것이 아니고, 알고리즘이 각 노드에서 <u>`후보 특성`을 랜덤하게 선택</u>한 후 이 후보들 중에서 최선의 `테스트`를 찾는다. 몇개의 특성을 고를지는 `max_features`매개변수로 조정할 수 있다.

<br>

`후보 특성`을 고르는 것은 매 노드마다 반복되므로<br> 
<u>(4)트리의 각 노드는 다른 `후보 특성`들을 사용하여 `테스트`를 만든다.</u>

<br>

정리하자면,<br>
- `부트스트랩 샘플링` : 랜덤 포레스트의 트리가 조금씩 다른 데이터셋을 이용해 만듬.<br>
- `각 노드에서 특성의 일부만 사용` : 트리의 각 분기는 각기 다른 특성 부분 집합을 사용

이 두 메커니즘이 합쳐져서 <u>`랜덤 포레스트`의 모든 트리가 서로 달라지도록 만든다.</u>

<br>

또한, 이 방식의 핵심 매개변수는 `max_features`이다.
- `max_features = n_features` 경우 : 트리의 각 노드에서 모든 특성을 고려하여 무작위성이 배제됨
- `max_features = 1` 경우 : 특성을 고를 필요없고, 그냥 무작위로 선택한 하나의 특성의 임계값을 찾기만하면 됨

<br>

> 결국 
> - `max_feature`값이 커지면, <br>`랜덤 포레스트`의 트리들은 서로 매우 비슷해지고<br> 가장 두드러진 특성을 이용해 데이터를 잘 맞출 것이다.
> - `max_feature`값이 작아지면, <br>`랜덤 포레스트`의 트리들은 많이 달라지고<br> 각 트리는 데이터에 맞추기 위해 깊이가 깊어진다.

---

#### <u>랜덤 포레스트 분석</u>

two_moon 데이터셋을 가지고 `트리` 5개로 구성된 `랜덤 포레스트`모델을 만들어보자


```python
X, y = make_moons(n_samples=100, noise=0.25, random_state=3)
X_train, X_test, y_train, y_test = train_test_split(X, y, stratify=y,
 random_state=42)

forest = RandomForestClassifier(n_estimators=5, random_state=2)
forest.fit(X_train, y_train)
```




    RandomForestClassifier(n_estimators=5, random_state=2)



`랜덤 포레스트`에서 만들어진 트리들은 `estimator_`속성에 저장된다.<br>
각 `트리`에서 학습된 결정 경계와 이를 취합해 결정 경계를 시각화하면 다음과 같다.


```python
fig, axes = plt.subplots(2, 3, figsize=(20, 10))

for i, (ax, tree) in enumerate(zip(axes.ravel(), forest.estimators_)):
    ax.set_title("Tree {}".format(i))
    mglearn.plots.plot_tree_partition(X, y, tree, ax=ax)

mglearn.plots.plot_2d_separator(forest, X, fill=True, ax=axes[-1, -1], alpha=.4)
axes[-1, -1].set_title("random forest")
mglearn.discrete_scatter(X[:, 0], X[:, 1], y)
```




    [<matplotlib.lines.Line2D at 0x1f04a289550>,
     <matplotlib.lines.Line2D at 0x1f04a2898b0>]




    
![ML_decision_tree_ensemble_random_forest_9_1](https://user-images.githubusercontent.com/53929665/99189187-728e1a00-27a3-11eb-86f5-5349e5d99413.png)
    


다섯 개의 트리가 만든 결정 경계는 확연하게 다르다는 것을 알 수 있다.<br>
`부트스트랩 샘플링` 때문에 한쪽 트리에 나타나는 훈련 포인트가 <br>
다른 트리에는 포함되지 않을 수 있어 각 트리는 불완전하다.

<br>

`랜덤 포레스트`는 개개의 트리보다는 덜 `과대적합`되고 훨씬 좋은 결정 경계를 만들어준다.<br>
실제 애플리케이션에서는 매우 많은 트리를 사용하기 때문에<br>
(수백, 수천 개) 더 부드러운 결정 경계가 만들어진다.

<br>

유방암 데이터셋에 100개의 트리로 이뤄진 `랜덤 포레스트`를 살펴보자


```python
cancer = load_breast_cancer()

X_train, X_test, y_train, y_test = train_test_split(
    cancer.data, cancer.target, random_state=0)
forest = RandomForestClassifier(n_estimators=100, random_state=0)
forest.fit(X_train, y_train)

print("훈련 세트 정확도: {:.3f}".format(forest.score(X_train, y_train)))
print("테스트 세트 정확도: {:.3f}".format(forest.score(X_test, y_test)))
```

    훈련 세트 정확도: 1.000
    테스트 세트 정확도: 0.972
    

`랜덤 포레스트`는 아무런 매개변수 튜닝 없이도 선형 모델이나 단일 결정 트리보다 높은 97% 정확도를 내고 있다.<br>
`단일 결정 트리`에서 한 것처럼 `max_features` 매개변수를 조정하거나 `사전 가지치기`를 할 수 있다.<br>
하지만 `랜덤 포레스트`는 <u>기본 설정으로도 좋은 결과를 만들어줄 때가 많다.</u>

<br>

결정 트리처럼 랜덤 포레스트도 `특성 중요도`를 제공하는데<br>
각 트리의 `특성 중요도`를 <u>취합하여 계산</u>한 것입니다.<br>

일반적으로 `랜덤 포레스트`에서 제공하는 `특성 중요도`가 <u>하나의 트리에서 제공하는 것보다 더 신뢰할 만합니다.</u>


```python
def plot_feature_importances_cancer(model):
    n_features = cancer.data.shape[1]
    plt.barh(range(n_features), model.feature_importances_, align='center')
    plt.yticks(np.arange(n_features), cancer.feature_names)
    plt.xlabel("feature_importances")
    plt.ylabel("property")
    plt.ylim(-1, n_features)

plot_feature_importances_cancer(forest)
```


    
![ML_decision_tree_ensemble_random_forest_15_0](https://user-images.githubusercontent.com/53929665/99189189-73bf4700-27a3-11eb-9121-d66221735f84.png)

    


그림에서 알 수 있듯이<br>
`랜덤 포레스트`에서는 `단일 트리`의 경우보다 훨씬 많은 특성이 0 이상의 중요도 값을 갖는다.<br>
`단일 트리`의 결과와 마찬가지로 랜덤 포레스트도 “worst radius” 특성이 매우 중요하다고 보지만,<br>
가장 많은 정보를 가진 특성으로는 “worst perimeter”를 선택했다.<br>

<br>

이처럼 `랜덤 포레스트`를 만드는 `무작위성`은 알고리즘이 가능성 있는 많은 경우를 고려할 수 있도록 하므로,<br> 그 결과 `랜덤 포레스트`가 `단일 트리`보다 <u>더 넓은 시각으로 데이터를 바라볼 수 있습니다.</u>

<br>

---

#### <u>장단점과 매개변수</u>

`회귀`와 `분류`에 있어서 `랜덤 포레스트`는 현재 가장 널리 사용되는 머신러닝 알고리즘이다.<br>
랜덤 포레스트는 성능이 매우 뛰어나고 매개변수 튜닝을 많이 하지 않아도 잘 작동하며<br>
데이터의 스케일을 맞출 필요도 없다.

- <b>장점</b>
    - `단일 트리`의 단점을 보완하고 장점을 그대로 가지고 있다.

<br>

- <b>단점</b>
    - 수십, 수천 개의 트리를 자세히 분석하기 어렵고 `랜덤 포레스트`는 다른 트리보다 더 깊어지는 경향이 있기 때문에, 만약 의사 결정 과정을 간소하게 표현해야 한다면 `단일 트리`가 권장된다. 그러므로, 비전문가에게 예측 과정을 시각적으로 보여주기 위해서는 하나의 `결정 트리`가 더 좋은 선택이다.
    - 해당 모델은 말 그대로 <b>랜덤</b>하기 때문에, `random_state`를 다르게 지정하면 전혀 다른 모델이 만들어진다. 하지만, `랜덤 포레스트`의 트리가 많을 수록 `random_state`값의 변화에 따른 변동이 적다. 만약 같은 결과를 만들어야 한다면 `random_state`값을 고정해야 한다.
    - `랜덤 포레스트`는 텍스트 데이터 같이 매우 차원이 높고 희소한 데이터에는 잘 작동하지 않는다.<br>(이런 데이터는 `선형 모델`이 더 적합하다)
    
<br>

중요 매개변수는 `n_estimators`, `max_features`이고<br>  `max_depth` 같은 `사전 가지치기` 옵션이 있다.<br>

- `n_estimators`는 클수록 좋다. <br>왜냐하면 <u>더 많은 트리를 평균하면 과대적합을 줄여 더 안정적인 모델을 만들기 때문이다.</u><br>
하지만 이로 인해 잃는 것도 있는데, <u>더 많은 트리는 더 많은 메모리와 긴 훈련 시간으로 이어진다.</u>

<br>

- `max_features`는 각 트리가 얼마나 무작위가 될지를 결정하며,<br>
	작은 `max_features`는 `과대적합`을 줄여준다.<br>
	하지만, 일반적으로 아래의 기본값을 쓰는 것이 좋은 방법이다.
	- 분류 : `max_features=sqrt(n_features)`
	- 회귀 : `max_features=n_features`

	`max_features`나 `max_leaf_nodes` 매개변수를 추가하면 가끔 성능이 향상되기도 한다.

<br>

---

### References

- 안드레아스 뮐러, 세라 가이도, 『파이썬 라이브러리를 활용한 머신러닝』, 박해선, 한빛미디어(2017)
-   [https://tensorflow.blog/파이썬-머신러닝/2-3-6-결정-트리의-앙상블/](https://tensorflow.blog/%ed%8c%8c%ec%9d%b4%ec%8d%ac-%eb%a8%b8%ec%8b%a0%eb%9f%ac%eb%8b%9d/2-3-6-%ea%b2%b0%ec%a0%95-%ed%8a%b8%eb%a6%ac%ec%9d%98-%ec%95%99%ec%83%81%eb%b8%94/)


