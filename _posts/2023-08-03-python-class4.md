---
layout: post
title: "[Py: Class] 5.연산자 오버로딩(Operator Overloading)"
subtitle: "[Py: Class] 5.연산자 오버로딩(Operator Overloading)"
categories: devlang
tags: python
comments: true
---
#### Contents
- [연산자 오버로딩(Operator Overloading)](#연산자-오버로딩operator-overloading)
- [in-place 형태의 연산자 오버로딩](#in-place-형태의-연산자-오버로딩)
  - [(1) in-place 연산자란?](#1-in-place-연산자란)
  - [(2) in-place 연산자 오버로딩](#2-in-place-연산자-오버로딩)
- [immutable & mutable 객체](#immutable--mutable-객체)

<br>

---

## <span style="color:navy">연산자 오버로딩(Operator Overloading)<span>

- `연산자 오버로딩(Operator Overloading)`은 프로그래밍에서 사용자가 직접 정의한 클래스 객체에 
`+`, `-`, `*`와 같은 <u>일반 연산자의 기능을 재정의하여 연산 가능한 상태로 만드는 기법</u>이다.

<br>

- 예를 들어, 복소수(Complex) 객체에서 필요한 `+`연산을 아래와 같이 `__add__`메소드를 통해서 재정의(오버로딩) 할 수 있다.

  ```python
  class Complex:
      def __init__(self, real, imag):
          self.real = real
          self.imag = imag
  
      # + 연산자 오버로딩
      def __add__(self, other):
          return Complex(self.real + other.real, self.imag + other.imag)
  
      def __str__(self):
          return f"{self.real} + {self.imag}i"
  
  a = Complex(1, 2)
  b = Complex(2, 3)
  c = a + b  # + 연산자 오버로딩을 사용한 덧셈
  print(c)  # 출력: 3 + 5i
  
  ```
  
<br>

---

## <span style="color:navy">in-place 형태의 연산자 오버로딩<span>

### <span style="color:navy">(1) in-place 연산자란?<span>

- 다음의 예제의 경우, 연산을 진행해도 피연산자 n1과 n2의 값의 변화는 없다.
  ```python
  n1 = 30
  n2 = 50
  n3 = n1+n2
  ```
  
  <br>

- 하지만, 우측의 예제에서는 연산을 진행할 경우 피연산자 n2의 값이 변경된다.
  ```python
  n1 = 30
  n2 = 50
  n2 += n1
  ```

<br>

- 이때, 우측과 같이 <u>피연산자의 원본을 수정하는 연산자</u>(`+=, -= 등`)를 `inplace-연산자`라고 한다. 그러므로, **in-place 형태의 연산자 오버로딩**은 <u>피연산자의 변화를 유도하는 오버로딩을 의미</u>함을 알 수 있다.

<br>

### <span style="color:navy">(2) in-place 연산자 오버로딩<span>

- `+=`이나 `-=`를 포함한 in-place연산자의 스페셜 메소드는 다음과 같다.
  - `+=` : `__iadd__(self, other)`
  - `-=` : `__isub__(self, other)`
  - `*=` : `__imul__(self, other)`
  - `/=` : `__itruediv__(self, other)`

<br>

- 위와 같은 in-place연산자를 오버로딩하기 위해서는 아래와 같이 **반드시** `self`**반환**을 수행해야 한다. (*이는 공식처럼 기억해야만 한다.)

  ```python
  def __iadd__(self, o):
      self.x += o.x
      self.y += o.y
      return self
  
  v1 += v2 # 해당 연산시 위의 class 메소드 수행됨
  ```

<br>

- 왜냐하면, `v1.__iadd__(v2)`에 의해 계산된 바른 값이 return되지 않았을 경우 다음과 같이 None이 반환되어 피연산자의 원본이 올바르게 수정되지 않기 때문이다.
  ```python
  def __iadd__(self, o):
      self.x += o.x
      self.y += o.y
      #return vector(self.x, self.y) 혹은 self 
  
  v1 += v2 # None
  ```

<br>

---

## <span style="color:navy">immutable & mutable 객체<span>

- in-place연산자의 동작은 대상 객체가 `immutable`인지 `mutable`인지에 따라 달라진다.

<br>

  - `immutable`객체
    - 정수, 문자열, 튜플이 해당된다.
    - 특정 주소에 한 번 생성된 내용을 <u>변경할 수 없는</u> 객체이다.
    - in-place 연산자를 해당 객체에 사용하면 <u>기존 객체가 저장된 주소와 다른 주소에 새로운 값을 저장</u>한다.
        ```python
        x1 = 10
        before_id1 = id(x1)
        
        x1 += 20
        after_id1 = id(x1)
    
        print(before_id1 == after_id1) # False
        ```
  
  <br>
  
  - `mutable`객체
    - 리스트, 딕셔너리, 집합이 해당된다.
    - 특정 주소에 생성된 내용을 <u>변경할 수 있는</u> 객체이다.
    - in-place 연산자를 해당 객체에 사용하면 원래의 객체가 직접 수정된다.
        ```python
        x2 = [10]
        before_id2 = id(x2)
        
        x2 += [20]
        after_id2 = id(x2)
  
        print(before_id2 == after_id2) # True
        ```
<br>


- 위의 객체들을 이용해서 **사용자 지정 클래스 객체**의 경우 프로그래머가 <u>프로그램의 내용에 따라서 immutable한 성격을 가지게 할 것인지 혹은 mutable한 성격을 가지게할 것인지 결정</u>할 수 있다. 예를 들면 다음과 같다.

  - **immutable 권장 케이스**: 판매 날짜, 상품 ID, 판매량, 단가 등이 포함된 판매 건별 매출 정보
    
  - **mutable 권장 케이스**: 은행 계좌에서는 잔액을 입금하거나 출금하는 등의 작업

<br>

---

## <span style="color:navy">References<span>
- 윤성우, 『윤성우의 열혈 파이썬 중급편』, ORANGE MEDIA(2021)
