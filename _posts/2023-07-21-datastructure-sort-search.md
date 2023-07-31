---
layout: post
title: "[자료구조] 2.정렬 & 탐색(Sorting & Searching)"
subtitle: "[자료구조] 2.정렬 & 탐색(Sorting & Searching)"
categories: data
tags: datastructure
comments: true
---
#### Contents

- [정렬(Sort) 이란?](#정렬sort-이란)
  - [(1) 내장함수: sorted()](#1-내장함수-sorted)
  - [(2) 메서드: .sort()](#2-메서드-sort)
- [수치(Number)가 아닌 데이터형의 정렬?](#수치number가-아닌-데이터형의-정렬)
- [탐색(Search) 이란?](#탐색search-이란)
  - [(1) 선형 탐색 (Linear Search)](#1-선형-탐색-linear-search)
  - [(2) 이진 탐색 (Binary Search)](#2-이진-탐색-binary-search)


<br>
<br>
<br>

---

## <span style="color:navy">정렬(Sort) 이란?<span>

- 복수의 원소로 주어진 데이터를 정해진 기준에 따라 새로 늘어놓는 작업이다.
- 정렬 알고리즘에는 여러 종류가 있지만,
    
    파이썬의 `리스트(list)`를 이용한다면, 직접 정렬 알고리즘을 구현할 필요는 없다.
    
    왜냐하면, 리스트(list)에 내장된 정렬 기능이 있기 때문이다.

<br>

### <span style="color:navy">(1) 내장함수: `sorted()`<span>

- 오름차순 정렬을 수행하는 경우
    
    ```python
    L = [3, 8, 2, 7, 6, 10, 9]
    
    L_asc = sorted(L) 
    
    L_asc # [2, 3, 6, 7, 8, 9 ,10] 오름차순 정렬
    L # [3, 8, 2, 7, 6, 10, 9]
    ```

<br>

- 내림차순 정렬을 수행하는 경우
    
    ```python
    L = [3, 8, 2, 7, 6, 10, 9]
    
    L_desc = sorted(L, reverse=True)
    
    L_asc # [10, 9, 8, 7, 6, 3, 2] 내림차순 정렬
    L # [3, 8, 2, 7, 6, 10, 9]
    ```
    
<br>

### <span style="color:navy">(2) 메서드: `.sort()`<span>

- 오름차순 정렬을 수행하는 경우
    
    ```python
    L = [3, 8, 2, 7, 6, 10, 9]
    
    L.sort()
    
    L # [2, 3, 6, 7, 8, 9 ,10] 오름차순 정렬
    ```
    
<br>

- 내림차순 정렬을 수행하는 경우
    
    ```python
    L = [3, 8, 2, 7, 6, 10, 9]
    
    L.sort(reverse=True)
    
    L # [10, 9, 8, 7, 6, 3, 2] 내림차순 정렬
    ```

<br>

---

## <span style="color:navy">수치(Number)가 아닌 데이터형의 정렬?<span>

- Python에서 문자열의 경우 알파벳 혹은 대소문자 순서대로 정렬이 기본적으로 수행된다.
- 하지만, `**문자열의 길이 순서**`로 정렬하려면 어떻게 해야할까?
    
    정답은 위에서 확인했던 `.sort()` 메소드 혹은 `sorted()`함수에 속한 `**key**`인자를 이용하는 것이다.
    
    <aside>
    💡 <b>key 매개변수</b>
    
    - Default는 none이다.
    - 리스트 요소에 대해 **호출할 함수**를 지정하는 매개변수
    - 이때, 해당 함수는 **정렬 목적으로 사용할 기준이 되는 키를 반환하는 함수**여야 한다.
    - 보통 일반함수 혹은 **lambda함수를 이용**해서 함수를 표현한다.
    
    </aside>
    
<br>

- 다음은 리스트L을 글자 수 기준으로 오름차순 정렬을 수행하는 과정이다.
    
    이때, L1_sort와 L2_sort를 통해 글자 수 기준으로 오름차순 정렬이 수행된 것을 확인할 수 있다.
    
    ```python
    L1 = ['abcd', 'xyz', 'spam']
    L1_sort = sorted(L1, key=lambda x: len(x))
    
    L2 = ['spam', 'xyz', 'abcd']
    L2_sort = sorted(L2, key=lambda x: len(x))
    
    L1_sort # ['xyz', 'abcd', 'spam']
    L2_sort # ['xyz', 'spam', 'abcd']
    
    ```

<br>
    
- 다음은 딕셔너리를 원소로 담은 리스트L의 원소 딕셔너리의 score키를 기준으로 리스트를 내림차순 정렬을 수행하는 과정이다.
    
    ```python
    L = [
    	{'name': 'John', 'score': 83}
    	, {'name': 'Paul', 'score': 92}
    ]
    
    L.sort(key=lambda x: x['score'], reverse=True)
    
    L # [{'name': 'Paul', 'score': 92}, {'name': 'John', 'score': 83}]
    ```

<br>

---

## <span style="color:navy">탐색(Search) 이란?<span>

- 복수의 원소로 이루어진 데이터에서 **특정 원소를 찾아내는 작업**이다.
- 탐색에 대해서는 다음의 두 가지를 소개한다.
    - `**선형 탐색 (Linear Search)**`
    - `**이진 탐색 (Binary Search)**`

<br>

### <span style="color:navy">(1) 선형 탐색 (Linear Search)<span>

- 선형 탐색은 `**순차 탐색(Sequential Search)**`라고도 불리운다.

- 순차적으로 모든 요소들을 탐색하여 원하는 값을 찾아내는 과정이다.

- 따라서, 작업 처리 시간이 배열의 길이에 비례한다.
    - 길이에 비례하는 알고리즘이므로, `**Big-O Notation**`으로 표기하면 $`O(n)`$이다.
    - 최악의 경우, 배열에 있는 모든 원소를 다 검사할 가능성도 있다.

<br>

### <span style="color:navy">(2) 이진 탐색 (Binary Search)<span>

- 해당 탐색 알고리즘은 탐색하려는 배열이 이미 정렬되어 있는 경우에만 사용할 수 있다.
    
    따라서, 알고리즘 적용 이전, 배열이 정렬되어있는지 더블체크가 필요하다.
    
- 배열의 가운데 원소와 찾으려는 값을 비교하면, 찾고자 하는 값이 왼쪽 혹은 오른쪽에 있는지 알 수 있다. 더불어, 적어도 반대쪽에 없는 것을 확신할 수 있기에, 배열의 반을 탐색하지 않고 버릴 수 있다. 이때, 이진 탐색은 해당 과정이 찾고자하는 원소를 발견할 때 까지 반복하는 과정이다.
    - 한 번의 비교를 통해 배열의 반을 버릴 수 있는 이와 같은 방식을 `**Divide&Conquer**`라 칭한다.
    - 그리고, 이런 성질을 가지는 알고리즘의 복잡도는 $\log n$에 비례하기에
        
        `**Big-O Notation**`으로 표기하면 $`**O(\log n)**`$이다.
        
- 다음은 [1, 3, 7, 8, 12, 15]라는 정렬된 리스트에서 원소 8을 탐색을 적용하는 예시이다.

<br>

1. *먼저 Min Index와 Max Index를 찾는다.
    - Min Index = 0, Max Index = 5
2. 발견된 Min Index와 Max Index 사이의 Mid Index를 집계한다.
    - (Max Index + Low Index)/2 = Mid Index
    - 이때, 소숫점 이하를 버렸을 때, Mid index=2이다.
3. Mid index에 위치한 원소와 찾고자하는 원소 8을 비교한다.
    - index=2에 위치한 원소는 7이며, 8보다 작다.
    - 따라서, 7의 좌측에 위치한 모든 원소는 정렬되어있기에 8보다 작을 것이고 탐색의 과정에서 배제된다.
4. *배제된 상태의 리스트에서 Min Index와 Max Index를 재탐색한다.
    - Min Index = 3, Max Index = 5

1. 2~3의 과정을 재수행한다.
    - 재수행 결과, Mid Index에 위치한 원소 12와 찾고자하는 원소 8을 비교했을 때,  원소 12가 원소 8보다 크다. 따라서, 12의 우측에 위치한 모든 원소는 정렬되어있기에 8보다 클 것이고 탐색의 과정에서 배제된다.

1. *배제된 상태의 리스트에서 Min Index와 Max Index를 재탐색한다.
    - Min Index = Max Index = 3
    - 재수행 결과, Mid Index에 위치한 원소는 찾고자하는 원소 8이 있음을 확인할 수 있다.

<br>

---


## <span style="color:navy">References<span>
- [Programmers, 어서와! 자료구조와 알고리즘은 처음이지?](https://school.programmers.co.kr/learn/courses/57/57-%EC%96%B4%EC%84%9C%EC%99%80-%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0%EC%99%80-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98%EC%9D%80-%EC%B2%98%EC%9D%8C%EC%9D%B4%EC%A7%80)
