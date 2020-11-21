---
layout: post
title:  "[ML with Python] 2. 지도 학습 알고리즘(4-2) 앙상블-그래디언트 부스팅"
subtitle: "[ML with Python] 2. 지도 학습 알고리즘(4-2) 앙상블-그래디언트 부스팅"
categories: data
tags: ml
comments: true
---
#### [ML with Python] 2. 지도 학습 알고리즘 (4) 앙상블 - 그래디언트 부스팅
- 본 포스팅은 지도 학습 알고리즘인 앙상블-그래디언트 부스팅에 관한 기본적인 내용에 관하여 다룹니다.
- 앙상블 (`ensemble`)
- 그래디언트 부스팅 (`Gradient Boosting`)
- 그래디언트 부스팅의 장단점과 매개변수

___

필요 라이브러리 import

```python
import mglearn
import numpy as np
from matplotlib import pyplot as plt
from sklearn.ensemble import RandomForestClassifier
from sklearn.ensemble import GradientBoostingClassifier
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

#### <u>그래디언트 부스팅 회귀 트리(Gradient Boosting Regression Tree)</u>

- 여러 개의 결정 트리를 묶어 강력한 모델을 만드는 또 다른 `앙상블` 방법이다.
- <u>이름은 회귀지만 `회귀`와 `분류`에 모두 사용할 수 있다.</u>
- `랜덤 포레스트`와 달리<br> `그래디언트 부스팅`은 이전 <u>트리의 오차를 보완하는 방식</u>을 이용해 <u>순차적</u>으로 `트리`를 만든다.<br>또한, <u>무작위성을 가지고 있지 않다. 대신 `사전 가지치기`가 사용</u>된다.
- 또한, `랜덤 포레스트`보다는 매개변수 설정에 조금 더 민감하지만,<br>잘 조정하면 더 높은 정확도를 보여준다.

<br>

`그래디언트 부스팅`의 기본 아이디어는<br>
`얕은 트리`와 같은 간단한 모델(`약한 학습기(weak learner)`)를 많이 연결하는 것이다.<br>
따라서, 각각의 `트리`는 데이터의 일부에 대해서만 예측을 잘 수행 할 수 있어서<br>
`트리`가 많이 추가될수록 성능이 좋아진다.

<br>

`그래디언트 부스팅`에서 중요한 매개변수는<br>
<u>이전 트리의 오차를 얼마나 강하게 보정할 것인지를 제어</u>하는 `learning_rate`이다.<br>
`learning_rate`가 크면 트리의 오차 보정을 강하게 하기 때문에 복잡한 모델을 생성한다.<br>
또한, `n_estimators`값을 키우면 `앙상블`에 트리가 더 많이 추가 되어 모델의 복잡도가 커지고 훈련 세트에서의 실수를 바로 잡을 기회가 더 많아진다.

<br>

아래는 `유방암 데이터셋`을 이용해 `GradientBoostingClassifier`를 사용한 예이다.<br>
기본값인 깊이가 3인 트리 100개와 `learning_rate` 0.1을 사용했다.


```python
cancer = load_breast_cancer()

X_train, X_test, y_train, y_test = train_test_split(
    cancer.data, cancer.target, random_state=0)

gbrt = GradientBoostingClassifier(random_state=0)
gbrt.fit(X_train, y_train)

print("훈련 세트 정확도: {:.3f}".format(gbrt.score(X_train, y_train)))
print("테스트 세트 정확도: {:.3f}".format(gbrt.score(X_test, y_test)))
```

    훈련 세트 정확도: 1.000
    테스트 세트 정확도: 0.965
    

`훈련 세트`의 정확도가 100%이므로 `과대적합`으로 추정된다.<br>
`과대적합`을 막기 위해서 
- `최대 깊이(max_depth)`를 줄이거나
- `사전 가지치기`를 강하게 하거나
- `학습률(learning_rate)`를 낮출 수 있다.


```python
# 최대깊이를 줄이는 경우
gbrt = GradientBoostingClassifier(random_state=0, max_depth=1)
gbrt.fit(X_train, y_train)

print("훈련 세트 정확도: {:.3f}".format(gbrt.score(X_train, y_train)))
print("테스트 세트 정확도: {:.3f}".format(gbrt.score(X_test, y_test)))
```

    훈련 세트 정확도: 0.991
    테스트 세트 정확도: 0.972
    


```python
# 학습률을 낮추는 경우
gbrt = GradientBoostingClassifier(random_state=0, learning_rate=0.01)
gbrt.fit(X_train, y_train)

