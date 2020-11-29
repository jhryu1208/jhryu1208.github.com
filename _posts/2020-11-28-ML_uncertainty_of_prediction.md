---
layout: post
title:  "[ML with Python] 2. 지도 학습 알고리즘 (7) 분류 예측의 불확실성 추정"
subtitle: "[ML with Python] 2. 지도 학습 알고리즘 (7) 분류 예측의 불확실성 추정"
categories: data
tags: ml
comments: true
---
#### [ML with Python] 2. 지도 학습 알고리즘 (7) 분류 예측의 불확실성 추정
- 본 포스팅은 예측의 불확실성 추정에 관한 기본적인 내용에 관하여 다룹니다.
- 이진 분류 
    - `decision_function`
    - `predict_proba`
- 다중 분류
    - `decision_function`
    - `predict_proba`
- 예측결과를 재현할 때 주의해야할 점
___

필요 라이브러리 import


```python
import mglearn
import numpy as np
import matplotlib.pyplot as plt
from sklearn.linear_model import LogisticRegression
from sklearn.model_selection import train_test_split
from sklearn.ensemble import GradientBoostingClassifier
from sklearn.datasets import make_circles
```

---

분류기의 `예측의 불확실성`을 추정하는 기능은 `scikit-learn`에서 많이 활용하는 기능 중 하나이다. 왜냐하면, 어떤 test point에 대해서 분류기가 예측한 클래스가 무엇인지 확인한 하고 넘어갈 것이 아니라, 그 <u>예측한 클래스에 대해 정답인지 얼마나 확신하는가</u>를 알아내는 것도 중요할 때가 많기 때문이다.<br>
ex. 병원에서의 암진료( 거짓 양성 / 거짓 음성 )

<br>

`scikit-learn`분류기에서` 불확실성을 추정할 수 있는 함수`가 아래와 같이 두가지 존재한다. 이 함수들은 대부분의 분류 클래스에서 적어도 둘 중 하나 혹은 모두를 제공한다.
- `decision_function`
- `predict_proba`

<br>

앞서 사용했던 인위적으로 만든 2차원 데이터셋을 사용해 `GradientBoostingClassifier`분류기의 `decision_function`과 `predict_proba` 메서드가 어떤 역할을 하는지 살펴보자


```python
from sklearn.ensemble import GradientBoostingClassifier
from sklearn.datasets import make_circles
X, y = make_circles(noise=0.25, factor=0.5, random_state=1)

# 예제를 위해 클래스의 이름을 "blue"와 "red"로 바꿉니다.
y_named = np.array(["blue", "red"])[y]

# 여러 배열을 한꺼번에 train_test_split에 넣을 수 있습니다.
# 훈련 세트와 테스트 세트로 나뉘는 방식은 모두 같습니다.
X_train, X_test, y_train_named, y_test_named, y_train, y_test = \
    train_test_split(X, y_named, y, random_state=0)

# 그래디언트 부스팅 모델을 만듭니다.
gbrt = GradientBoostingClassifier(random_state=0)
gbrt.fit(X_train, y_train_named)
```




    GradientBoostingClassifier(random_state=0)



<br>

---

#### <u>결정 함수 (for 이진분류)</u>

`이진 분류`에서 `decision_function` 반환값의 크기는 `(n_sample, )` 이다.<br>
아래의 예시를 같이 참조하면, 이때 `n_sample`는 `X_test`의 각 테스트 샘플들의 갯수임을 확인할 수 있다. 


```python
print("X_test.shape: {}".format(X_test.shape))
print("결정 함수 결과 형태: {}".format(gbrt.decision_function(X_test).shape))
```

    X_test.shape: (25, 2)
    결정 함수 결과 형태: (25,)
    

또한, `decision_function`의 각 테스트 샘플에 대한 반환값을 실수 형태로 나타낸다.<br>
이 반환값은 <u>특정 클래스(혹은 양성클래스)에 데이터 샘플이 속한다고 믿는 정도를 의미한다.</u> <br>
즉, 반환값이 `양수`일 경우, `양성 클래스`임을 뜻하고,`음수`일 경우, `음성 클래스(혹은 양성클래스 외 다른 클래스)`임을 의미한다.


