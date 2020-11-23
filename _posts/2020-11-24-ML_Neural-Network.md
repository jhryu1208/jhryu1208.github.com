---
layout: post
title:  "[ML with Python] 2. 지도 학습 알고리즘 (6-1) 신경망 모델(다층 퍼셉트론)"
subtitle: "[ML with Python] 2. 지도 학습 알고리즘 (6-1) 신경망 모델(다층 퍼셉트론)"
categories: data
tags: ml
comments: true
---
#### [ML with Python] 2. 지도 학습 알고리즘 (6-1) 신경망 모델(다층 퍼셉트론)
- 본 포스팅은 지도 학습 알고리즘인 신경망 모델에 관한 기본적인 내용에 관하여 다룹니다.
- 다층 퍼셉트론(`multilayer perceptrons, MLP`)

___

필요 라이브러리 import


```python
import mglearn
import graphviz
```

---

#### <u>신경망 모델_다층 퍼셉트론(multilayer perceptrons, MLP)</u>

가장 기본적인 형태의 인공신경망(Artificial Neural Networks) 구조로는 대표적으로 `다층 퍼셉트론(MLP)`이 있다. `MLP`는 입력층과 출력층 사이에 하나 이상의 은닉층이 존재하는 신경망이다. 또한, `MLP`는 선형 모델의 일반화된 모습이라고도 볼 수 있다. 

<br>

기존 선형 모델의 예측 공식은 다음과 같았다. 여기서 `w`는 가중치, `x`는  입력 특성, 그리고 `ŷ `는 학습된 계수의 가중치 합이다.
> ŷ = w[0] * x[0] + w[1] * x[1] + … + w[p] * x[p] + b

이 선형 모델은 다음과 같이 노드와 연결선으로 표현할 수 있다.
- 노드(node) : 입력 특성 `x`, 예측 `ŷ`
- 연결선 : 가중치 `w`


```python
display(mglearn.plots.plot_logistic_regression_graph())
```



![그림5](https://user-images.githubusercontent.com/53929665/99989155-a3023380-2df5-11eb-863d-d0ec81b751ea.JPG)



선형 모델에서 더 나아가 `MLP`에서는 가중치 합을 만드는 과정을 여러 번 반복되며, `은닉 유닛(hidden unit)`이 함께 구성되어 있다. 그리고 이를 이용하여 최종 결과를 산출하기 위해 다시 가중치 합을 계산한다.


```python
display(mglearn.plots.plot_single_hidden_layer_graph())
```


![그림6](https://user-images.githubusercontent.com/53929665/99989143-a1387000-2df5-11eb-9ab6-207d24d50c7c.JPG)


위의 노드와 연결선 신경망은 아래와 같이 가중치를 표시하였을 때

<img width="400" alt="2" src="https://user-images.githubusercontent.com/53929665/99986439-83b5d700-2df2-11eb-87f5-604fddf0ab9c.jpg">

<br>

여기서 `입력층`이 `가중치`가 곱해져서 `은닉층`에 도달하게되면 다음과 같은 계산이 수행된다.

![그림3](https://user-images.githubusercontent.com/53929665/99986710-de4f3300-2df2-11eb-9c79-c5368da3e299.jpg)

<br>

마지막으로, `은닉층`에서 다시 `가중치`를 거쳐 `출력층`에 도달하게되면 다음과 같이 계산되는 것을 확인할 수 있다.

![그림4](https://user-images.githubusercontent.com/53929665/99986814-076fc380-2df3-11eb-8d40-b015412f3645.jpg)

<br>

하지만, 여러 개의 가중치 합을 계산하는 것은 수학적으로 보면 하나의 가중치 합을 계산하는 것과 동일하기 때문에 `선형 함수`로 `은닉층`을 여러번 추가하더라도 `은닉층`을 1회 추가했을 때와 별반 차이가 없을 것이다. 따라서, `은닉층`과 `출력층`의 뉴런에서 출력값을 결정하는 함수인 `활성화 함수`는 선형 함수가 아닌 `비선형 함수`(ex. `시그모이드 함수` / `렐루` / `하이퍼볼릭탄젠트` 등)여야 한다.

<br>

![그림1](https://user-images.githubusercontent.com/53929665/99986429-831d4080-2df2-11eb-8c7f-f8cbdb104172.jpg)

<br>

<b><참고></b>

`렐루` 함수의 경우 아래에서 확인할 수 있듯이 0 이하의 값을 모두 잘라버리고, <br>`tanh` 함수는 낮은 입력값에 대해서는 -1로 수렴하고, 큰 입력값에 대해서는 +1로 수렴한다. 


```python
line = np.linspace(-3, 3, 100)
plt.plot(line, np.tanh(line), label="tanh")
plt.plot(line, np.maximum(line, 0), label="relu")
plt.legend(loc="best")
plt.xlabel("x")
plt.ylabel("relu(x), tanh(x)")
```




    Text(0, 0.5, 'relu(x), tanh(x)')




![ML_Neural-Network_10_1](https://user-images.githubusercontent.com/53929665/99989225-b4e3d680-2df5-11eb-917c-adc7e7f28f68.png)



<br>

따라서, 우리가 개별적으로 정해야 할 것은 `은닉층의 유닛 개수`이다. `은닉층`은 아래의 노드와 같이 계속해서 추가되어 질 수 있다. 소규모 데이터 셋일 경우 10개 정도로도 충분하지만 매우 복잡한 데이터셋에서는 10000개가 될 수도 있다. 이와 같이 `많은 은닉충`으로 구성된 대규모 신경망이 생기면 이를 `딥러닝`이라고 부른다.


```python
mglearn.plots.plot_two_hidden_layer_graph()
```




![그림7](https://user-images.githubusercontent.com/53929665/99989151-a2699d00-2df5-11eb-9511-7e43c26f53f5.JPG)



<br>

---

### References

- 안드레아스 뮐러, 세라 가이도, 『파이썬 라이브러리를 활용한 머신러닝』, 박해선, 한빛미디어(2017)

