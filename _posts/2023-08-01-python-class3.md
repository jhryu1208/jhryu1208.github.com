---
layout: post
title: "[Py: Class] 4.스페셜 메소드"
subtitle: "[Py: Class] 4.스페셜 메소드"
categories: devlang
tags: python
comments: true
---
#### Contents
- [스페셜 메소드](#스페셜-메소드)
- [클래스에 스페셜 메소드 정의하기](#클래스에-스페셜-메소드-정의하기)

<br>

---

## <span style="color:navy">스페셜 메소드<span>

- 사용자가 이름을 <u>직접 명시하여 호출하지 않아도 자동으로 호출되는 메소드</u>를 가리켜 `스페셜 메소드(special methods)`라고 한다.

<br>

- 이러한 메소드들은 언더스코어(`__`) 두 개로 시작하고 끝나는 이름을 가진다. <br>예를 들자면 다음과 같다.
  - `__len__`: 컨테이너 객체의 길이를 반환할 때 자동으로 호출된다.(*`len()`함수가 호출될 때 실행)
  - `__str__`: 객체를 문자열로 표현할 때 사용된다. (*`print(obj)`와 같이 호출될 때 실행)
  - `__eq__`: 두 객체가 같은지 확인하는 연산에서 자동으로 호출된다. (*`A == B` 연산 시 실행)
  - `__call__`: 객체를 함수처럼 호출할 때 사용된다.
  - `__iter__`: iter함수가 호출되었을 때 자동으로 호출된다.

  ```python
  t = (1, 2, 3)
  print(len(t)) #3
  print(t.__len__()) #3
  
  print(iter(t)) #  <tuple_iterator object at 0x000002B99ADC6548>
  print(t.__iter__()) #   <tuple_iterator object at 0x000002B99ADC65C8>
  
  print(str(t)) #  (1, 2, 3)
  print(t.__str__()) #  (1, 2, 3)
  ```

<br>

- 스페셜 메소드는 사용자 정의 클래스가 파이썬의 내장 타입과 같은 방식으로 작동하도록 만들어 다음과 같은 장점을 가진다.
  - <u>코드의 일관성과 확장성을 증가</u>시키며,<br> 표준 연산자, 함수, 및 구문과의 자연스러운 상호 작용을 가능케한다.
  - 사용자에게 익숙하고 직관적인 인터페이스를 제공하기 때문에<br><u>코드의 가독성과 재사용성을 향상</u>시킨다.
  - 짧게 말하면, 스페셜 메소드는 <u>사용자 경험의 개선</u>에 도움을 준다. 

<br>

- 스페셜 메소드가 좋다는 것은 알겠지만, 왜 좋은지 느낌이 잘 오지 않을 수 있다🤔🤔. 이를 위해, 다음의 예제를 살펴보자.
  다음은 스페셜 메소드 없이 두 벡터를 더하는 간단한 클래스이다. 두 백터를 더하기 위해 객체에서 `add`메소드를 호출하는 것을 확인할 수 있다.
  
  ```python
    class Vector:
        def __init__(self, x, y):
            self.x = x
            self.y = y
    
        def add(self, other):
            return Vector(self.x + other.x, self.y + other.y)
    
    v1 = Vector(2, 3)
    v2 = Vector(3, 4)
    v3 = v1.add(v2)
    ```
    
    <br>    

  "add"라는 이름은 직관적이고 간단하기 때문에 클래스 메소드가 굳이 필요한지 의문이 들 수 있다. 그러나, "add"와 동일한 역할을 하는 메소드가 사용자의 취향에 따라 "additon"(오타), "adddddd"(혼란스러운), 또는 "deohagi"(직관성X) 같은 이름을 가질 수 있다. 이런 경우, 다른 사용자가 코드를 읽을 때 가독성과 경험이 저해될 수 있다.

    <br>

    반면, **스페셜 메소드**를 사용하면, 일반적인 덧셈 연산자 +를 통해 두 벡터를 더할 수 있게 되며, 
    이는 코드를 읽고 이해하기 쉽게 만든다.
    이러한 방식은 파이썬의 내장 타입과 일관된 동작을 제공하므로 사용자에게 친숙하고 직관적이다.
    ```python
    class Vector:
        def __init__(self, x, y):
            self.x = x
            self.y = y
    
        def __add__(self, other):
            return Vector(self.x + other.x, self.y + other.y)
    
    v1 = Vector(2, 3)
    v2 = Vector(3, 4)
    v3 = v1 + v2 
    ```





<br>

---
## <span style="color:navy">클래스에 스페셜 메소드 정의하기<span>

- 아래의 예제 클래스를 기준으로 스페셜 메소드가 어떻게 정의되었는지 확인해보자.
  ```python
  class simple:
      def __init__(self, id):
          print('객체가 생성됨')
          self.id = id
      def __len__(self):
          print('len함수가 사용됨')
          return len(self.id)
      def __str__(self):
          print('str함수가 사용됨')
          return '사실은 월요일!'
  ```

<br>

- 객체가 생성된 경우 `__init__`메소드가 자동으로 호출되었기 때문에 
   `__init__`메소드 내부 함수들이 수행되었기에 print문에 의한 출력 결과가 나타나는 것을 확인할 수 있다.
    ```python
    s = simple('오늘은 금요일') #출력: 객체가 생성됨
    ```
<br>

- simple class의 객체인 s에 `len()`함수를 사용하였기 때문에 
   simple class 내부에 정의된 `__len__`메소드를 자동으로 호출하게 된다. 
   따라서, print문에 의한 출력 및 길이 반환 값을 확인할 수 있다.
   - 즉, `len(s)`는 `s.__len__()`과 동일한 의미이다.
   - 따라서, s라는 클래스 객체에 정의된 `__len__`메소드를 사용한다.
    ```python
    result0 = len(s) # len함수가 사용됨
    print(result0) # 7 (= '오늘은 금요일'의 글자 수)
    ```
<br>

- simple 클래스의 인스턴스 s는 `str` 함수를 사용할 때, simple 클래스 내의 `__str__` 메소드를 자동으로 호출하므로 "str함수가 사용됨"이라는 문자열이 출력된다. 반면, "아무 일도 없다"와 같은 str 클래스의 객체는 simple 클래스와는 독립적이며, str 클래스 내에 정의된 `__str__` 메소드를 사용한다. 따라서 이 경우에는 해당 문자열이 평범하게 출력되는 것을 확인할 수 있다.
    ```python
    result1 = str(s) 
    print(result1) 
    # str함수가 사용됨
    # 사실은 월요일!
  
    print('아무 일도 없었다')
    # 아무 일도 없었다  
    ```  
<br>

- "매운맛 새우깡"과 같은 문자열 객체는 내장 str 클래스의 인스턴스이므로, 개별 정의한 simple 클래스의 `__len__` 메소드와는 관련이 없다. 따라서 simple 클래스 내의 `__len__` 메소드에 추가된 print문은 "매운맛 새우깡"의 길이를 조회할 때 출력되지 않는다.
반면, simple 클래스로 생성된 객체 s의 경우, 클래스 내에 정의된 `__len__` 메소드가 len 함수를 사용할 때 호출된다. 따라서 이 경우에는 `__len__` 메소드 내에 추가한 print문이 출력되는 것을 확인할 수 있다.
    ```python
    result2 = len('매운맛 새우깡')
    print(result2) # 7
  
    result3 = len(s)
    print(result3)
    # len함수가 사용됨
    # 7
    ```


<br>

---

## <span style="color:navy">References<span>
- 윤성우, 『윤성우의 열혈 파이썬 중급편』, ORANGE MEDIA(2021)