```python
print('25개의 테스트 샘플에 대한 결정함수 : \n', gbrt.decision_function(X_test))
```

    25개의 테스트 샘플에 대한 결정함수 : 
     [ 4.13592603 -1.70169917 -3.95106099 -3.62609552  4.28986642  3.66166081
     -7.69097179  4.11001686  1.10753937  3.40782222 -6.46255955  4.28986642
      3.90156346 -1.20031247  3.66166081 -4.17231157 -1.23010079 -3.91576223
      4.03602783  4.11001686  4.11001686  0.65709014  2.69826265 -2.65673274
     -1.86776596]
    

그리고, `결정 함수의 부호`<u>만을 보고 예측 결과를 알 수 있다.</u> <br>
이는 아래에서 확인할 수 있다. 첫 번째 print문에서 양수와 음수로 분리되어 나온 반환값을 True(양수), False(음수)로 치환하여 재출력한 것을 확인할 수 있고, 두 번째 print문에서 `predict`메소드를 이용하여 샘플의 예측 클래스 값을 반환하였음을 확인할 수 있다. 그리고 이 둘의 결과 값은 같다는 것은(True = red, False = blue) 또한 확인할 수 있다. 따라서, `결정 함수의 부호`만을 이용하여 예측결과를 무엇인지 확인할 수 있는 것이다.


```python
# 실수값이 음수이면 False를 반환하도록 한다.
# 실수값이 양수이면 True를 반환하도록 한다.
print("임계치와 결정 함수 결과 비교:\n{}".format(gbrt.decision_function(X_test) > 0))
# pred = gbrt
print("예측:\n{}".format(gbrt.predict(X_test)))
```

    임계치와 결정 함수 결과 비교:
    [ True False False False  True  True False  True  True  True False  True
      True False  True False False False  True  True  True  True  True False
     False]
    예측:
    ['red' 'blue' 'blue' 'blue' 'red' 'red' 'blue' 'red' 'red' 'red' 'blue'
     'red' 'red' 'blue' 'red' 'blue' 'blue' 'blue' 'red' 'red' 'red' 'red'
     'red' 'blue' 'blue']
    

더불어, `이진 분류`에서 `classes`속성을 이용하여 위에서의 `predict`함수의 결과를 완전히 재현할 수 있다.
- `classes`속성의 두 번째 원소 = `양성 클래스`
- `classes`속성의 첫 번째 원소 = `음성 클래스`

> [참고] `np.all()`, `np.any()`
> - `np.all()` = 배열의 모든 데이터가 조건과 맞으면 True를 반환하고 하나라도 다르면 False반환
> - `np.any()` = 배열의 모든 데이터 중 조건과 맞는 데이터가 있으면 `True를 반환하고, 전혀 없으면 False반환


```python
gbrt.classes_
```




    array(['blue', 'red'], dtype='<U4')




```python
# 불리언 값을 0과 1로 변환합니다.
greater_zero = (gbrt.decision_function(X_test) > 0).astype(int)
# classes_에 인덱스로 사용합니다.
pred = gbrt.classes_[greater_zero]
# pred와 gbrt.predict의 결과를 비교합니다.
print("pred는 예측 결과와 같다: {}".format(
    np.all(pred == gbrt.predict(X_test))))
```

    pred는 예측 결과와 같다: True
    

위에서 True가 반환되었기에, 위 데이터셋에서  `결정 함수`결과와 `predict 함수`결과가 완전히 같음을 확인할 수 있다.

<br>

하지만, `decision_function`의 실수값 범위는 데이터와 모델 파라미터에 따라 달라진다. <u>따라서, 해당 함수의 출력값이 왜 저렇게 나왔느지 뭘 의미하는지 완벽하게 이해하기는 어렵다.</u> 즉 실수의 숫자부에 의미를 가지기보다 양수인지 음수인지 부호에 의미를 가지고 이를 통해 분류할 수 있다는 장점밖에 존재하지 않는다.

<br>

다음 예에서 2차원 평면의 모든 점에 대해 `decision_function`의 값을 색으로 표현하여 앞서본 `결정 경계`와 함께 그래프로 비교해보자.<br>(훈련 데이터 = 원, 테스트 데이터 = 삼각형)


```python
fig, axes = plt.subplots(1, 2, figsize=(13, 5))
mglearn.tools.plot_2d_separator(gbrt, X, ax=axes[0], alpha=.4,
                                fill=True, cm=mglearn.cm2)
