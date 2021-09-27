---
layout: post
title:  "[Pandas] Series 연산"
subtitle: "[Pandas] Series 연산"
categories: data
tags: pandas
comments: true
mathjax: true
---
#### Contents
- [with 숫자](#with-숫자)
- [with 시리즈](#with-시리즈)
- [메소드를 이용한 시리즈 연산](#메소드를-이용한-시리즈-연산)

본 포스팅은 Pandas의 Series 연산에 관하여 정리하였습니다.

<br>

---

## <span style="color:navy">with 숫자<span>

<u>시리즈 객체에 숫자를 연산</u>할 경우, <u>시리즈의 각 원소마다 연산이 진행</u>된다. <br>

더불어, 연산 결과 <u>시리즈 객체를 반환</u>한다. 

```python
sr1 = pd.Series({'A':100, 'B':200, 'C':300})

sr_add = sr1+100
sr_sub = sr1-100 
sr_div = sr1/100
sr_mul = sr1*0

print( '반환 객체 :', type(sr_add))
print(pd.DataFrame([sr_add, sr_sub, sr_div, sr_mul], 
			index = ['덧셈', '뺄셈', '나눗셈', '곱셈']))
```

```
반환 객체 : <class 'pandas.core.series.Series'>

				     A      B      C
덧셈      200.0  300.0  400.0
뺄셈        0.0  100.0  200.0
나눗셈      1.0    2.0    3.0
곱셈        0.0    0.0    0.0
```

<br>

## <span style="color:navy">with 시리즈<span>

<u>시리즈끼리 연산</u>을 수행할 경우, <u>동일 인덱스를 가진 원소끼리 계산을 수행</u>한다. <br>

숫자와의 연산과 마찬가지로 <u>시리즈 객체를 반환</u>한다.

```python
sr1 = pd.Series({'A':100, 'B':200, 'C':300})
sr2 = pd.Series({'A':10, 'B':20, 'C':30})

sr_add = sr1+sr2
sr_sub = sr1-sr2 
sr_div = sr1/sr2
sr_mul = sr1*sr2

print( '반환 객체 :', type(sr_add))
print(pd.DataFrame([sr_add, sr_sub, sr_div, sr_mul], 
		  index = ['덧셈', '뺄셈', '나눗셈', '곱셈']))
```

```
반환 객체 : <class 'pandas.core.series.Series'>
          
						A       B       C
덧셈     110.0   220.0   330.0
뺄셈      90.0   180.0   270.0
나눗셈    10.0    10.0    10.0
곱셈    1000.0  4000.0  9000.0
```

<br>

<u>인덱스의 정렬이 다르더라도 동일 인덱스를 가진 원소끼리 연산</u>되는 것을 확인할 수 있다.

```python
sr1 = pd.Series({'A':100, 'B':200, 'C':300})
sr2 = pd.Series({'B':10, 'A':20, 'C':30})

sr1+sr2
```

```
A    120
B    210
C    330
dtype: int64
```

<br>

더불어, 연산되어지는 <u>두 시리즈 중 어느 한쪽에만 존재하는 인덱스가 있을 경우</u>에는 연산을 할 수 없기 때문에 `NaN`<u>을 반환</u>한다. 더불어, 연산의 결과 <u>시리즈 인덱스의 합집합을 인덱스로 가지는 시리즈를 반환</u>하는 것을 확인할 수 있다.

```python
sr1 = pd.Series({'A':100, 'B':200, 'C':300})
sr2 = pd.Series({'B':200, 'C':300, 'D':400, 'E':500})

sr1+sr2
```

```
A      NaN
B    400.0
C    600.0
D      NaN
E      NaN
dtype: float64
```

<br>

## <span style="color:navy">메소드를 이용한 시리즈 연산<span>

시리즈끼리의 연산을 위한 메소드도 존재한다. 기본적으로는 `add`, `sub`, `mul`, `div` 등이 있다. 더불어 해당 연산 메소드는 `fill_value`<u> 옵션을 가지고 있으며, 어느 한쪽만 존재하는 인덱스에 의해서 </u>`NaN`<u>을 반환하는 케이스를 방지</u>할 수 있다. 해당 메소드들을 이용해서 데이터프레임과의 계산도 진행할 수 있다. 이는 다음 포스팅에서 살펴볼 예정이다.  

```python
# 메소드를 이용한 시리즈끼리의 계산
[시리즈 객체1].[add|sub|mul|div]([시리즈 객체2], fill_value=...)
```

<br>

다음과 같이 `fill_value`<u> 옵션을 추가함으로써 인덱스 짝이 없는 원소 값으로  </u>`NaN`<u>을 반환하지 않으며,  옵션으로 지정한 값으로 연산을 진행</u>하는 것을 확인할 수 있다.

```python
sr1 = pd.Series({'A':100, 'B':200, 'C':300})
sr2 = pd.Series({'B':20, 'C':30, 'D':40, 'E':50})

print('[ fill_value옵션 X ]\n', sr1.add(sr2), '\n')
print('[ fill_value옵션 O ]\n', sr1.add(sr2, fill_value = 1000))
```

```
[ fill_value옵션 X ]
A      NaN
B    220.0
C    330.0
D      NaN
E      NaN
dtype: float64 

[ fill_value옵션 O ]
A    1100.0          # <- NaN이 아닌 1000을 더해서 1100이 반환됨
B     220.0
C     330.0
D    1040.0          # <- NaN이 아닌 1000을 더해서 1040이 반환됨
E    1050.0          # <- NaN이 아닌 1000을 더해서 1050이 반환됨
dtype: float64
```
