---
layout: post
title:  "[Statistics-ML] 정규화"
subtitle: "[Statistics-ML] 정규화"
categories: data
tags: sa
comments: true
---
#### [Statistics ML] 정규화
-  본 포스팅은 정규화에 관하여 다룹니다.
- 통계학과 머신러닝에서 언급되는 정규화
- 최소제곱법(MSE)
- Bias, Variance

---

#### <u>[정규화]</u>

> 파라미터를 추정할 때 손실함수에 `벌칙항`을 도입함으로써 계수가 큰 값이 되는 것을 막는 기법

정규화에 관한 설명을 살펴보면 무슨 말인지 와닿지 않는다. 이를 이해하기 위해서는 <u>머신러닝에서 좋은 모델이란 무엇인지</u>에 관하여 먼저 알아볼 필요가 있다.<br>
기본적으로 좋은 모델이란 다음과 같다.

- 현재 데이터(training data)를 잘 설명하는 모델
- 미래 데이터(testing data)를 잘 설명하는 모델

`현재 데이터를 잘 설명할 수 있는 모델`이란, Trainning error를 최소화하는 모델을 의미한다.<br>Trainning error를 최소화 시키기 위해서는 `MSE(평균 제곱의 오차)`가 최소가 되어야 한다.
<br>

<img width="150" alt="4" src="https://user-images.githubusercontent.com/53929665/97801649-9ae52700-1c81-11eb-90ba-9c0725304f14.PNG">

이때, `Expected MSE(MSE의 기댓값)`을 구하게되면  Irreducible Error, Bias, 그리고 Variance의 합이 산출된다. 여기서 Irreducible Error의 경우 우리가 어떻게 해볼 수 있는 값이 아니며, 집중해야할 것은 `Bias`와 `Variance` 이다.

- `Bias`
	- 정답과 예측값이 서로 가까운지 먼지 알려주는 지표이다.
	- Bias는 지나치게 단순화된 모델로 인한 error에 해당한다. 따라서, Bias가 클 수록 데이터가 편향되게 출력되는 것을 확인할 수 있다.
	- <u>Bias가 클수록 예측값과 멀어지게 되며, Bias가 작을수록 예측값과 가까워진다.</u> 

- `Variance`
	- 예측값들이 서로 얼마나 흩어져 있는가 를 표현
	- Variance는 지나치게 복잡한 모델(train 데이터에 지나치게 fit하려는 모델)로 인한 error에 해당한다. 따라서, <u>Variance가 지나치게 큰 모델은 일반화시키기 어려운 모델이다. </u>
	- Variance가 큰 모델을 예를 들어 설명하면 점산도에서 n차원 회귀선이 거의 모든 점을 지나면서 그려지는 모델에 해당한다. 작은 모델의 경우는 점산도에서 단순 2차원의 선형 회귀선이 이에 해당된다.

아래의 그림을 참조하면 `Bias`와 `Variance`의 관계에 대해 더 쉽게 파악할 수 있다.<br>
<img width="400" alt="1" src="https://user-images.githubusercontent.com/53929665/97800339-d975e400-1c77-11eb-8765-200434d40ad9.PNG">

위에서 설명한 `Expected MSE`는 <u>미래 데이터의 예측 성능</u>이라고도 볼 수 있다.<br> 따라서 미래 데이터를 잘 예측한다는 것은 `Expected MSE` 가 낮은 모델을 의미하는 것이다. <br> 모델을 만들 때, `Expected MSE`를 줄이기 위해서는 `Bias`, `Variance` 혹은 모두 낮추어야한다.<br>  하지만, 둘 중 하나를 포기해야 하는 경우도 종종 발생한다.<br> 이때, <u>bias를 좀 가지더라도 제일 작은 variance를 가지는 모델을 만들 수 있지 않을까 생각하게 된다.</u><br>즉 variance가 지나치게 큰 과적합이 발생하지 않는 모델을 만들자는 의미이다.

과적합 즉 overfitting을 해결하는 방법은 크게 두가지 이다.
- <b>특성(Feature)의 갯수 줄이기</b>
	- 주요 특징을 직접 선택하고 나머지는 버린다.
	- Model Selection Algorithm을 사용한다.
	<br>
