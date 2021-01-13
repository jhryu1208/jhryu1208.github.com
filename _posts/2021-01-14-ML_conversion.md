---
layout: post
title:  "[ML with Python] 4.일변량 비선량 변환 (log, exp, sin)"
subtitle: "[ML with Python] 4.일변량 비선량 변환 (log, exp, sin)"
categories: data
tags: ml
comments: true
---
#### [ML with Python] 4. 일변량 비선량 변환 (log, exp, sin)
- 본 포스팅은 일변량 비선량 변환에 관한 기본적인 내용에 관하여 다룹니다.


<h3>Table of Contents<span class="tocSkip"></span></h3>
<div class="toc"><ul class="toc-item"><li><span><a href="#1)-일변량-비선량-변환-(log,-exp,-sin)" data-toc-modified-id="1)-일변량-비선량-변환-(log,-exp,-sin)-1">1) 일변량 비선량 변환 (log, exp, sin)</a></span></li><li><span><a href="#2)-References" data-toc-modified-id="2)-References-2">2) References</a></span></li></ul></div>

필요라이브러리 import


```python
import numpy as np
import matplotlib.pyplot as plt
from sklearn.model_selection import train_test_split
from sklearn.linear_model import Ridge
import warnings
warnings.filterwarnings(action='ignore')
plt.rcParams['axes.unicode_minus'] = False 
plt.rc('font', family='Malgun Gothic') 
```

<br>

---

### 1) 일변량 비선량 변환 (log, exp, sin)

이전 포스팅에서 `다항식`을 추가할 경우, `선형 회귀` 모델에 도움이 되었음을 살펴보았다. 한편 `다항식`말고도, `log`, `exp`, `sin`같은 수학 함수를 적용하는 방법도 특성 변환에서 유용하다. 

<br>

`선형 모델`<u>과 </u>`신경망`<u>은 각 특성의 스케일과 분포에 밀접하게 연관되어 있으며</u>, <u>특히 </u>`선형 모델`<u>의 경우 특성과 타깃값 사이에 비선형성이 존재한다면 해당 모델을 구성하기 어렵다</u>. 이런 경우, `log`와 `exp`함수는 데이터의 스케일을 변경해 `선형 모델`과 `신경망` 모델의 성능을 올리는데 도움을 준다.

<br>

더불어, 대부분의 모델은 각 특성이 `정규분포`와 비슷할 때 최고의 성능을 나타낸다. 이렇기 때문에 정규분포가 아닌 데이터를 정규분포의 형태로 스케일링할 필요가 있다. 이때, `log`와 `exp`함수를 사용하는 것이 편법이면서 가장 쉽고 효과적인 방법에 속한다. 

<br>

특히, 이런 변환이 도움되는 전형적인 경우는 `정수 카운트 데이터(ex.사용자가 얼마나 자주 로그인하는지)`를 다룰 때이
다. 

<br>

정수 카운트 데이터는 다음과 같은 특징을 지닌다.
- 정수 카운트 데이터는 음수가 존재하지 않는다.
- 특별한 통계 패턴을 따르는 경우가 많다.

<br>

실제 데이터의 속성과 비슷한 (특성이 모두 정수고 응답이 실수인)카운트 데이터를 만들어 사용해볼 것이다.


```python
# 난수 생성 초기값 객체 생성
rnd = np.random.RandomState(0)
X_org = rnd.normal(size = (1000, 3))
w = rnd.normal(size=3)

# ㅔ
X = rnd.poisson(10 * np.exp(X_org))
y = np.dot(X_org, w)

X[:10, 0]
```




    array([ 56,  81,  25,  20,  27,  18,  12,  21, 109,   7])



<br>

첫 번째 특성(1행)의 값 10개만을 살펴보면 모두 양의 정수지만 특정한 패턴을 보이지는 않는다. 하지만 각 값이 나타난 횟수를 세면 그 분포가 드러난다. `np.bincount`를 사용하여 확인해볼 경우, 2가 68회로 가장 많이 나타나며, 큰 값의 수는 빠르게 줄어드는 것을 확인할 수 있다. 그래프로 확인할 시 더 직관적으로 보인다. 이러한 <u>작은 수치가 많고 큰 수치는 몇 안되는 분포</u>는 `카운트 데이터`에서 전형적인 `푸아송(Poisson, 단위 시간 안에 일어날 이벤트 횟수를 표현하는 확률분포)` 분포이다.


