---
layout: post
title: "[자료구조] 8. 스택 (Stacks)"
subtitle: "[자료구조] 8. 스택 (Stacks)"
categories: data
tags: datastructure
comments: true
---
#### Contents
- [스택 (Stacks)](#스택-stacks)
- [스택의 추상적 자료구조 구현](#스택의-추상적-자료구조-구현)
    - [(1) 연산의 정의](#1-연산의-정의)
    - [(2) 배열(Array)를 이용하여 구현](#2-배열array를-이용하여-구현)
    - [(3) 양방향 연결리스트 (Doubly Linked List)를 이용하여 구현](#3-양방향-연결리스트--Doubly-linked-list를-이용하여-구현)
    - [(4) 파이썬 라이브러리](#4-파이썬-라이브러리)

<br>
  
---

## <span style="color:navy">스택 (Stacks)<span>

- 스택이란 자료를 순차적으로 보관할 수 있는 선형구조이다.

  - 스택은 `후입선출(LIFO, Last-In First-Out)`형식의 구조를 가지고 있다.
      - **후입선출**: 가장 최근에 들어온 데이터가 가장 먼저 나가는 데이터 관리 방식이다.
      - 후입선출 구조를 수행하기위해 스택은 `push`, `pop`을 통해 데이터를 관리한다.
  
  - **push**: 스택에 데이터를 추가
    
      [![](https://mermaid.ink/img/eyJjb2RlIjoiICAgICAgIGZsb3djaGFydCBUQlxuICAgICAgICBjbGFzc0RlZiBncmVlbiBmaWxsOmdyZWVuLCBzdHJva2U6YmxhY2ssIHN0cm9rZS13aWR0aDoycHgsIGNvbG9yOndoaXRlXG4gICAgICAgIGNsYXNzRGVmIHJlZF93IHN0cm9rZS13aWR0aDoycHgsIGNvbG9yOnJlZFxuICAgICAgICBcbiAgICAgICAgZGF0YTFbXCJEYXRhMVwiXVxuICAgICAgICBkYXRhMV8xW0RhdGEyXTo6OnJlZF93XG4gICAgICAgIGV4aXN0X25vZGUxKFwiKOy2nCnsnoXqtazwn4-DXCIpOjo6Z3JlZW5cbiAgICAgICAgXG4gICAgICAgIHN1YmdyYXBoIFwiU3RhY2tcIlxuICAgICAgICBkYXRhMSAtLS0gZXhpc3Rfbm9kZTEgLS1cInB1c2goRGF0YTIpXCItLS0gZGF0YTFfMTtcbiAgICAgICAgZW5kIiwibWVybWFpZCI6eyJ0aGVtZSI6ImRlZmF1bHQifSwidXBkYXRlRWRpdG9yIjpmYWxzZX0)](https://mermaid-js.github.io/docs/deprecated-editor/#/edit/eyJjb2RlIjoiICAgICAgIGZsb3djaGFydCBUQlxuICAgICAgICBjbGFzc0RlZiBncmVlbiBmaWxsOmdyZWVuLCBzdHJva2U6YmxhY2ssIHN0cm9rZS13aWR0aDoycHgsIGNvbG9yOndoaXRlXG4gICAgICAgIGNsYXNzRGVmIHJlZF93IHN0cm9rZS13aWR0aDoycHgsIGNvbG9yOnJlZFxuICAgICAgICBcbiAgICAgICAgZGF0YTFbXCJEYXRhMVwiXVxuICAgICAgICBkYXRhMV8xW0RhdGEyXTo6OnJlZF93XG4gICAgICAgIGV4aXN0X25vZGUxKFwiKOy2nCnsnoXqtazwn4-DXCIpOjo6Z3JlZW5cbiAgICAgICAgXG4gICAgICAgIHN1YmdyYXBoIFwiU3RhY2tcIlxuICAgICAgICBkYXRhMSAtLS0gZXhpc3Rfbm9kZTEgLS1cInB1c2goRGF0YTIpXCItLS0gZGF0YTFfMTtcbiAgICAgICAgZW5kIiwibWVybWFpZCI6eyJ0aGVtZSI6ImRlZmF1bHQifSwidXBkYXRlRWRpdG9yIjpmYWxzZX0)

      [![](https://mermaid.ink/img/eyJjb2RlIjoiICAgICAgICBmbG93Y2hhcnQgVEJcbiAgICAgICAgY2xhc3NEZWYgZ3JlZW4gZmlsbDpncmVlbiwgc3Ryb2tlOmJsYWNrLCBzdHJva2Utd2lkdGg6MnB4LCBjb2xvcjp3aGl0ZVxuICAgICAgICBjbGFzc0RlZiByZWRfdyBzdHJva2Utd2lkdGg6MnB4LCBjb2xvcjpyZWRcbiAgICAgICAgXG4gICAgICAgIGRhdGEyW1wiRGF0YTFcIl1cbiAgICAgICAgZGF0YTJfMVtEYXRhMl06OjpyZWRfd1xuICAgICAgICBleGlzdF9ub2RlMihcIijstpwp7J6F6rWs8J-Pg1wiKTo6OmdyZWVuXG4gICAgICAgIFxuICAgICAgICBzdWJncmFwaCBcIlN0YWNrXCJcbiAgICAgICAgZGF0YTIgLS0tIGRhdGEyXzEgLS0tIGV4aXN0X25vZGUyO1xuICAgICAgICBlbmQiLCJtZXJtYWlkIjp7InRoZW1lIjoiZGVmYXVsdCJ9LCJ1cGRhdGVFZGl0b3IiOmZhbHNlfQ)](https://mermaid-js.github.io/docs/deprecated-editor/#/edit/eyJjb2RlIjoiICAgICAgICBmbG93Y2hhcnQgVEJcbiAgICAgICAgY2xhc3NEZWYgZ3JlZW4gZmlsbDpncmVlbiwgc3Ryb2tlOmJsYWNrLCBzdHJva2Utd2lkdGg6MnB4LCBjb2xvcjp3aGl0ZVxuICAgICAgICBjbGFzc0RlZiByZWRfdyBzdHJva2Utd2lkdGg6MnB4LCBjb2xvcjpyZWRcbiAgICAgICAgXG4gICAgICAgIGRhdGEyW1wiRGF0YTFcIl1cbiAgICAgICAgZGF0YTJfMVtEYXRhMl06OjpyZWRfd1xuICAgICAgICBleGlzdF9ub2RlMihcIijstpwp7J6F6rWs8J-Pg1wiKTo6OmdyZWVuXG4gICAgICAgIFxuICAgICAgICBzdWJncmFwaCBcIlN0YWNrXCJcbiAgICAgICAgZGF0YTIgLS0tIGRhdGEyXzEgLS0tIGV4aXN0X25vZGUyO1xuICAgICAgICBlbmQiLCJtZXJtYWlkIjp7InRoZW1lIjoiZGVmYXVsdCJ9LCJ1cGRhdGVFZGl0b3IiOmZhbHNlfQ)        
       
    <br>    

  - **pop**: 스택에서 가장 최근에 추가된 데이터를 제거
        
    [![](https://mermaid.ink/img/eyJjb2RlIjoiICAgICBmbG93Y2hhcnQgVEJcbiAgICAgIGNsYXNzRGVmIGdyZWVuIGZpbGw6Z3JlZW4sIHN0cm9rZTpibGFjaywgc3Ryb2tlLXdpZHRoOjJweCwgY29sb3I6d2hpdGVcbiAgICAgIGNsYXNzRGVmIHJlZF93IHN0cm9rZS13aWR0aDoycHgsIGNvbG9yOnJlZFxuICAgICAgICBcbiAgICAgIGRhdGEyW0RhdGExXVxuICAgICAgZGF0YTNbRGF0YTJdOjo6cmVkX3dcbiAgICAgICAgXG4gICAgICBleGlzdF9ub2RlMShcIuy2nCjsnoUp6rWs8J-Pg1wiKTo6OmdyZWVuXG4gICAgICAgIFxuICAgICAgc3ViZ3JhcGggXCJTdGFja1wiXG4gICAgICBkYXRhMiAtLS0gZGF0YTMgXG4gICAgICBkYXRhMyAtLVwicG9wKERhdGEyKVwiLS0tIGV4aXN0X25vZGUxXG4gICAgICBlbmQiLCJtZXJtYWlkIjp7InRoZW1lIjoiZGVmYXVsdCJ9LCJ1cGRhdGVFZGl0b3IiOmZhbHNlfQ)](https://mermaid-js.github.io/docs/deprecated-editor/#/edit/eyJjb2RlIjoiICAgICBmbG93Y2hhcnQgVEJcbiAgICAgIGNsYXNzRGVmIGdyZWVuIGZpbGw6Z3JlZW4sIHN0cm9rZTpibGFjaywgc3Ryb2tlLXdpZHRoOjJweCwgY29sb3I6d2hpdGVcbiAgICAgIGNsYXNzRGVmIHJlZF93IHN0cm9rZS13aWR0aDoycHgsIGNvbG9yOnJlZFxuICAgICAgICBcbiAgICAgIGRhdGEyW0RhdGExXVxuICAgICAgZGF0YTNbRGF0YTJdOjo6cmVkX3dcbiAgICAgICAgXG4gICAgICBleGlzdF9ub2RlMShcIuy2nCjsnoUp6rWs8J-Pg1wiKTo6OmdyZWVuXG4gICAgICAgIFxuICAgICAgc3ViZ3JhcGggXCJTdGFja1wiXG4gICAgICBkYXRhMiAtLS0gZGF0YTMgXG4gICAgICBkYXRhMyAtLVwicG9wKERhdGEyKVwiLS0tIGV4aXN0X25vZGUxXG4gICAgICBlbmQiLCJtZXJtYWlkIjp7InRoZW1lIjoiZGVmYXVsdCJ9LCJ1cGRhdGVFZGl0b3IiOmZhbHNlfQ)
        
    [![](https://mermaid.ink/img/eyJjb2RlIjoiICAgICAgZmxvd2NoYXJ0IExSXG4gICAgICBjbGFzc0RlZiBncmVlbiBmaWxsOmdyZWVuLCBzdHJva2U6YmxhY2ssIHN0cm9rZS13aWR0aDoycHgsIGNvbG9yOndoaXRlXG4gICAgICBjbGFzc0RlZiByZWRfdyBzdHJva2Utd2lkdGg6MnB4LCBjb2xvcjpyZWRcbiAgICAgICAgXG4gICAgICBkYXRhMl8xW0RhdGExXVxuICAgICAgZGF0YTNfMVtEYXRhMl06OjpyZWRfd1xuICAgICAgICBcbiAgICAgIGV4aXN0X25vZGUyKFwi7LacKOyehSnqtazwn4-DXCIpOjo6Z3JlZW5cbiAgICAgICAgXG4gICAgICBzdWJncmFwaCBcIlN0YWNrXCJcbiAgICAgIGRhdGEyXzEgLS0tIGV4aXN0X25vZGUyXG4gICAgICBlbmRcbiAgICAgIGV4aXN0X25vZGUyLS0tIGRhdGEzXzEiLCJtZXJtYWlkIjp7InRoZW1lIjoiZGVmYXVsdCJ9LCJ1cGRhdGVFZGl0b3IiOmZhbHNlfQ)](https://mermaid-js.github.io/docs/deprecated-editor/#/edit/eyJjb2RlIjoiICAgICAgZmxvd2NoYXJ0IExSXG4gICAgICBjbGFzc0RlZiBncmVlbiBmaWxsOmdyZWVuLCBzdHJva2U6YmxhY2ssIHN0cm9rZS13aWR0aDoycHgsIGNvbG9yOndoaXRlXG4gICAgICBjbGFzc0RlZiByZWRfdyBzdHJva2Utd2lkdGg6MnB4LCBjb2xvcjpyZWRcbiAgICAgICAgXG4gICAgICBkYXRhMl8xW0RhdGExXVxuICAgICAgZGF0YTNfMVtEYXRhMl06OjpyZWRfd1xuICAgICAgICBcbiAgICAgIGV4aXN0X25vZGUyKFwi7LacKOyehSnqtazwn4-DXCIpOjo6Z3JlZW5cbiAgICAgICAgXG4gICAgICBzdWJncmFwaCBcIlN0YWNrXCJcbiAgICAgIGRhdGEyXzEgLS0tIGV4aXN0X25vZGUyXG4gICAgICBlbmRcbiAgICAgIGV4aXN0X25vZGUyLS0tIGRhdGEzXzEiLCJtZXJtYWlkIjp7InRoZW1lIjoiZGVmYXVsdCJ9LCJ1cGRhdGVFZGl0b3IiOmZhbHNlfQ)
        
    <br>

  - 스택에서는 다음과 같은 오류가 발생할 수 있다.
      - **Stack Underflow:** 꺼낼 데이터가 없는데 꺼내려고 시도할 때 발생하는 오류
      - **Stack Overflow**: 스택이 꽉차 있는 상태에서 데이터 원소를 넣으려고 시도할 때 발생하는 오류

<br>

---

## <span style="color:navy">스택의 추상적 자료구조 구현<span>

### <span style="color:navy">(1) 연산의 정의<span>

- `size()`: 스택에 들어있는 데이터 원소의 수를 구한다.
- `isEmpty()`: 스택이 비어있는지 확인한다.
- `push(x)`: 스택에 데이터 원소 x를 스택에 추가한다.
- `pop()`: 스택의 맨 위에 저장된 데이터 원소를 제거하고 반환한다.
- `peek()`: 스택의 맨 위에 저장된 데이터 원소가 무엇인지 확인한다.
    
<br>

### <span style="color:navy">(2) 배열(Array)를 이용하여 구현<span>

```python
class ArrayStack:

    def __init__(self):
            self.data = [] # 빈 스택을 초기화

    def size(self):
            return len(self.data) # 스택의 크기를 리턴

    def isEmpty(self):
            return self.size()==0 # 스택이 비어 있는지 판단

    def push(self, item):
            self.data.append(item) # 데이터 원소를 추가 

    def pop(self):
            self.data.pop() # 데이터 원소를 삭제 (리턴)

    def peek(self):
            return self.data[-1] # 스택의 꼭대기 원소 반환 
```

<br>

### <span style="color:navy">(3) 양방향 연결리스트 (Doubly Linked List)를 이용하여 구현<span>

```python
# 노드, 양방향연결리스트의 모듈 코드는 이전 포스팅을 참고 부탁드립니다.
from utils_practice.node_v2 import Node
from utils_practice.linkedlist_v2 import DoublyLinkedList

class LinkedListStack:

    def __init__(self):
        self.data = DoublyLinkedList() # 빈 스택을 초기화
    
    def size(self):
        return self.data.nodeCount # 스택의 크기를 리턴
    
    def isEmpty(self):
        return self.size() == 0 # 스택이 비어 있는지 판단
    
    def push(self, item):
        node = Node(item)
        self.data.insertAt(self.size() + 1, node) # 데이터 원소를 추가
    
    def pop(self):
        return self.data.popAt(self.size()) # 데이터 원소를 삭제 (리턴)
    
    def peek(self):
        return self.data.getAt(self.size()).data # 스택의 꼭대기 원소 반환 
```

<br>

### <span style="color:navy">(4) 파이썬 라이브러리<span>

- 스택은 굉장히 많이 사용되는 알고리즘이기에 파이썬 라이브러리 형태로도 존재한다.
    
    ```python
    from pythonds.basic.stack import Stack
    ```
  
<br>

---


## <span style="color:navy">References<span>
- [Programmers, 어서와! 자료구조와 알고리즘은 처음이지?](https://school.programmers.co.kr/learn/courses/57/57-%EC%96%B4%EC%84%9C%EC%99%80-%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0%EC%99%80-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98%EC%9D%80-%EC%B2%98%EC%9D%8C%EC%9D%B4%EC%A7%80)