scores_image = mglearn.tools.plot_2d_scores(gbrt, X, ax=axes[1],
                                            alpha=.4, cm=mglearn.ReBl)

for ax in axes:
    # 훈련 포인트와 테스트 포인트를 그리기
    mglearn.discrete_scatter(X_test[:, 0], X_test[:, 1], y_test,
                             markers='^', ax=ax)
    mglearn.discrete_scatter(X_train[:, 0], X_train[:, 1], y_train,
                             markers='o', ax=ax)
    ax.set_xlabel("property 0")
    ax.set_ylabel("property 1")
cbar = plt.colorbar(scores_image, ax=axes.tolist())
axes[0].legend(["test class 0", "test class 1", "train class 0",
                "train class 1"], ncol=4, loc=(.1, 1.1))
```




    <matplotlib.legend.Legend at 0x21849bc1948>




![ML_uncertainty_of_prediction_18_1](https://user-images.githubusercontent.com/53929665/100541895-4639c980-328a-11eb-807f-6948709a6d7f.png)


`결정 경계(좌)`와 달리 <br>`decision_function(우)`그래프에서 해당 함수는 이해하기 어려운 <u>실수 범위의 값을 반환</u>하기 때문에 <u>두 클래스 사이의 경계를 정확하게 구분하는 것은 어려운 것으로 확인된다.</u>

<br>

---

#### <u>예측 확률 (for 이진분류)</u>

`predict_proba`의 출력은 <u>각 클래스에 대한 확률</u>이다.<br> (확률이라는 의미에서 벌써 앞의 `decision_function`보다 이해하기 쉬울 것이다.)<br> 이 함수의 반환값의 크기는 `이진 분류`에서 항상 `(n_samples, 2)`이다. 


```python
print("확률 값의 형태: {}".format(gbrt.predict_proba(X_test).shape))
```

    확률 값의 형태: (25, 2)
    

`predict_proba`의 반환값에서 의미하는 바는 다음과 같다.
- 첫 번째 원소 = 첫 번째 클래스의 예측 확률
- 두 번째 원소 = 두 번째 클래스의 예측 확률
- 즉 두 클래스에 대한 확률의 합( 첫 번째 클래스의 예측 확률 + 두 번째 클래스의 예측 확률)은 항상 1이다.

따라서, `이진 분류`에서 두 클래스 중 하나는 50%이상의 확신을 가질 것이며, 그리고 그것이 예측값이 된다.<br>
만약! 이럴 일은 거의 없겠지만, 두 클래스가 정확히 동시에 50%의 확률을 보이면 예측은 랜덤하게 둘 중하나를 선택한다.


```python
print(gbrt.predict_proba(X_test)[:6], '\n ...')
```

    [[0.01573626 0.98426374]
     [0.84575653 0.15424347]
     [0.98112869 0.01887131]
     [0.97407033 0.02592967]
     [0.01352142 0.98647858]
     [0.02504637 0.97495363]] 
     ...
    

또한, `과대적합`모델의 경우 <u>예측의 확신이 강하게</u> 나타날 것이며, `복잡도가 낮은 모델`에서는 <u>예측의 확신이 약하게(즉 불확실하게)</u> 나타날 것이다. 이런 `불확실성`과 `모델의 정확도`가 동일하면 모델이 `보정(calibration)`되었다고 한다. 즉, 보정된 모델에서 70%확신을 가진 예측은 70%의 정확도를 나타낼 것이다.

<br>

앞에서 마지막으로 확인했던 것과 똑같이  데이터셋을 사용해 `결정 경계`와 `클래스 1`의 확률을 살펴보자 


```python
fig, axes = plt.subplots(1, 2, figsize=(13, 5))

mglearn.tools.plot_2d_separator(
    gbrt, X, ax=axes[0], alpha=.4, fill=True, cm=mglearn.cm2)
scores_image = mglearn.tools.plot_2d_scores(
    gbrt, X, ax=axes[1], alpha=.5, cm=mglearn.ReBl, function='predict_proba')

for ax in axes:
    # 훈련 포인트와 테스트 포인트를 그리기
    mglearn.discrete_scatter(X_test[:, 0], X_test[:, 1], y_test,
                             markers='^', ax=ax)
    mglearn.discrete_scatter(X_train[:, 0], X_train[:, 1], y_train,
                             markers='o', ax=ax)
    ax.set_xlabel("property 0")
    ax.set_ylabel("property 1")
