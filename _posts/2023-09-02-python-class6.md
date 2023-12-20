---
layout: post
title: "[Py: Class] 7.__slots__"
date: 2023-09-02 00:00:00 +0900
categories: [Dev, Python]
tags: [Python, class, information hiding,attribute hiding, direct access, indirect access, special method]
comments: true
math: true
mermaid: true
---

## <span style="color:navy">`__dict__`의 단점</span>

- [앞서](https://jhryu1208.github.io/posts/python-class5/) 이야기했듯이 기본적으로 Python의 모든 객체는 `__dict__`라는 딕셔너리를 가지고 있어서 
객체의 속성을 저장한다. `__dict__`는 유연성을 제공하지만, <u>메모리를 상당히 많이 사용</u>한다. 

<br>

- 하지만, 아래와 같은 3차원 좌표 객체를 이용해 특정 물체를 상세히 모델링한다고 가정했을 때, 수백 개에서 수천 개가 넘는 객체가 생성되는 동시에, 딕셔너리가 차지하는 메모리 사이즈도 기하급수적 증가하여 메모리 오버헤드 문제가 발생될 수 있다.

    ```python
    class point3d:
        def __init__(self, x, y, z):
            self.x = x
            self.y = y
            self.z = z
        def __str__(self):
            return '({0}, {1}, {2})'.format(self.x, self.y, self.z)
    
    def main():
        p1 = point3d(1, 1, 1)
        p2 = point3d(2, 2, 2)
        print(p1)
        print(p2)
    
    main()
    ```

<br>

---

## <span style="color:navy">`__slots__` 이란?</span>

-  위와 같은 문제 상황에서 `__slots__`를 사용하면 메모리 사용량이 큰 `__dict__`를 생성하지 않고, 지정된 객체를 저장하기위한 <u>고정된 메모리를 할당</u>하 메모리 오버헤드 문제를 예방할 수 있다.
    - **장점**: 많은 수의 객체를 생성할 때 <u>1) 메모리 효율성을 증가</u>시키는 동시에, 값 탐색을 위해 해시 변환이 필요한 딕셔너리와 달리 속성의 위치를 미리 정의하여 고정시키기 때문에 <u>2) 연산속도를 높일 수(약 20% 향상)</u> 있다.
    - **단점**: 딕셔너리의 외부 접근을 통한 객체의 추가 삭제에 대한 유연성은 사라진다. 

    ```python
    class point3d: 
        __slots__ = ('x', 'y', 'z') # 변수를 x, y, z로 제한한다.
    
        def __init__(self, x, y, z):
            self.x = x
            self.y = y
            self.z = z
   
        def __str__(self):
            return '({0}, {1}, {2})'.format(self.x, self.y, self.z)
    
    def main():
        p1 = point3d(1, 1, 1)
        p2 = point3d(2, 2, 2)
        print(p1)
        print(p2)
    
    main()
    ```

<br>


- 따라서 `__slots__`의 사용은 다음의 상황에서 적절하다.
  - 메모리 최적화가 필요한 경우
  - 데이터 무결성을 유지하기 위해 속성의 동적 추가를 원하지 않는 경우
  
<br>

---

## <span style="color:navy">References</span>
- 윤성우, 『윤성우의 열혈 파이썬 중급편』, ORANGE MEDIA(2021)
