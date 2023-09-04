---
layout: post
title: "[Py: Class] 6.정보은닉(Information Hiding)과 __dict__"
subtitle: "[Py: Class] 6.정보은닉(Information Hiding)과 __dict__"
categories: devlang
tags: python
comments: true
---
#### Contents
* [속성 감춤의 필요성](#속성-감춤의-필요성)
* [직접 접근 & 간접 접근](#직접-접근--간접-접근)
  * [(1) 직접 접근](#1-직접-접근)
  * [(2) 간접 접근 ](#2-간접-접근)
* [직접 접근의 원천 봉쇄](#직접-접근의-원천-봉쇄)
* [\_\_dict\_\_ 스페셜 메소드](#dict-스페셜-메소드)
* [직접 접근의 봉쇄가 가능했던 이유](#직접-접근의-봉쇄가-가능했던-이유)


<br>

---

## <span style="color:navy">속성 감춤의 필요성</span>

- 프로그래머의 실수로 인한 오류는 종종 실행 시점에서는 잘 동작하는 것처럼 보이지만, 실제로는 예상치 못한 결과를 초래할 수 있다. 이러한 문제는 실제 서비스에서 발생할 경우 큰 피해를 줄 수 있다.

- 예를 들어, 다음과 같이 주민등록상의 만 나이를 직접 조정하는 경우가 문제가 될 수 있다.
    
    ```python
    class person:
        def __init__(self, n, a):
            self.name = n
            self.age = a
        def __str__(self):
            return '{0}: {1}'.format(self.name, self.age)
    
    def main():
        p = person('James', 22)
        print(p)
        p.age -= 1 # (이슈) 나이를 감소시킴
        print(p)
    
    main()
    # James: 22
    # James: 21
    ```
  
<br>  

- 이러한 코드의 문제점은 <u>객체의 속성에 직접 접근하게 되면, 해당 속성의 값이 어떻게 변경될지 예측하기 어렵다는 것</u>입니다. 만약 나이를 조정하는 메소드가 제공되었고, 그 메소드 내에서 입력값의 유효성을 검사하고 오류를 반환하는 로직이 포함되어 있었다면, 위와 같은 실수는 발생하지 않았을 것입니다.
- 따라서, <u>객체의 속성에 직접 접근하는 것은 데이터의 무결성을 해칠 수 있으므로, 속성을 감추어 외부에서의 접근을 제한하고, 속성을 조작하는 메소드를 통해 안전하게 데이터를 관리하는 것이 바람직</u>하다.

<br>

---

## <span style="color:navy">직접 접근 & 간접 접근</span>

### <span style="color:navy">(1) 직접 접근</span>

- 클래스 외부에서 `[클래스객체, 클래스이름].변수이름`과 같은 방법으로 변수에 직접적으로 접근하는 방식을 의미한다.
- 그리고, 이러한 직접 접근을 막는 것을 `정보 은닉`이라고한다.

<br>

### <span style="color:navy">(2) 간접 접근 </span>

- 메소드를 거쳐서 변수에 간접적으로 접근시키는 방식을 의미한다.
- 해당 접근 방식으로 `정보 은닉`을 수행할 수 있다.
- 즉, 메소드를 통해서만 접근할 수 있게 함으로서 안전성을 높이는 것이다.<br>
(기능의 관점에서는 아무런 이득이 없지만, 안전성 측면에서는 좋다.)
    
    ```python
    class person:
        def __init__(self, n, a):
            self.name = n
            self.age = a
    
        def add_age(self, a):
            if(a < 0):
                print('나이 정보 오류')
            else:
                self.age += a
    
        def __str__(self):
            return '{0}: {1}'.format(self.name, self.age)
    
    def main():
        p = person('James', 22)
        p.add_age(1) # 메소드를 통해서 변수에 간접 접근
        print(p)
    
    main()
    ```

<br>

---

## <span style="color:navy">직접 접근의 원천 봉쇄</span>

- 만약 p.age와 같은 직접접근을 100% 차단하고 싶으면 다음과 같이 <br>class의 변수 속성 이름앞에 `__`를 붙여주면 된다.

  - `self.age` ⇒ `self.__age`
  - `self.name` ⇒ `self.__name`

<br>

- 만약, `__`가 붙어있는 변수에 직접 접근을 시도하려고하면 다음과 같이 `AttributeError`를 발생시키면서 class 객체에 색인 변수가 없다며 착한 거짓말을 선 보인다. 

    ```python
    class person:
        def __init__(self, n, a):
            self.__name = n
            self.__age = a
        def add_age(self, a):
            if(a < 0):
                print('나이 정보 오류')
            else:
                self.__age += a
        def __str__(self):
            return '{0}: {1}'.format(self.__name, self.__age)
    
    def main():
            p = person('James', 22)
        print(p.name) # James
        print(p.__age) # AttributeError: 'person' object has no attribute '__age'
    
    main()
    ```

<br>

- 하지만, `_`를 2개씩이나 붙여주는 것은 스페셜 메소드와 비슷하기에 code상에서는 시각적으로 좋지 않아서 프로그래머들 사이에서는 암묵적으로 `_`를 하나만 붙여주어 절대 직접 접근하지말라고 경고를 보내기도 한다.

<br>

---

## <span style="color:navy">\_\_dict\_\_ 스페셜 메소드</span>

- class객체 내에는 해당 객체의 변수 정보를 담고 있는 `딕셔너리`가 객체 당 한개 씩 존재한다.

    ```python
    class personperson:
        def __init__(self, n, a):
            self.name = n
            self.age = a
    
    def main():
        x = personperson('James', 22)
        print(x.__dict__)
    
    main() # {'name': 'James', 'age': 22}
    ```

<br>

- 만약, 객체에 변수가 추가되거나 기존 변수의 값이 변경되면 `__dict__`에도 정보의 변경사항이 반영된다.

    ```python
    class personperson:
        def __init__(self, n, a):
            self.name = n
            self.age = a
    
    def main():
        x = personperson('James', 22)
        print(x.__dict__)
        x.gender = 'M' # gender라는 변수를 객체에 추가
        x.name = 'Mic' # name라는 변수의 값을 변경
        print(x.__dict__)
    
    main() # {'name': 'Mic', 'age': 22, 'gender': 'M'}
    ```

<br>

- 또한,  `__dict__`를 이용해서 객체 내 변수의 값도 수정 가능하다. <br>즉, 객체 내에 있는 변수의 값은 사실 `__dict__`를 통해서 관리됨을 알 수 있다.

    ```python
    class personperson:
        def __init__(self, n, a):
            self.name = n
            self.age = a
    
    def main():
        x = personperson('James', 22)
        x.__dict__['name'] = 'doradora'
        x.__dict__['city'] = 'seoul'
        print(x.__dict__)
     
    main() # {'name': 'doradora', 'age': 22, 'city': 'seoul'}
    ```

<br>

- class객체에서의 `__dict__`를 정리하면 다음과 같다. 
  - class객체의 변수 정보를 딕셔너리 형태로 담고 있다. 
  - class객체의 추가 및 변동 사항 또한 `__dict__`에 반영된다. 
  - class객체의 변수는 `__dict__`를 이용해서 추가 및 변경가능하다. 
  - 즉, 객체 내에 있는 변수의 관리자는 바로 `__dict__`이다.

<br>

---

## <span style="color:navy">직접 접근의 봉쇄가 가능했던 이유<span>


- `__`를 사용했을 때 변수의 이름으로 직접 접근이 불가능했던 이유는 `__dict__`를 통해서 확인할 수 있다. 

<br>

- 다음의 예제에서 `__`가  붙은 변수를 가지고 있는 class객체의 딕셔너리를 확인해보면 다음과 같이 `__dict__`에 등록된 속성의 이름이 다음과 같은 패턴으로 수정되었음을 알 수 있다.

    | __AttrName | _ClassName_AttrName |
    | --- | --- |
    | __name | _sample__name |
    | __age | _sample__age |

- 즉, 변수 이름에 언더바를 두 개 붙이면 파이썬은 접두에 _sample을 붙이는 패턴으로 변수 이름을 바꾸어 버리기 때문에 기존에 생성한 변수의 이름을 통해서 객체 외부에서 접근이 불가능 했던 것이다.

<br>

- 따라서, 변수 이름에 언더바를 두 개 붙이더라도 바뀐 이름으로 접근하다면 그 접근은 막지 못한다.

    ```python
    class sample:
        def __init__(self, x, y):
            self.__x = x
            self.__y = y
    
    def main():
        s = sample(10, 50)
        print(s.__dict__) #{'_sample__x': 10, '_sample__y': 50}
    
        print(s._sample__x) # 10
        print(s._sample__y) # 50
        
    main()
    ```

<br>

---

## <span style="color:navy">References</span>
- 윤성우, 『윤성우의 열혈 파이썬 중급편』, ORANGE MEDIA(2021)
