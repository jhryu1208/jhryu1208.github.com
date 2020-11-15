---
layout: post
title:  "[ML with Python] 2. 지도 학습 알고리즘(3) 나이브 베이즈 분류"
subtitle: "[ML with Python] 2. 지도 학습 알고리즘(3) 나이브 베이즈 분류"
categories: data
tags: ml
comments: true
---
#### [ML with Python] 2. 지도 학습 알고리즘 (3) 나이브 베이즈 분류
- 본 포스팅은 지도 학습 알고리즘인 나이브 베이즈에 관한 기본적인 내용에 관하여 다룹니다.
- 나이브 베이즈(`naive bayes`)
	- `BernoulliNB`
	- `MultinomialNB` 
	-  `GaussianNB`
- 나이브 베이즈의 장단점과 매개변수


___

필요 라이브러리 import

```python
import numpy as np
```

---

#### <u>나이브 베이즈(naive bayes) 분류기</u>

`나이브 베이즈(naive bayes)` 분류기는 `선형 모델`과 매우 유사하다.<br>
`LogisticRegression`이나 `LinearSVC` 같은 선형 분류기 보다 <u>훈련 속도가 빠르지만</u>,<br>
<u>일반화 성능이 조금 뒤쳐진다.</u>

<br>

<b>`나이브베이즈(naive bayes)`</b>
- 각 특성을 개별로 취급해 파라미터를 학습하고 그 특성에서 클래스별 통계를 단순하게 취합시킨다.

<br>

- `scikit-learn`에 구현된 해당 분류기는 `GaussianNB`, `BernoulliNB`, `MultinomialNB` 이렇게 3 가지이다.
    - `GaussianNB` : 연속적인 어떤 데이터에도 적용가능
    - `BernoulliNB` : 이진(binary) 데이터에 적용가능
    - `MultinomialNB` : 카운트 데이터(ex 문장에 나타난 단어의 횟수)에 적용가능

<br>

- `BernoulliNB`와 `MultinomialNB`는 대부분 텍스트 데이터를 분류할 때 사용한다.

---

#### <u>BernoulliNB</u>

<u>각 클래스의 특성 중 0이 아닌 것이 몇 개인지 센다.</u><br>
다음 예시를 보자


```python
X = np.array([[0, 1, 0, 1],
              [1, 0, 1, 1],
              [0, 0, 0, 1],
              [1, 0, 1, 0]])
y = np.array([0, 1, 0, 1])
```

이진(binary) 특성을 4개 가진 데이터 포인트가 4개 있다.<br>
클래스는 0과 1, 두개이다.

<br>

출력 `y`의 클래스가 `0`인 경우,<br>
첫 번째 특성은 `0`이 두 번이고, 두 번째 특성은 `0`이 한 번 / `1`이 한 번이다.<br>
같은 방식으로 두 번째 클래스에 해당하는 데이터 포인트에 대해서도 계산한다.<br>

<br>

클래스 별로 `0`이 아닌 원소를 세는 과정을 요약하면 다음과 같다.


```python
counts = {}
for label in np.unique(y):
    # 클래스마다 반복
    # 특성마다 1이 나타난 횟수를 센다.
    counts[label] = X[y == label].sum(axis=0)
print("특성 카운트:\n{}".format(counts))
```

    특성 카운트:
    {0: array([0, 1, 0, 2]), 1: array([2, 0, 2, 1])}
    

<br>

---

#### <u>MultinomialNB, GaussianNB</u>

다른 두 나이브 베이즈 모델 `MultinomialNB`와 `GaussianNB`는 계산하는 통계 데이터의 종류가 조금 다르다.<br> 
- `MultinomialNB` : 클래스별로 특성의 평균을 계산
- `GaussianNB` : 클래스별로 각 특성의 표준편차와 평균을 저장

<br>

예측할 땐 데이터 포인트를 클래스의 통계 값과 비교해서 가장 잘 맞는 클래스를 예측값으로 합니다. <br>
`MultinomialNB`와 `BernoulliNB`의 예측 공식은 `선형 모델`과 형태가 같습니다.<br>
그러나 `나이브 베이즈` 모델의 `coef_`는 기울기 `w`가 아니라서 <u>선형 모델과는 의미가 다릅니다</u>

<br>

---

#### <u>장단점과 매개변수</u>


`MultinomialNB`와 `BernoulliNB`는 모델의 복잡도를 조절하는 `alpha` 매개변수 하나를 가지고 있습니다.<br>

<br>

`alpha`가 주어지면<br> 알고리즘이 모든 특성에 양의 값을 가진<br>
가상의 데이터 포인트를 `alpha` 개수만큼 추가한다.<br>
이는 <u>통계 데이터를 완만</u>하게 해주며, `alpha`가 크면 더 완만해지고 모델의 복잡도는 낮아진다. <br>

<br>

하지만, alpha에 따른 알고리즘 성능 변동은 비교적 크지 않아서,<br>
 <u>alpha 값이 성능 향상에 크게 기여하지 않는다.</u><br> 
그러나 이 값을 조정하면 어느 정도는 정확도를 높일 수 있다.<br>

<br>

`GaussianNB`는 대부분 <u>매우 고차원인 데이터셋</u>에 사용하고,<br> 
`MultinomialNB`와 `BernoulliNB` 모델은 <u>텍스트 같은 희소한 데이터를 카운트</u>하는 데 사용한다.<br>
`MultinomialNB`는 보통 <u>0이 아닌 특성이 비교적 많은 데이터셋(예를 들어 큰 문서들)</u>에서 `BernoulliNB`보다 성능이 높다.

<br>

`나이브 베이즈` 모델과 `선형` 모델의 장단점은 비슷하다.
- 훈련과 예측 속도가 빠르며 훈련 과정을 이해하기 쉽다. 
- 희소한 고차원 데이터에서 잘 작동하며 비교적 매개변수에 민감하지 않다. 
- 선형 모델로는 학습 시간이 너무 오래 걸리는 매우 큰 데이터셋에는<br> 나이브 베이즈 모델을 시도해볼 만하며 종종 사용된다.

---

### References

- 안드레아스 뮐러, 세라 가이도, 『파이썬 라이브러리를 활용한 머신러닝』, 박해선, 한빛미디어(2017)
-   [https://tensorflow.blog/파이썬-머신러닝/2-3-3-선형-모델/](https://tensorflow.blog/%ed%8c%8c%ec%9d%b4%ec%8d%ac-%eb%a8%b8%ec%8b%a0%eb%9f%ac%eb%8b%9d/2-3-3-%ec%84%a0%ed%98%95-%eb%aa%a8%eb%8d%b8/)