```python
print("특성 출현 횟수 :\n", np.bincount(X[:, 0]))

plt.xlim(0, 160)
plt.ylim(0, 70)
bins = np.bincount(X[:, 0])
plt.bar(range(len(bins)), bins, color = 'grey')
plt.ylabel('출현횟수')
plt.xlabel('값')
```

    특성 출현 횟수 :
     [28 38 68 48 61 59 45 56 37 40 35 34 36 26 23 26 27 21 23 23 18 21 10  9
     17  9  7 14 12  7  3  8  4  5  5  3  4  2  4  1  1  3  2  5  3  8  2  5
      2  1  2  3  3  2  2  3  3  0  1  2  1  0  0  3  1  0  0  0  1  3  0  1
      0  2  0  1  1  0  0  0  0  1  0  0  2  2  0  1  1  0  0  0  0  1  1  0
      0  0  0  0  0  0  1  0  0  0  0  0  1  1  0  0  1  0  0  0  0  0  0  0
      1  0  0  0  0  1  0  0  0  0  0  0  0  0  0  0  0  0  0  0  1]
    




    Text(0.5, 0, '값')




![ML_conversion_8_2](https://user-images.githubusercontent.com/53929665/104486299-e4051000-560e-11eb-97c2-7cec48f304f3.png)


<br>

`선형 모델`들은 위와 같은 데이터를 잘 처리하지 못한다. 아래는 이 데이터에 `리지 회귀`를 적용했을 때의 점수 결과이다.


```python
from sklearn.linear_model import Ridge
X_train, X_test, y_train, y_test = train_test_split(X, y, random_state = 0)
score = Ridge().fit(X_train, y_train).score(X_test, y_test)
print('테스트 점수 : {:.3f}'.format(score))
```

    테스트 점수 : 0.622
    

비교적 낮은 R2점수가 나온 것으로 보아 `Ridge`는 X와 y의 관계를 제대로 모델링하지 못하였음을 알 수 있다.

<br>

하지만, 이런 경우 `log 스케일`로 변환하면 큰 도움이 된다. 변환 시 다음과 같이 매우 큰 값을 가진 이상치도 보이지 않으며, 리지 모델의 경우에서도 훨씬 좋은 결과가 나온다.


```python
# bins = 10, 10개로 구분할 경우
plt.hist(X_train_log[:, 0], bins = 10, color = 'gray')
plt.ylabel('출현횟수')
plt.xlabel('값')
```




    Text(0.5, 0, '값')




![ML_conversion_12_1](https://user-images.githubusercontent.com/53929665/104486302-e5363d00-560e-11eb-94c5-f456a1105cf4.png)


```python
from sklearn.linear_model import Ridge
X_train, X_test, y_train, y_test = train_test_split(X, y, random_state = 0)

# 데이터에 0이 있으면 log함수를 적용할 수 없기 때문에 log(X+1)을 사용한다.
X_train_log = np.log(X_train+1)
X_test_log = np.log(X_test+1)

score = Ridge().fit(X_train_log, y_train).score(X_test_log, y_test)
print('테스트 점수 : {:.3f}'.format(score))
```

    테스트 점수 : 0.875
    

<br>

데이터셋 모델의 조합에 최적인 변환 방법을 찾기란 상당히 까다로운 일이다. 이 예에서는 모든 특성이 같은 속성을 가지고 있다. 하지만, 실제로 이런 경우는 드물며, 일부 특성만 변환하거나 특성마다 모두 다르게 변환하기도 한다.

<br>

이런 변환은 `트리` 기반 모델에서는 불필요하지만, `선형 모델`에서는 필수이다. 가끔 `회귀`에서 타깃 변수 y를 변환하는 것이 좋을 때도 있다. `카운트(ex. 주문 횟수)`를 예측하는 경우가 전형적인 예로 log(y+1)을 사용해 변환하면 도움이 된다.


<br>

---

### 2) References

- 안드레아스 뮐러, 세라 가이도, 『파이썬 라이브러리를 활용한 머신러닝』, 박해선, 한빛미디어(2017)

