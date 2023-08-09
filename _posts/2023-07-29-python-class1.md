---
layout: post
title: "[Py: Class] 1.클래스와 객체의 본질"
subtitle: "[Py: Class] 1.클래스와 객체의 본질"
categories: devlang
tags: python
comments: true
---
#### Contents
- [부모 클래스와 자식 클래스](#부모-클래스와-자식-클래스)
- [메소드 오버라이딩과 super](#메소드-오버라이딩과-super)
- [init 메소드의 오버라이딩](#init-메소드의-오버라이딩)

<br>

---
## <span style="color:navy">부모 클래스와 자식 클래스<span>

- 파이썬의 class는 `상속(inheritance)`이라는 것을 할 수 있다. <br>A class가 Z class를 상속했다고 가정해보자. <br>이때,  두 클래스를 다음과 같이 칭한다.

  - A class : `부모 클래스`, `슈퍼 클래스`, `상위 클래스`
  - Z class : `자식 클래스`, `서브 클래스`, `하위 클래스`

<br>

- 그리고,  `상속`에 의해서 다음과 같은 일을 할 수 있다.

  1. 부모 class가 갖는 모든 메소드가 자식 class에도 담긴다.
  2. 자식 class는 부모 class의 메소드와 별도로 추가 메소드를 정의할 수 있다.

<br>

- 아래의 예제에서 자식 클래스가 부모 클래스의 메소드를 사용하고,
<br>별도로 정의된 메소드까지 사용하는 것을 확인할 수 있다.

    ```python
    class parent:
        def parent_ability(self):
            print("Do parent ability")
    
    class child(parent): # <- 상속하는 방법 확인
        def child_ability(self):
            print("Do child ability")
    
    def main():
        s = child()
        s.parent_ability()
        s.child_ability()
    
    main()
    # Do parent ability
    # Do child ability
    ```

    <br>

    더불어, <u>한 번에 둘 이상의 클래스를 상속하는 것도 가능</u>하다. 하지만, 둘 이상의 클래스를 동시에 사용하면 구조가 복잡해지고 주의해야 할 사항들이 늘어나기 때문에 <u>일반적으로는 둘 이상의 클래스를 동시에 상속하지 않는다</u>.

    ```python
    class father:
        def father_ability(self):
            print('Do father ability!')
    
    class mother:
        def mother_ability(self):
            print('Do mother ability!')
    
    class child(father, mother):
        def child_ability(self):
            print('Do child ability!')
    
    def main():
        s = child()
        s.father_ability()
        s.mother_ability()
        s.child_ability()
    
    main()
    # Do father ability!
    # Do mother ability!
    # Do child ability!
    ```

<br>

- 정리하면 다음과 같다.
  1. **자식 클래스는 부모 클래스의 메소드를 사용할 수 있다.**
  2. **더불어, 자식 클래스에서 별도로 정의된 메소드도 사용할 수 있다.**
  3. **자식 클래스는 둘 이상의 부모 클래스를 상속받을 수 있다.**

<br>

---

## <span style="color:navy">메소드 오버라이딩과 super<span>

- 부모 클래스가 갖는 메소드와 <u>동일한 이름의 메소드</u>를 자식 클래스에가 정의하는 경우도 있다. <br>이를 가리켜 `메소드 오버라이딩`이라고 한다.

- `메소드 오버라이딩`이 수행될 경우, <u>부모 클래스의 메소드는 보이지 않는 상태</u>(= 호출이 불가능한 상태, del된 것이 아님)가 된다.

- 아래의 예제의 경우, sun class의 strong_ability메소드가 father class의 strong_ability메소드를 가린 상태이다. <br>즉, 삭제된 상태가 아니다. 두 메소드 모두 온전히 존재한다.

    ```python
    class father:
        def strong_ability(self):
            print('strong father')
    
    class sun(father):
        def strong_ability(self):
            print('more stronger than fater')
    
    def main():
        s = sun()
        s.strong_ability()
    
    main() # more stronger than fater
    ```

    <br>
    
    사라진 것이 가려졌을 뿐이기에 가려진 메소드의 이름 앞에 `super()`을 붙여서 사용할 수 있다.

    ```python
    class father:
        def strong_ability(self):
            print('strong father')
    
    class sun(father):
        def strong_ability(self):
            print('more stronger than fater')
        def hide_strong_ability(self):
            super().strong_ability()
    
    def main():
        s = sun()
        s.strong_ability()
        s.hide_strong_ability()
    
    main()
    # more stronger than fater
    # strong father
    ```

<br>

---

## <span style="color:navy">init 메소드의 오버라이딩<span>

- 다음과 같은 상황이 존재할 수 있다.
  1. `__init__메소드 오버라이딩`을 해야만 하는 상황
  2. 동시에 가려진 메소드(즉, `오버라이딩된 메소드`)를 호출해야하는 상황

<br>

- 아래의 예제에서는  `__init__` 메소드가 `오버라이딩`되었으며 차와 트럭의 무게의 정보를 정의하기 위해서 가려진 메소드를 호출해야만 하는 상황에 해당한다.  이때, truck class의 `__init__`메소드에서 `super()`를통해 car class의 `__init__`메소드를 호출한 것을 확인할 수 있다. 즉, truck의 class에 상속된  car의 변수들을 자동으로 생성시키고 선언할 수 있게된다.

    ```python
    class car:
        def __init__(self, id, f):
            self.id = id
            self.fuel = f
        def drive(self):
            self.fuel -= 10
        def add_fuel(self, f):
            self.fuel += f
        def show_info(self):
            print(f"id : {self.id}")
            print(f"fuel : {self.fuel}")
    
    class truck(car):
        def __init__(self, id, f, c):
            super().__init__(id, f)
            self.cargo = c
        def add_cargo(self, c):
            self.cargo += c
        def show_info(self):
            super().show_info()
            print(f"cargo : {self.cargo}")
    
    def main():
        s = truck('42럭5959', 0, 0)
    
        s.add_fuel(100)
        s.add_cargo(50)
    
        s.drive()
        s.show_info()
    
    main()
    # id : 42럭5959
    # fuel : 90
    # cargo : 50
    ```
    
    <br>

    따라서, 위와 같은 상황에서는 자식 클래스의 `__init__`은 부모의 변수를 초기화하기 위한 값도 함께 전달받아야 한다.

<br>

---


## <span style="color:navy">References<span>
- 윤성우, 『윤성우의 열혈 파이썬 중급편』, ORANGE MEDIA(2021)