cbar = plt.colorbar(scores_image, ax=axes.tolist())
axes[0].legend(["test class 0", "test class 1", "train class 0",
                "train class 1"], ncol=4, loc=(.1, 1.1))
```




    <matplotlib.legend.Legend at 0x2184bba7048>




![ML_uncertainty_of_prediction_26_1](https://user-images.githubusercontent.com/53929665/100541896-476af680-328a-11eb-9cc1-2b3e7477bb84.png)


`predict_proba(우)`<u>로 나타낸 경계는 이전의 </u>`decision_function`<u>보다 훨씬 명확하게 잘 나타나 있는 것</u>을 확인할 수 있다.

<br>

지금 까지는 `이진 분류`에서만 모델을 확인하였으므로, `다중 분류`에서는 어떨지 살펴볼 것이다.

<br>

---


#### <u> 다중 분류에서의 불확실성 </u>

`decision_function`과 `predict_proba`메소드는 `이진 분류`뿐만 아니라 `다중 분류`에서도 사용할 수 있다!<br>
클래스가 세 개인 iris데이터셋에서 이를 확인해보자


```python
from sklearn.datasets import load_iris

iris = load_iris()
X_train, X_test, y_train, y_test = train_test_split(
    iris.data, iris.target, random_state=42)

gbrt = GradientBoostingClassifier(learning_rate=0.01, random_state=0)
gbrt.fit(X_train, y_train)
```




    GradientBoostingClassifier(learning_rate=0.01, random_state=0)



<br>

#### decision_function

`다중 분류`에서 `decision_function`의 반환값의 크기는 `이진 분류`와는 다르다.
- `다중 분류` -> `(n_samples, n_classes)`
- `이진 분류` -> `(n_samples, )`

각 열은 <u>각 클래스에 대한 <b>확신 점수</b> </u>를 담고 있다.
- 점수 ⇧ : 해당 클래스일 가능성 ⇧
- 점수 ⇩ : 해당 클래스일 가능성 ⇩ 


```python
print("결정 함수의 결과 형태: {}".format(gbrt.decision_function(X_test).shape))
# decision function 결과 중 앞부분 일부를 확인합니다.
print("결정 함수 결과:\n{}".format(gbrt.decision_function(X_test)[:6, :]))
```

    결정 함수의 결과 형태: (38, 3)
    결정 함수 결과:
    [[-1.995715    0.04758267 -1.92720695]
     [ 0.06146394 -1.90755736 -1.92793758]
     [-1.99058203 -1.87637861  0.09686725]
     [-1.995715    0.04758267 -1.92720695]
     [-1.99730159 -0.13469108 -1.20341483]
     [ 0.06146394 -1.90755736 -1.92793758]]
    

데이터 포인트마다 점수들에서 가장 큰 값을 찾아 예측 결과를 다음과 같이 재현할 수 있다.

> [참고] `np.argmax()`, `np.argmin()`
> - `np.argmax()` = array 요소들 중 최댓값의 Index 반환
> - `np.argmin()` = array 요소들 중 최솟값의 Index 반환


```python
print("가장 큰 결정 함수의 인덱스:\n{}".format(np.argmax(gbrt.decision_function(X_test), axis=1)))
print("예측:\n{}".format(gbrt.predict(X_test)))
```

    가장 큰 결정 함수의 인덱스:
    [1 0 2 1 1 0 1 2 1 1 2 0 0 0 0 1 2 1 1 2 0 2 0 2 2 2 2 2 0 0 0 0 1 0 0 2 1
     0]
    예측:
    [1 0 2 1 1 0 1 2 1 1 2 0 0 0 0 1 2 1 1 2 0 2 0 2 2 2 2 2 0 0 0 0 1 0 0 2 1
     0]
    

<br>

#### predict_proba

`다중 분류`에서 `predict_proba`의 반환값 크기는 `이진 분류`와 똑같다. 앞에서는 `(n_samples, 2)`라고 표기했지만, 이는 `이진 분류`에서 클래스의 샘플 갯수는 단 두개 밖에 없기 때문이다. 결론적으로는 둘이 똑같은게 맞다.
- `다중 분류` -> `(n_samples, n_classes)`
- `이진 분류` -> `(n_samples, n_classes)`

그리고 똑같이 각 데이터 포인트에서 클래스 확률의 합은 1이다.


```python
# predict_proba 결과 중 앞부분 일부를 확인합니다.
print("예측 확률:\n{}".format(gbrt.predict_proba(X_test)[:6]))
# 행 방향으로 확률을 더하면 1이 됩니다.
print("합: {}".format(gbrt.predict_proba(X_test)[:6].sum(axis=1)))
```

    예측 확률:
    [[0.10217718 0.78840034 0.10942248]
     [0.78347147 0.10936745 0.10716108]
     [0.09818072 0.11005864 0.79176065]
     [0.10217718 0.78840034 0.10942248]
     [0.10360005 0.66723901 0.22916094]
     [0.78347147 0.10936745 0.10716108]]
    합: [1. 1. 1. 1. 1. 1.]
    

<br>

또한, `decision_function`과 마찬가지로 `np.argmax`를 적용해서 예측을 재현할 수 있다.


```python
print("가장 큰 예측 확률의 인덱스:\n{}".format(np.argmax(gbrt.predict_proba(X_test), axis=1)))
print("예측:\n{}".format(gbrt.predict(X_test)))
```

    가장 큰 예측 확률의 인덱스:
    [1 0 2 1 1 0 1 2 1 1 2 0 0 0 0 1 2 1 1 2 0 2 0 2 2 2 2 2 0 0 0 0 1 0 0 2 1
     0]
    예측:
    [1 0 2 1 1 0 1 2 1 1 2 0 0 0 0 1 2 1 1 2 0 2 0 2 2 2 2 2 0 0 0 0 1 0 0 2 1
     0]
    

<br>

#### 재현할 때 주의!

`다중 분류`에서 `decision_function`과 `predict_proba`는 둘 다 `np.argmax`함수를 적용해 예측 결과를 재현할 수 있었다. 하지만 주의할 것은 클래스 name이 문자열이거나 또는 정수형을 사용하지만 연속적이지 않을 수 있다. 따라서, 위에서 확인했던 것들 처럼 0, 1, 2 단순한 연속된 정수형으로 재현하기 보다는 항상 `classes_`의 값을 사용하는 것이 미연에 버그를 방지할 수 있다.


```python
logreg = LogisticRegression()
# iris 데이터셋의 타깃을 클래스 이름으로 나타내기
named_target = iris.target_names[y_train]

