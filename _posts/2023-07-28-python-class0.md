---
layout: post
title: "[Py: Class] 1.클래스와 객체의 본질"
subtitle: "[Py: Class] 1.클래스와 객체의 본질"
categories: devlang
tags: python
comments: true
---
#### Contents
- [class 객체 안에 변수가 만들어지는 시점](#class-객체-안에-변수가-만들어지는-시점)
- [init](#init)
- [객체에 변수와 메소드 붙였다 떼었다 해보기](#객체에-변수와-메소드-붙였다-떼었다-해보기)
- [클래스에 변수 추가하기](#클래스에-변수-추가하기)
- [파이썬에서는 class도 객체](#파이썬에서는-class도-객체)

<br>

---

## <span style="color:navy">class 객체 안에 변수가 만들어지는 시점<span>

- `클래스`
  - 객체를 만들기 위한 일종의 설계도
  - 클래스 내에 들어갈 변수와(데이터와) 메소드를(기능을) 결정하는 것
- `객체`
  - 클래스를 기반으로 만들어진 실제 사물

<br>

- 아래의 예제의 경우 변수의 선언이 명시적으로 존재하지 않는다.
    ```python
    class Simple:
        def seti(self, i):
            self.i = i
        def geti(self):
            print(self.i)
    ```

    하지만, 파이썬은 객체에 필요한 변수를 자동으로 생성해준다.
언제 자동으로 생성 해주냐면, <u>객체 내에서 변수를 대상으로 대입 연산이 수행되는 순간</u>이다.
즉, Class 객체를 생성하고 <u>메소드를 호출</u>하여 <u>변수에 인자를 기입하는 순간</u>이다.

<br>

- 아래의 예제에서는 `seti`메소드가 호출되어 인자를 전달받으면서 class객체내에 변수 i가 생성된다.
따라서, 변수 i가 생성되지 않은 상태에서 변수 i를 `geti`메소드를 이용해서 먼저 출력하려고하면 오류를 반환할 것이다. 왜냐하면, 출력할 값이 아직 생성되지 않았기 때문이다.

    ```python
    s1 = Simple() 
    s1.seti(200) # 변수 i 생성
    s1.geti()
    ```

<br>

---

## <span style="color:navy">init<span>

- 그러나 클래스를 정의할 때, 객체 생성 시 자동으로 호출되는 `__init__` 메소드를 정의하여, <br>
직접적인 메소드 호출 없이, 메소드 안에서 객체 내에 필요한 변수를 선언하여 위에서 언급된 오류를 회피할 수 있다.
왜냐하면, `__init__`<u>이 자동으로 호출되는 시점까지를 class객체 생성이 완료되는 시점으로 판단</u>하기 때문이다.

    ```python
    class Simple:
        def __init__(self):
            self.i = 0
        def seti(self, i):
            self.i = i
        def geti(self):
            print(self.i)
    
    s0 = Simple()
    s0.geti() # 0 
    
    s1 = Simple()
    s1.seti(25)
    s1.geti() # 25
    ```
    

<br>

---

## <span style="color:navy">객체에 변수와 메소드 붙였다 떼었다 해보기<span>

- 클래스 객체의 변수는 반드시 <u>클래스 내부의 메소드에 의해서만 선언되는 것이 아니며</u>,<br>
<u>클래스 외부에서도 클래스 내부의 변수 이름과 동일하게하여 선언</u>할 수 있다.
    ```python
    ss.i = 27
     ``` 
   
- 더불어, class객체의 메소드는 class객체를 통해서 <u>class 외부에서도 추가할 수 있다</u>.
    ```python
    ss.add_method = lambda : print('hi')
    ```
- class객체에 **선언된 변수**와 **정의된 메소드**는 <u>class객체를 통해서 class 외부에서 삭제할 수 있다</u>.
   ```python 
    del ss.i
    del ss.add_method
    ```

<br>

```python
class sosimple:
    def geti(self):
        print(self.i)

ss = sosimple()
ss.i = 27 # 이 순간 변수 ss에 담긴 객체에 i라는 변수가 생성된다.
ss.geti()

ss.add_method = lambda : print('hi')
ss.add_method()

# 클래스 객체 내의 메소드와 변수는 삭제 가능하다.
del ss.i
del ss.add_method
```

<br>

---

## <span style="color:navy">클래스에 변수 추가하기<span>

- 위에서 봤던 변수 추가 방식의 경우  class에 의해서 생성된 class 객체마다의 변수를 정의해주는 방법이였다면,
`class`의 경우 `class객체`와 동일하게 **객체**에 해당하기 때문에
다음과 같은 방법으로 <u>class 자체에 변수를 추가할 수 있다</u>.
    ```python
    [class이름].[추가변수]=값
    ```

<br>

- 아래의 예제에서 class객체 s1과 s2에는 변수 n이 존재하지 않는다.<br>
이때, 파이썬은 해당 객체를 생성하는데 이용된 class를 찾아가서 n을 수색한다.<br>
따라서, 독립된 class객체 s1, s2를 생성하였을 때, 두 객체의 n값이 동일한 것을 확인할 수 있다. <br>
(하지만, `__init__`의 변수 이름과 동일한 이름으로 생성하는 것은 권장하지 않는다.)

    ```python
    class simple:
        def __init__(self, i):
            self.i = i
        def geti(self):
            return self.i
    
    simple.n = 100
    # simple.i = 50 이라고 선언은 할 수 있지만, 권장 X
    
    s1 = simple(3)
    print(s1.n, s1.geti(), sep = ', ') # 100, 3
    s2 = simple(5)
    print(s2.n, s2.geti(), sep = ', ') # 100, 5
    ```

<br>

- 정리하자면 다음과 같다.

  - **class는 class이자 객체이다.**
  - **class객체가 아닌 class 자체에 속하는 변수를 만들 수 있다.**
  - **class객체에서 찾는 변수가 없다면, 해당 객체를 생성하는데 사용된 class로 찾아가서 그 변수를 찾는다.**

<br>

---

## <span style="color:navy">파이썬에서는 class도 객체<span>

- `type` 은 자료형을 확인할 때 호출하는 함수이다. 그리고, 함수지만  `type`은 class에 해당한다.
    ```python
    print(type) # <class 'type'>
    ```

<br>

- 그리고, `type`에  list객체를 전달하게되면 `list class`에 의해서 생성된 객체(즉, list class객체)를 의미함을 확인할 수 있는데,
`list class`를 `type`에 전달해보니 `<class ‘type’>`을 반환하였다.
이는 즉, `list class`는 사실 `type class`에 의해서 생성된 객체라는 것을 의미한다.
    
    ```python
    print(type([1, 2])) # <class 'list'>
    print(type(list)) # <class 'type'>
    ```

<br>

- 다음과 같이 직접 정의한 class도 `type class`에 의해서 생성된 것으로 확인된다.

    ```python
    class sample():
        pass
    
    print(type(simple)) # <class 'type'>
    ```

<br>

- 따라서, Python의 모든 class는 객체이며, `type` 클래스에 의해 생성된 객체이다.

<br>

---


## <span style="color:navy">References<span>
- 윤성우, 『윤성우의 열혈 파이썬 중급편』, ORANGE MEDIA(2021)