print("훈련 세트 정확도: {:.3f}".format(gbrt.score(X_train, y_train)))
print("테스트 세트 정확도: {:.3f}".format(gbrt.score(X_test, y_test)))
```

    훈련 세트 정확도: 0.988
    테스트 세트 정확도: 0.965
    

위의 두 방식은 복잡도를 감소시키기에, `훈련 세트`의 정확도가 낮아졌다.<br>
- `학습률을 낮추는 것`은 테스트 세트의 성능을 조금밖에 개선 못했지만,<br>
- `트리의 최대 깊이를 낮추는 것`은 모델 성능 향상에 크게 기여했다.

<br>

다른 결정 트리 기반의 모델처럼 특성의 중요도를 시각화하면 모델을 더 잘 이해할 수 있다.<br>


```python
def plot_feature_importances_cancer(model):
    n_features = cancer.data.shape[1]
    plt.barh(range(n_features), model.feature_importances_, align='center')
    plt.yticks(np.arange(n_features), cancer.feature_names)
    plt.xlabel("feature_importances")
    plt.ylabel("property")
    plt.ylim(-1, n_features)

gbrt = GradientBoostingClassifier(random_state=0, max_depth=1)
gbrt.fit(X_train, y_train)

plot_feature_importances_cancer(gbrt)
```


    
![ML_decision_tree_ensemble_gradientboosting_9_0](https://user-images.githubusercontent.com/53929665/99261549-379dec00-2860-11eb-92cc-63799563699b.png)
    


[이전 포스팅](https://jhryu1208.github.io/data/2020/11/16/ML_decision_tree_ensemble_random_forest/)의 `랜덤 포레스트`와 `그레디언트 부스팅`의 특성 중요도가 비슷한 것으로 확인된다. 다만 `그래디언트 부스팅`은 일부 특성을 완전히 무시한다.

<br>

비슷한 종류의 데이터에서 `그래디언트 부스팅`과 `랜덤 포레스트` 둘다 잘 작동된다.<br>
보통은 더 안정적인 `랜덤 포레스트`를 먼저 적용한다.<br>
아무리 `랜덤 포레스트`가 잘 작동하더라도 예측 시간이 중요하거나<br>
머신러닝 모델에서 마지막 성능까지 쥐어 짜야 할 때는 `그레디언 부스팅`이 도움이 된다.

<br>

---

#### <u>장단점과 매개변수</u>

`그래디언트 부스팅` 결정 트리는 지도학습에서 가장 강력하고 널리 사용하는 모델 중 하나이다.<br>

- <b>장점</b>
    - 다른 트리 기반 모델처럼 특성의 스케일을 조정하지 않아도 된다.
    - 이진(binary) 특성이나 연속적인 특성에서도 잘 동작한다. 

<br>

- <b>단점</b>
    - 매개변수를 잘 조정해야만 한다.
    - 훈련 시간이 길다.
    - 트리 기반 모델의 특성상 희소한 고차원 데이터에는 잘 작동하지 못한다.
    
    
<br>

- <b>그래디언트 부스팅 트리 모델의 매개변수</b>
    - `n_estimators` : 트리의 개수 지정
    
        - 해당 매개변수가 클수록 `랜덤 포레스트`와 달리<br> `그래디언 부스팅`에서는 `모델이 복잡`해지고 `과대적합`될 가능성이 높아진다.
        
    <br>
    
    - `learning_rate` : 이전 트리의 오차를 보정하는 정도
    

        - `n_estimators`와 이 매개변수는 매우 깊게 연관되어있으며 해당 변수를 낮추면 비슷한 복잡도의 모델을 만들기 위해서는 `n_estimators`를 늘려서 더 많은 트리를 추가해야한다.
        - 일반적인 관례는 가용한 시간과 메모리 한도에서 `n_estimators`를 맞추고 나서 적절한 `learning_rate`를 찾는 것이다.
        
     <br>
     
    - `max_depth(or max_leaf_nodes)` : 트리의 복잡도를 지정
    

        - 통상 `그래디언트 부스팅`모델에서는 이 매개변수를 매우 작게 설정하며<br>트리의 깊이가 5보다 깊어지지 않게 한다.
    
    
<br>    

---

### References

- 안드레아스 뮐러, 세라 가이도, 『파이썬 라이브러리를 활용한 머신러닝』, 박해선, 한빛미디어(2017)
-   [https://tensorflow.blog/파이썬-머신러닝/2-3-6-결정-트리의-앙상블/](https://tensorflow.blog/%ed%8c%8c%ec%9d%b4%ec%8d%ac-%eb%a8%b8%ec%8b%a0%eb%9f%ac%eb%8b%9d/2-3-6-%ea%b2%b0%ec%a0%95-%ed%8a%b8%eb%a6%ac%ec%9d%98-%ec%95%99%ec%83%81%eb%b8%94/)

