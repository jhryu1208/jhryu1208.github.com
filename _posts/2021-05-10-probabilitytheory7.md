---
layout: post
title:  "[확률론 Basic] Gambler's Ruin & Random Variables"
subtitle: "[확률론 Basic] Gambler's Ruin & Random Variables"
categories: math
tags: proba_theory
comments: true
mathjax: true
---
#### Contents
- Gambler's Ruin Problem
- Random Variable

---
<br>


![확률론 기초-17](https://user-images.githubusercontent.com/53929665/118400237-c074d380-b69b-11eb-8fa0-a33f4abd5bc1.jpg)

<br>

![확률론 기초-17](https://user-images.githubusercontent.com/53929665/117578671-5a7fcd80-b12a-11eb-8c81-74432ca101cf.jpg)

<br>

![확률론 기초-18](https://user-images.githubusercontent.com/53929665/117578672-5b186400-b12a-11eb-88a3-a763094ebdc2.jpg)


- "$n$개의 독립적인 $Bern(p)$동안의 성공 횟수에 관한 분포"
	- 해당 노트가 애매모호한 것 같기에 아래에 추가 설명을 참조하였다.

<br>

---

<br>

#### 베르누이 확률분포 와 확률분포 추가 메모
- 만약 어떤 확률변수 $X$가 베르누이 분포에 의해 발생되었을 때
	- "확률 변수 $X$가 베르누이 분포를 <br>따른다</br>"
	- 수식 : $X$~$Bern(k;p)$ 
	- 베르누이 분포의 확률 질량함수의 수식은 다음과 같다.
$$Bern(k;p)=  \begin{cases} p  
					& \text{if }k = 1 
					\\ 1-p  & \text{if }k = 0 \end{cases}$$
	- 위 식을 하나의 수식으로 표현하면 다음과 같다.
$$Bern(k;p)=  p^{k}(1-p)^{(1-k)}$$

<br>

- `베르누이분포`와 `이항분포` 모두 `베르누이 확률변수`에서 나온 표본값
	- 베르누이분포 : 표본 데이터가  하나 뿐 
	- 이항분포 : 표본 데이터가 여럿 
	- 이항분포 확률변수 $X$와 베르누이 확률분포 $Y$($y_1, y_2, ..., y_n$)의 관계
		- $y_n$에 해당하는 값은 모두 0(실패) 아니면 1(성공)이라는 값을 가지기 때문에 $n$번 중 성공한 횟수는 $n$개의 표본값의 합이된다.
	
$$k = \sum_{i=1}^{n}{y_i}$$


<br>

---

### References
- Joe Blitzstein. "Statistics 110 : Probability"  Harvard University
- [데이터 사이언스 스쿨](https://datascienceschool.net/02%20mathematics/08.02%20%EB%B2%A0%EB%A5%B4%EB%88%84%EC%9D%B4%EB%B6%84%ED%8F%AC%EC%99%80%20%EC%9D%B4%ED%95%AD%EB%B6%84%ED%8F%AC.html)