- <b>정규화(Regularization)를 수행한다.</b>
	- 모든 특성을 사용하되, 파라미터의 값을 줄인다.

그럼 이제 <b>정규화</b>에 관하여 살펴보자.

<br>
이 개념을 가지고 아래의 예제를 살펴보자.

![2](https://user-images.githubusercontent.com/53929665/97800485-e515da80-1c78-11eb-8e70-33a2c048b724.jpg)

위의 그림에서 어떤 모델이 주어진 (산점도로 표시된)자료를 가장 잘 표현하는 것 일까?<br>초면에는 이게 뭔가 싶다. 하지만, 다음의 그림을 확인해보자.

![3](https://user-images.githubusercontent.com/53929665/97800486-e6470780-1c78-11eb-9fba-414c174beb45.jpg)

위의 그래프는 그래프의 차수와 Error와의 관계를 보여준다.<br>(다른 머신러닝 책에서의 경우 y축이 정확도로 나타나있으며, 위의 그래프를 x축 대칭 시킨 모습과 똑같다.)<br>여기서 확인할 수 잇는 점들은 다음과 같다.

- `Degree of Polynominal 값이 작을수록`, 훈련 데이터와 테스트 데이터 모두 높은 Error를 보여준다.
	- 즉, <u>모델이 너무 단순화되어 두 데이터셋 모두 예측값과 매우 먼 값을 출력함을 의미하기에, <br>Bias가 매우 크다는 것</u>을 확인할 수 있다.
	- 이와 같은 경우 위에서 확인한 <u>Case 1</u>과 같은 모델이다.
- `Degree of Polynominal 값이 클수록`, 훈련 데이터는 낮은 Error수치를 테스트 데이터는 높은 Error수치를 보여준다.
	- 즉, <u>모델이 Train data셋과 너무 fit되어있기 때문에 복잡도가 높음</u>을 의미한다.<br>따라서, <u>Variance가 매우 높다는 것</u>을 확인할 수 있다.
	- 이와 같은 경우 위에서 확인한 <u>Case 3</u>와 같은 모델이다.

따라서, Case 1, 3보다는 <u>Case 2가 가장 최적화된 모델이라는 것을 알 수 있다.</u>

<br>
그렇다면,  Case 3 모델처럼 모델이 너무 복잡해지거나  너무 간단해지는 것을 막기위해서는 어떻게 해야할까?<br>이를 위한 방법 중 하나가 `정규화(Regularization)` 이다.

아래의 예시를 살펴보자
<br>

<img width="150" alt="5" src="https://user-images.githubusercontent.com/53929665/97801651-9c165400-1c81-11eb-848f-48c836e1f3e0.PNG">


위의 `MSE`에서 최소가되는 beta를 찾고자 한다. 하지만, 너무 복잡한 모델은 지향하고 싶다. 이럴 경우 `벌칙항(or 정규항)`을 도입하여 계수가 큰 값이 되는 것을 막는 기법을 `정규화`라고 부른다. 
<br>

<img width="350" alt="6" src="https://user-images.githubusercontent.com/53929665/97801653-9c165400-1c81-11eb-9772-3aae871575a5.PNG">



따라서 <u>벌칙항을 추가하기 위해</u>,<br> 위와 같이 beta_3와 beta_4의 계수로 상당히 큰 숫자로 보이는 5000이라는 값을 곱해주고 전체 식에 더해주었다. 그렇다면, 위의 전체식이 최솟값을 되도록 할려면 beta_3와 beta_4는 어떤 값을 가져야만 할까?<br>

바로 <u>beta_3와 beta_4가 0에 근사한 값을 가지면 된다.</u>

따라서, case 3의 beta_3와 beta_4는 0에 근사한 값이 삽입되고, case 3는 bestfit 모델이라 부를 수 있는 case 2에 근사한 모델이 된다!


---
### References
- 파이썬으로 배우는 통계학 교과서
- https://www.youtube.com/watch?v=pJCcGK5omhE
- https://bkshin.tistory.com/entry/%EB%A8%B8%EC%8B%A0%EB%9F%AC%EB%8B%9D-12-%ED%8E%B8%ED%96%A5Bias%EC%99%80-%EB%B6%84%EC%82%B0Variance-Trade-off
- http://scott.fortmann-roe.com
- https://opentutorials.org/module/3653/22071

