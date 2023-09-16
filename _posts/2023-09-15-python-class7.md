---
layout: post
title: "[Py: Class] 8.Property"
subtitle: "[Py: Class] 8.Property"
categories: devlang
tags: python
comments: true
---
#### Contents
* [안전하게 접근하면서 간결하게...?](#span-stylecolornavy1-안전하게-접근하면서-간결하게)
* [Property](#property)
  * [(1) property() 함수란?](#1-property-함수란)
  * [(2) property() 함수의 메소드 등록법](#2-property-함수의-메소드-등록법)
* [내장 @property 데코레이터](#내장-property-데코레이터)

<br>

---

## <span style="color:navy">안전하게 접근하면서 간결하게...?</span>

- [이전 포스팅](https://jhryu1208.github.io/devlang/2023/09/01/python-class5/)에서 클래스 속성의 접근 방식은 <br>직접 접근 방법보다 <u>메서드를 이용한 간접 접근 방식이 안전</u>하다는 것에 관한 내용을 다루었다. 

<br>

- 그리고, 이러한 보호를 위한 메서드는 속성을 다루는 성격에 따라 다음과 같이 구분할 수 있다.
  - `getter`: 클래스 객체의 값을 **조회**하는 성격을 가진 메서드
  - `setter`: 클래스 객체의 값을 **수정**하는 성격을 가진 메서드

<br>

- 예를 들어, 다음 코드의 `getn`은 값을 반환(=조회)함으로 **getter**에 해당하며, <br>
`setn`은 클래스 속성을 수정하기 때문에 **setter**에 해당한다.
    ```python
    class natural:
        def __init__(self, n):
            if(n < 1):
                self.__n = 1
            else:
                self.__n = n
    
        def getn(self):
            return self.__n
    
        def setn(self, n):
            if(n < 1):
                self.__n = 1
            else:
                self.__n = n
    ```

<br>

- 메소드를 이용한 간접 접근 방식 자체는 안전하지만,<br> 명시적인 메소드 호출이 빈번해질 경우 코드의 복잡성을 유발할 수 있다.<br> 이를 위해 <u>안전성을 확보하면서 코드를 더 간결하게 표현</u>할 수 있는 `property`를 사용하는 방법이 존재한다.

<br>

---
## <span style="color:navy">Property</span>

### <span style="color:navy">(1) property() 함수란?</span>
- property는 파이썬의 내장 함수인 `property()`를 사용하여 생성할 수 있다.
    ``` 
    property(fget=None, fset=None)
    ```
  - `fget`
    - 객체의 속성을 **조회**하는 함수가 위치한다.
    - 프로퍼티가 **우변항**에 위치할 떄, fget에 위치한 함수가 호출된다.
    - 예를 들어, `k = [class 객체].[property 속성]`를 수행하면 변수 k에 조회된 class 객체의 지정된 속성 값을 저장한다. 
  - `fset`
    - 객체의 속성을 **수정**하는 함수가 위치한다.
    - 프로퍼티가 **좌변항**에 위치할 때, fset에 위치한 함수가 호출된다.
    - 예를 들어, `[class 객체].[property 속성] = 20`을 수행하면 정수 20이 class 객체의 지정된 속성 값에 저장된다.
  - 그 외, 삭제를 위한 `fdel`인자와 프로퍼티 설명을 위한 `doc`인자가 존재한다.
 
<br>

- 다음은 property를 사용하여 작성한 코드이다. 주석 처리된 명시적인 메소드 호출보다 상대적으로 간결해짐을 확인할 수 있다.
  
  ```python
    class natural:
        def __init__(self, n):
            self.setn(n)
        def getn(self):
            return self.__n
        def setn(self, n):
            if(n < 1):
                self.__n = 1
            else:
                self.__n = n
    
        p = property(getn, setn) # property 설정
    
    def main():
        n1 = natural(1)
        n2 = natural(2)
        n3 = natural(3)
        # n1.setn(n2.getn()+n3.getn())
        n1.p = n2.p + n3.p
        print(n1.p)
    
    main()
  ```

<br>

### <span style="color:navy">(2) property() 함수의 메소드 등록법</span>
- `p = property(fget, fset)` 코드 수행 시, <br> property 객체가 생성되는 동시에 getter와 setter 등록도 동시에 진행된다.

<br>

- 하지만, 이를 동시에 진행하지 않고 빈 property객체의 `getter`와 `setter` 메소드를 이용하여 함수를 개별로 등록할 수 있다.
  - property 객체의 `getter` 메소드: getter가 등록된 새로운 property 객체를 생성 및 반환 (기존 fset 유지)
  - property 객체의 `setter` 메소드: setter가 등록된 새로운 property 객체를 생성 및 반환 (기존 fget 유)

<br>

- 다음은 getter와 setter를 개별로 등록하는 예시 코드이다. 코드에서 getter와 setter가 같은 이름으로 작성된 것을 확인할 수 있는데, 이는 프로퍼티를 통해서 getter와 setter를 구분하여 사용할 수 있으므로 굳이 이름으로 구분할 필요가 없기 때문이다. 종종 이렇게 사용한다는데, 내 성격상 나는 이렇게 하지는 않을 것 같다.
  ```python
  class natural:  
      def __init__(self, n):
          self.setn(n)
      p=property() # property객체 생성
    
  
      def pm(self):
          return self.__n
      p = p.getter(getn) # 위의 pm메소드를 getter로 등록  
  
      def pm(self, n):
          if(n < 1):
              self.__n = 1
          else:
              self.__n = n
      p = p.setter(setn) # 위의 pm메소드를 setter로 등록
  ```

<br>

---

## <span style="color:navy">내장 @property 데코레이터</span>

- 위 방식보다 `@property`라는 **내장 데코레이터의(Decorator)**를 이용해서 더 쉽게 프로퍼티를 정의할 수 있다.

<br>

- 다음은 위의 예제를 @property를 이용해 재구성한 코드이다.
  ```python
  class natural:  
      def __init__(self, n):
          self.setn(n)
    
      @property # property를 생성하면서 하단의 메소드를 getter로 지정
      def pm(self):
          return self.__n
        
      @pm.setter # 위에서 생성한 property에 하단의 메소드를 setter로 지정
      def pm(self, n):
          if(n < 1):
              self.__n = 1
          else:
              self.__n = n
  ```
  
<br>

---

## <span style="color:navy">References</span>
- 윤성우, 『윤성우의 열혈 파이썬 중급편』, ORANGE MEDIA(2021)
- [Python Document, Built-in Functions: Property](https://docs.python.org/3/library/functions.html#property)
