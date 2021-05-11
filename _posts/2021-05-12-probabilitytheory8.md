---
layout: post
title:  "[확률론 Basic] Random Variables & Their Distribution"
subtitle: "[확률론 Basic] Random Variables & Their Distribution"
categories: math
tags: proba_theory
comments: true
mathjax: true
---
#### Contents
- Binomial Distribution (이항분포)
	- Probability Mass Function, PMF (확률질량함수)
	- Cumulative Distribution Function, CDF(누적분포함수)
	- X+Y~Bin(m+n, p) 증명
- Hypergeometric Distribution (초기하분포)
 
---
<br>

![확률론 기초-20](https://user-images.githubusercontent.com/53929665/117843503-d6f4e680-b2b9-11eb-821f-cb64b57a138b.jpg)

<br>

![확률론 기초-21](https://user-images.githubusercontent.com/53929665/117843511-d8261380-b2b9-11eb-87ef-749c560f6ffc.jpg)

<br>

여기서 계속 개인적으로 아리송했던 부분 정리
#### 확률질량함수 
- PMF는 <b>이산확률변수</b>에서 특정 값에 대한 확률을 나타내는 함수이다.
- PMF와 대응되는 함수로는 <b>연속확률변수</b>에 관한 CDF가 있다.
	-  CDF는 <b>연속확률변수</b>뿐만 아니라 <b>이산확률변수</b>에 관해서도 표현할 수 있다.
- <u>PMF는 위의 노트에서 언급한 2가지의 조건을 만족해야 유효</u>하다.
	- 확률변수가 반드시~~이항확률변수~~에 해당하지 않더라도 <u>PMF는 위의 2가지 조건을 충족하면 된다</u>. (위의 노트 참조)
	- 특정 확률변수에 관해서 수식을 세웠을 때, <b>이항확률변수</b><u>에 대한 PMF와 동일한 꼴 일 경우 그 특정 확률변수는 이항확률변수를 따른다</u>고 볼 수 있다. (아래의 노트 참조)
	
<br>

![확률론 기초-22](https://user-images.githubusercontent.com/53929665/117843513-d8beaa00-b2b9-11eb-9ec4-97539297a6b8.jpg)


<br>

---

### References
- Joe Blitzstein. "Statistics 110 : Probability"  Harvard University

