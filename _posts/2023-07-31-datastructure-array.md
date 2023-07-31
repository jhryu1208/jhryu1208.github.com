---
layout: post
title: "[자료구조] 선형배열"
subtitle: "[자료구조] 선형배열"
categories: data
tags: datastructure
comments: true
mathjax: true
---
#### Contents
- [선형 배열](#선형-배열)
- [리스트와 관계 없이 빠르게 실행 결과를 보게되는 연산들](#리스트와-관계-없이-빠르게-실행-결과를-보게되는-연산들)
  - [(1) 원소 덧붙이기: .append()](#1-원소-덧붙이기-append)
  - [(2) 원소 하나를 꺼내기: .pop()](#2-원소-하나를-꺼내기-pop)
- [리스트 길이에 비례해서 실행 시간이 걸리는 연산들](#리스트-길이에-비례해서-실행-시간이-걸리는-연산들)
  - [(1) 원소 삽입하기 .insert()](#1-원소-삽입하기-insert)
  - [(2) 원소 삭제하기 .del()](#2-원소-삭제하기-del)
- [리스트 (배열) 연산](#리스트-배열-연산)
  - [(1) 원소 탐색하기 .index()](#1-원소-탐색하기-index)

<br>

---

## <span style="color:navy">선형 배열<span>

- 선형 배열은 데이터들이 선(Line)처럼 일렬로 늘어선 형태를 의미한다.
    - 보통 프로그래밍에서는 `Array`라고 하면 같은 종류의 데이터가 줄지어 늘어서 있는 것을 뜻한다.
        
        Python에서는 서로 다른 종류의 데이터 또한 줄세울 수 있는 `리스트(List)`라는 데이터형이 있다.

<br>     

---

## <span style="color:navy">리스트와 관계 없이 빠르게 실행 결과를 보게되는 연산들<span>

순식간에 할 수 있는 일

즉, 리스트의 길이와 무관하게 상수시간내에서 해낼 수 있는 일을 

`Big-O Notation`으로 $`O(1)`$이라고 표시한다.

<br>

### <span style="color:navy">(1) 원소 덧붙이기: `.append()`, 값을 반환하지 않는 메소드<span>

```python
x = ['Bob', 'Cat', 'Spam', 'Programmers']

x.append('New')

print(x) # ['Bob', 'Cat', 'Spam', 'Programmers', 'New']
```

<br>

### <span style="color:navy">(2) 원소 하나를 꺼내기: `.pop()`<span>

```python
x = ['Bob', 'Cat', 'Spam', 'Programmers']

x.pop() # 'Programmers'가 출력으로 아웃되고, 기존 리스트에서 사라진다.

print(x) # ['Bob', 'Cat', 'Spam']
```

<br>

---

## <span style="color:navy">리스트 길이에 비례해서 실행 시간이 걸리는 연산들<span>

아래의 연산들은 리스트의 길이가 길면 길수록 처리가 오래 걸리게 된다.

구체적으로 말하면 리스트의 길이에 실행 시간이 비례(선형 시간)한다는 의미이다.

즉, 리스트의 길이가 100배가 되면, 위 연산들을 실행하는 데 걸리는 시간이 100배 커진다.

이때, 리스트 길이와 비례하여 선형 시간내에 해낼 수 있는 일을 `Big-O Notation`으로 $$`O(n)`$$이라고 표시한다.

<br>

### <span style="color:navy">(1) 원소 삽입하기 `.insert()`<span>

```python
x = [20, 37, 58, 72, 91]

x.insert(3,65) # index=3의 위치에 원소 65를 삽입해라
"""
[ insert가 수행되는 과정 ]

1. index=3의 위치를 먼저 색인한다. 
	 [20, 37, 58,↓72, 91]

2. 가장 우측에 위치한 원소를 한 칸 우측으로 옮긴다.
	 *이때, 리스트의 길이가 한 칸 늘어난다. 
   [20, 37, 58, ↓72, Vaccant, 91]

3. index=3에 위치한 원소를 한 칸 우측으로 옮긴다.
   [20, 37, 58, Vaccant, ↓72, 91]

4. 기존 index=3의 위치에 65를 삽입한다.
   [20, 37, 58, ↓65, 72, 91]

"""

print(x) # [20, 37, 58, 65, 72, 91]
```

<br>

### <span style="color:navy">(2) 원소 삭제하기 `.del()`<span>

```python
x = [20, 37, 58, 72, 91]

del(x[2])# 리스트x의 index=2의 위치한 원소를 삭제해라
"""
[ del이 수행되는 과정 ]

1. index=2의 위치를 먼저 색인한다.
	 [20, 37, ↓58, 72, 91]

2. index=2앞의 index=3의 원소를 index=2위치로 당긴다.
   [20, 37, ↓72, Vaccant, 91]

3. index=3앞의 index=4의 원소를 index=3위치로 당긴다.
   [20, 37, ↓72, 91, Vaccant]

4. 리스트 마지막에 위치한 index자리를 삭제한다.
   *이때, 리스트의 길이가 한 칸 줄어든다.
   [20, 37, ↓72, 91]

"""

print(x) # [20, 37, 72, 91]
```

<br>

---

## <span style="color:navy">리스트 (배열) 연산 <span>

### <span style="color:navy">(1) 원소 탐색하기 `.index()`<span>

- 탐색하는 리스트에 색인하고자 하는 원소가 존재하지 않을 경우 `ValueError`를 반환함

```python
L = ['Bob', 'Cat', 'Spam', 'Programmers']

L.index('Spam') # 2
```

<br>


---


## <span style="color:navy">References<span>
- [Programmers, 어서와! 자료구조와 알고리즘은 처음이지?](https://school.programmers.co.kr/learn/courses/57/57-%EC%96%B4%EC%84%9C%EC%99%80-%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0%EC%99%80-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98%EC%9D%80-%EC%B2%98%EC%9D%8C%EC%9D%B4%EC%A7%80)