logreg.classes_
```




    array(['setosa', 'versicolor', 'virginica'], dtype='<U10')




```python
logreg.fit(X_train, named_target)
print("훈련 데이터에 있는 클래스 종류: {}".format(logreg.classes_))
print("예측: {}".format(logreg.predict(X_test)[:10]))
argmax_dec_func = np.argmax(logreg.decision_function(X_test), axis=1)
print("가장 큰 결정 함수의 인덱스: {}".format(argmax_dec_func[:10]))
print("인덱스를 classses_에 연결: {}".format(
       logreg.classes_[argmax_dec_func][:10]))
```

    훈련 데이터에 있는 클래스 종류: ['setosa' 'versicolor' 'virginica']
    예측: ['versicolor' 'setosa' 'virginica' 'versicolor' 'versicolor' 'setosa'
     'versicolor' 'virginica' 'versicolor' 'versicolor']
    가장 큰 결정 함수의 인덱스: [1 0 2 1 1 0 1 2 1 1]
    인덱스를 classses_에 연결: ['versicolor' 'setosa' 'virginica' 'versicolor' 'versicolor' 'setosa'
     'versicolor' 'virginica' 'versicolor' 'versicolor']
    

    C:\Users\jhryu\AppData\Local\Programs\Python\Python37\lib\site-packages\sklearn\linear_model\_logistic.py:764: ConvergenceWarning: lbfgs failed to converge (status=1):
    STOP: TOTAL NO. of ITERATIONS REACHED LIMIT.
    
    Increase the number of iterations (max_iter) or scale the data as shown in:
        https://scikit-learn.org/stable/modules/preprocessing.html
    Please also refer to the documentation for alternative solver options:
        https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression
      extra_warning_msg=_LOGISTIC_SOLVER_CONVERGENCE_MSG)
    

<br>

---

### References

- 안드레아스 뮐러, 세라 가이도, 『파이썬 라이브러리를 활용한 머신러닝』, 박해선, 한빛미디어(2017)

