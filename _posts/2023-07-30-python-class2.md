---
layout: post
title: "[Py: Class] 3.isinstance 함수와 object 클래스"
subtitle: "[Py: Class] 3.isinstance 함수와 object 클래스"
categories: devlang
tags: python
comments: true
---
#### Contents
- [isinstance 함수](#isinstance-함수)
- [object 클래스](#object-클래스)

<br>

---
## <span style="color:navy">isinstance 함수<span>

- `isinstance`함수는 <u>객체의 클래스 유형을 확인하는 함수</u>이다. <br>즉, “이 객체는 저 class에 의해서 생성된 객체인가?”라는 질문에 대한 답을 해주는 함수이다.

  ```python
  isinstance(object, classinfo)
  ```

<br>

- 다음의 예시와 같이 `object`에 위치한 객체의 class가 classinfo에 위치한 class와 동일하다면  `True`를 반환한다.

  ```python
  class simple:
      pass
  
  s = simple()
  
  print(isinstance(s, simple)) # s라는 객체의 클래스 유형은 simple인가?, True
  print(isinstance([1,2], list)) # [1,2]의 객체는 클래스 유형은 list인가?, True
  ```

<br>

- 상속 관계가 있는 다음의 경우에도 `True`를 반환한다!<br>
  아래의 코드에서 class C는 class B에 의해서 class A를 간접 상속한다. (간접 상속도 상속이다!)<br>
  따라서, `isinstance`는 <u>상속/간접상속 관계가 있는 경우</u>에 `True`를 반환함을 알 수 있다.

  ```python
  class A:
      pass

  class B(A):
      pass

  class C(B):
      pass

  s = C()
  print(isinstance(s, C)) # True
  print(isinstance(s, B)) # True
  print(isinstance(s, A)) # True
  ```

<br>

---

## <span style="color:navy">object 클래스<span>

- `object`클래스는 파이썬에서 <u>모든 클래스의 시작점</u>이다. 왜냐하면 <u>파이썬의 모든 클래스는</u> `object`클래스를 직접/간접 상속</u>하기 때문이다. 따라서, `object` 클래스는 최상위 수준에 있으며, <u>모든 기본적인 메서드와 속성을 제공</u>한다.
- 다음은 list객체 내에 `object`객체의 메서드 모두 포함되어있는지 확인하는 코드이다. `object` 객체는 총 23개의 메서드를 가지고 있으며, 해당 메서드들이 모두 list객체에 포함되어있는 것을 확인할 수 있다.
  
  ```python
  from collections import Counter

  s = object()
  object_method_lists = dir(object)
  list_method_lists = dir(list)

  answer = []

  for object_method_list in object_method_lists:
      answer.append(True) if object_method_list in list_method_lists else answer.append(False)

  print(f'object객체의 메서드 수: {len(object_method_lists)}') # 23
  print(f'list객의 메드 수: {len(list_method_lists)}') # 47
  print(Counter(answer)) # Counter({True: 23})
  ```

<br>

- 더불어, `object`클래스의 상속을 명시하지 않아도 파이썬이 `object`를 직접 혹은 간접 상속하도록 클래스를 구성하기 때문에 모든 클래스의 객체를 생성하는데 일조하는 `type`까지 `object` class를 상속하는 것을 확인할 수 있다.

  ```python
  class simple:
      pass
  
  print(isinstance(simple, object)) # True
  print(isinstance([1, 2], object)) # True
  print(isinstance(type, object))   # True 
  ```

<br>

- `type`과 `object`에 관해서 정리하자면,
  - 모든 객체는 클래스의 인스턴스이다. 
  - `object`를 포함한 모든 클래스는 `type`의 인스턴스이다. 
  - `type`을 포함한 모든 클래스는 `object`를 상속한다.
  - 즉, `type`은 `object`의 서브클래스이며, `object`는 `type`의 인스턴스로 특별한 순환 관계를 갖고 있다.

  ```python
  print(isinstance(object, type))  # True
  print(isinstance(type, type))    # True
  print(issubclass(type, object))  # True
  ```

<br>

---

## <span style="color:navy">References<span>
- 윤성우, 『윤성우의 열혈 파이썬 중급편』, ORANGE MEDIA(2021)
