---
layout: post
title: "[자료구조] 11. 큐 (Queue)"
subtitle: "[자료구조] 11. 큐 (Queue)"
categories: data
tags: datastructure
comments: true
---
#### Contents
- [큐 (Queue)](#큐-queue)
  - [(1) 인큐(Enqueue) 연산](#1-인큐enqueue-연산)
  - [(2) 디큐(Dequeue) 연산](#2-디큐dequeue-연산)
- [큐의 추상적 자료구조 구현](#큐의-추상적-자료구조-구현)
  - [(1) 연산의 정의](#1-연산의-정의)
  - [(2) 배열(Array)를 이용하여 구현](#2-배열array를-이용하여-구현)
  - [(3) 양방향 연결리스트 (Doubly Linked List)를 이용하여 구현](#3-양방향-연결리스트-doubly-linked-list를-이용하여-구현)

<br>

---

## <span style="color:navy">큐 (Queue)<span>

- `큐(Queue)`는 <u>Data 원소를 보관할 수 있는 선형 구조 알고리즘이다</u>.
- 어떻게 보면 앞서 후입선출(LIFO, Last-In First-Out)형인 스택(Stack)과 비슷한 것으로 보이지만,
큐는 데이터 원소를 <u>인풋하는 방향과 아웃풋하는 방향이 서로 반대</u>인 `선입선출(FIFO, First-In First-Out)`형 알고리즘이다.

<br>

### <span style="color:navy">(1) 인큐(Enqueue) 연산<span>

- `인큐`는 큐에 새로운 <u>데이터를 추가하는 연산</u>이다.
- 데이터는 큐의 <u>맨 뒤에 삽입</u>된다.

    [![](https://mermaid.ink/img/eyJjb2RlIjoiICAgIGZsb3djaGFydCBMUlxuICAgIGNsYXNzRGVmIGdyZWVuIGZpbGw6Z3JlZW4sIHN0cm9rZTpibGFjaywgc3Ryb2tlLXdpZHRoOjJweCwgY29sb3I6d2hpdGVcbiAgICBjbGFzc0RlZiByZWRfdyBzdHJva2Utd2lkdGg6MnB4LCBjb2xvcjpyZWRcbiAgICBcbiAgICBleGlzdF9ub2RlX2luMihcIuy2nOq1rPCfj4NcIik6OjpncmVlblxuICAgIGV4aXN0X25vZGVfb3V0MihcIuyeheq1rPCfj4NcIik6OjpncmVlblxuICAgIE5vZGUxKFwiRGF0YTJcIik6OjpyZWRfd1xuICAgIE5vZGUxXzEoXCJEYXRhMVwiKVxuICAgIFxuICAgIHN1YmdyYXBoIFwiUXVldWVcIlxuICAgIFx0ZXhpc3Rfbm9kZV9pbjIgLS0tTm9kZTFfMS0tLSBleGlzdF9ub2RlX291dDI7XG4gICAgZW5kO1xuICAgIGV4aXN0X25vZGVfb3V0MiAtLVwiZW5xdWV1ZShEYXRhMilcIi0tLSBOb2RlMTsiLCJtZXJtYWlkIjp7InRoZW1lIjoiZGVmYXVsdCJ9LCJ1cGRhdGVFZGl0b3IiOmZhbHNlfQ)](https://mermaid-js.github.io/docs/deprecated-editor/#/edit/eyJjb2RlIjoiICAgIGZsb3djaGFydCBMUlxuICAgIGNsYXNzRGVmIGdyZWVuIGZpbGw6Z3JlZW4sIHN0cm9rZTpibGFjaywgc3Ryb2tlLXdpZHRoOjJweCwgY29sb3I6d2hpdGVcbiAgICBjbGFzc0RlZiByZWRfdyBzdHJva2Utd2lkdGg6MnB4LCBjb2xvcjpyZWRcbiAgICBcbiAgICBleGlzdF9ub2RlX2luMihcIuy2nOq1rPCfj4NcIik6OjpncmVlblxuICAgIGV4aXN0X25vZGVfb3V0MihcIuyeheq1rPCfj4NcIik6OjpncmVlblxuICAgIE5vZGUxKFwiRGF0YTJcIik6OjpyZWRfd1xuICAgIE5vZGUxXzEoXCJEYXRhMVwiKVxuICAgIFxuICAgIHN1YmdyYXBoIFwiUXVldWVcIlxuICAgIFx0ZXhpc3Rfbm9kZV9pbjIgLS0tTm9kZTFfMS0tLSBleGlzdF9ub2RlX291dDI7XG4gICAgZW5kO1xuICAgIGV4aXN0X25vZGVfb3V0MiAtLVwiZW5xdWV1ZShEYXRhMilcIi0tLSBOb2RlMTsiLCJtZXJtYWlkIjp7InRoZW1lIjoiZGVmYXVsdCJ9LCJ1cGRhdGVFZGl0b3IiOmZhbHNlfQ)

    [![](https://mermaid.ink/img/eyJjb2RlIjoiICAgIGZsb3djaGFydCBUQlxuICAgIGNsYXNzRGVmIGdyZWVuIGZpbGw6Z3JlZW4sIHN0cm9rZTpibGFjaywgc3Ryb2tlLXdpZHRoOjJweCwgY29sb3I6d2hpdGVcbiAgICBjbGFzc0RlZiByZWRfdyBzdHJva2Utd2lkdGg6MnB4LCBjb2xvcjpyZWRcbiAgICBcbiAgICBleGlzdF9ub2RlX2luMihcIuy2nOq1rPCfj4NcIik6OjpncmVlblxuICAgIGV4aXN0X25vZGVfb3V0MihcIuyeheq1rPCfj4NcIik6OjpncmVlblxuICAgIE5vZGUxKFwiRGF0YTJcIik6OjpyZWRfd1xuICAgIE5vZGUyKFwiRGF0YTFcIilcbiAgICBcbiAgICBzdWJncmFwaCBcIlF1ZXVlXCJcbiAgICBcdGV4aXN0X25vZGVfaW4yIC0tLU5vZGUyLS0tTm9kZTEtLS1leGlzdF9ub2RlX291dDI7XG4gICAgZW5kO1xuIiwibWVybWFpZCI6eyJ0aGVtZSI6ImRlZmF1bHQifSwidXBkYXRlRWRpdG9yIjpmYWxzZX0)](https://mermaid-js.github.io/docs/deprecated-editor/#/edit/eyJjb2RlIjoiICAgIGZsb3djaGFydCBUQlxuICAgIGNsYXNzRGVmIGdyZWVuIGZpbGw6Z3JlZW4sIHN0cm9rZTpibGFjaywgc3Ryb2tlLXdpZHRoOjJweCwgY29sb3I6d2hpdGVcbiAgICBjbGFzc0RlZiByZWRfdyBzdHJva2Utd2lkdGg6MnB4LCBjb2xvcjpyZWRcbiAgICBcbiAgICBleGlzdF9ub2RlX2luMihcIuy2nOq1rPCfj4NcIik6OjpncmVlblxuICAgIGV4aXN0X25vZGVfb3V0MihcIuyeheq1rPCfj4NcIik6OjpncmVlblxuICAgIE5vZGUxKFwiRGF0YTJcIik6OjpyZWRfd1xuICAgIE5vZGUyKFwiRGF0YTFcIilcbiAgICBcbiAgICBzdWJncmFwaCBcIlF1ZXVlXCJcbiAgICBcdGV4aXN0X25vZGVfaW4yIC0tLU5vZGUyLS0tTm9kZTEtLS1leGlzdF9ub2RlX291dDI7XG4gICAgZW5kO1xuIiwibWVybWFpZCI6eyJ0aGVtZSI6ImRlZmF1bHQifSwidXBkYXRlRWRpdG9yIjpmYWxzZX0)

<br>

### <span style="color:navy">(2) 디큐(Dequeue) 연산<span>

- `디큐`는 큐에 <u>데이터를 제거하는 연산</u>이다.
- 큐의 <u>맨 앞에 위치한 데이터가 제거</u>된다.
    
    [![](https://mermaid.ink/img/eyJjb2RlIjoiICAgIGZsb3djaGFydCBUQlxuICAgIGNsYXNzRGVmIGdyZWVuIGZpbGw6Z3JlZW4sIHN0cm9rZTpibGFjaywgc3Ryb2tlLXdpZHRoOjJweCwgY29sb3I6d2hpdGVcbiAgICBjbGFzc0RlZiByZWRfdyBzdHJva2Utd2lkdGg6MnB4LCBjb2xvcjpyZWRcbiAgICBcbiAgICBleGlzdF9ub2RlX2luMihcIuy2nOq1rPCfj4NcIik6OjpncmVlblxuICAgIGV4aXN0X25vZGVfb3V0MihcIuyeheq1rPCfj4NcIik6OjpncmVlblxuICAgIE5vZGUxKFwiRGF0YTFcIik6OjpyZWRfd1xuICAgIE5vZGUxXzEoXCJEYXRhMlwiKVxuICAgIFxuICAgIHN1YmdyYXBoIFwiUXVldWVcIlxuICAgIFx0ZXhpc3Rfbm9kZV9pbjIgLS1cImRlcXVldWUoRGF0YTEpXCItLS0gTm9kZTEgLS0tIE5vZGUxXzEtLS0gZXhpc3Rfbm9kZV9vdXQyO1xuICAgIGVuZCIsIm1lcm1haWQiOnsidGhlbWUiOiJkZWZhdWx0In0sInVwZGF0ZUVkaXRvciI6ZmFsc2V9)](https://mermaid-js.github.io/docs/deprecated-editor/#/edit/eyJjb2RlIjoiICAgIGZsb3djaGFydCBUQlxuICAgIGNsYXNzRGVmIGdyZWVuIGZpbGw6Z3JlZW4sIHN0cm9rZTpibGFjaywgc3Ryb2tlLXdpZHRoOjJweCwgY29sb3I6d2hpdGVcbiAgICBjbGFzc0RlZiByZWRfdyBzdHJva2Utd2lkdGg6MnB4LCBjb2xvcjpyZWRcbiAgICBcbiAgICBleGlzdF9ub2RlX2luMihcIuy2nOq1rPCfj4NcIik6OjpncmVlblxuICAgIGV4aXN0X25vZGVfb3V0MihcIuyeheq1rPCfj4NcIik6OjpncmVlblxuICAgIE5vZGUxKFwiRGF0YTFcIik6OjpyZWRfd1xuICAgIE5vZGUxXzEoXCJEYXRhMlwiKVxuICAgIFxuICAgIHN1YmdyYXBoIFwiUXVldWVcIlxuICAgIFx0ZXhpc3Rfbm9kZV9pbjIgLS1cImRlcXVldWUoRGF0YTEpXCItLS0gTm9kZTEgLS0tIE5vZGUxXzEtLS0gZXhpc3Rfbm9kZV9vdXQyO1xuICAgIGVuZCIsIm1lcm1haWQiOnsidGhlbWUiOiJkZWZhdWx0In0sInVwZGF0ZUVkaXRvciI6ZmFsc2V9)    
    
    [![](https://mermaid.ink/img/eyJjb2RlIjoiICAgIGZsb3djaGFydCBMUlxuICAgIGNsYXNzRGVmIGdyZWVuIGZpbGw6Z3JlZW4sIHN0cm9rZTpibGFjaywgc3Ryb2tlLXdpZHRoOjJweCwgY29sb3I6d2hpdGVcbiAgICBjbGFzc0RlZiByZWRfdyBzdHJva2Utd2lkdGg6MnB4LCBjb2xvcjpyZWRcbiAgICBcbiAgICBleGlzdF9ub2RlX2luMShcIuy2nOq1rPCfj4NcIik6OjpncmVlblxuICAgIGV4aXN0X25vZGVfb3V0MShcIuyeheq1rPCfj4NcIik6OjpncmVlblxuICAgIE5vZGUyKFwiRGF0YTFcIik6OjpyZWRfd1xuICAgIE5vZGUyXzEoXCJEYXRhMlwiKVxuICAgIFxuICAgIE5vZGUyIC0tLSBleGlzdF9ub2RlX2luMVxuICAgIHN1YmdyYXBoIFwiUXVldWVcIlxuICAgIGV4aXN0X25vZGVfaW4xIC0tLSBOb2RlMl8xLS0tIGV4aXN0X25vZGVfb3V0MTtcbiAgICBlbmQiLCJtZXJtYWlkIjp7InRoZW1lIjoiZGVmYXVsdCJ9LCJ1cGRhdGVFZGl0b3IiOmZhbHNlfQ)](https://mermaid-js.github.io/docs/deprecated-editor/#/edit/eyJjb2RlIjoiICAgIGZsb3djaGFydCBMUlxuICAgIGNsYXNzRGVmIGdyZWVuIGZpbGw6Z3JlZW4sIHN0cm9rZTpibGFjaywgc3Ryb2tlLXdpZHRoOjJweCwgY29sb3I6d2hpdGVcbiAgICBjbGFzc0RlZiByZWRfdyBzdHJva2Utd2lkdGg6MnB4LCBjb2xvcjpyZWRcbiAgICBcbiAgICBleGlzdF9ub2RlX2luMShcIuy2nOq1rPCfj4NcIik6OjpncmVlblxuICAgIGV4aXN0X25vZGVfb3V0MShcIuyeheq1rPCfj4NcIik6OjpncmVlblxuICAgIE5vZGUyKFwiRGF0YTFcIik6OjpyZWRfd1xuICAgIE5vZGUyXzEoXCJEYXRhMlwiKVxuICAgIFxuICAgIE5vZGUyIC0tLSBleGlzdF9ub2RlX2luMVxuICAgIHN1YmdyYXBoIFwiUXVldWVcIlxuICAgIGV4aXN0X25vZGVfaW4xIC0tLSBOb2RlMl8xLS0tIGV4aXN0X25vZGVfb3V0MTtcbiAgICBlbmQiLCJtZXJtYWlkIjp7InRoZW1lIjoiZGVmYXVsdCJ9LCJ1cGRhdGVFZGl0b3IiOmZhbHNlfQ)

<br>

---

## <span style="color:navy">큐의 추상적 자료구조 구현<span>

### <span style="color:navy">(1) 연산의 정의<span>

- `size()`: 현재 큐에 들어있는 데이터 원소의 수를 구한다.
- `isEmpty()`: 현재 큐가 비어있는지를 판단한다.
- `enqueue()`: 데이터 원소 x를 큐에 추가한다.
- `dequeue()`: 큐의 맨 앞에 저장된 데이터 원소를 제거하고 반환한다.
- `peek()`: 큐의 맨 앞에 저장된 데이터 원소를 반환한다. (*공개 라이브러리에는 X)

<br>

### <span style="color:navy">(2) 배열(Array)를 이용하여 구현<span>

- 이전 스택을 배열을 이용하여 구현했을 때와 <u>원소의 삭제를 제외</u>하고는 모두 동일하다.
- 큐의 연산복잡도는 `dequeue`<u>를 제외하고 모두 상수시간 알고리즘</u>\\( O(1) \\)에 해당한다.
    - 참고) 사이즈 반환의 경우, 리스트의 길이 정보가 리스트 객체 내에 직접 저장되기 때문에 상수시간 알고리즘\\(O(1)\\)에 해당한다.
- `dequeue`의 경우 <u>리스트의 맨 앞에 위치한 원소를 삭제하는 과정을 거치기 때문에</u>, 삭제 이후 index=0부터 데이터 원소를 채우기 위해서 index이동이 발생한다. 따라서, `dequeue`<u>는 큐내에 데이터 원소 수에 비례하여 처리 시간이 길어지는 선형시간 알고리즘</u>\\( O(n) \\)에 해당한다.
- 코드로 구현하면 다음과 같다.
    
    ```python
    class ArrayQueue:
    
        def __init__(self):
            self.data = [] # 빈 큐를 초기화

        def size(self):
            return len(self.data) # 큐의 크기를 리턴

        def isEmpty(self):
            return self.size()==0 # 큐가 비어 있는지 판단

        def enqueue(self, item):
            self.data.append(item) # 데이터 원소를 추가 

        def dequeue(self):
            self.data.pop(0) # 데이터 원소를 삭제 (리턴)

        def peek(self):
            return self.data[-1] # 큐의 맨 앞 원소 반환 
    ```

<br>

### <span style="color:navy">(3) 양방향 연결리스트 (Doubly Linked List)를 이용하여 구현<span>

- 위에서 설명했듯이, 배열 기반의 선형 큐는 `dequeue`작업 수행 시 선형 시간\\(O(n)\\) 알고리즘에 해당하여 담고 있는 데이터 원소의 수가 증가할수록 비효율적이다. 따라서, <u>배열보다는 인덱싱 이동이 필요없는 양방향 연결리스트로 구현하는 것</u>이 배열 기반으로 구현하는 것보다 <u>시간복잡도 측면에서 효율적</u>이다.
- 코드로 구현하면 다음과 같다.([모듈 코드 참고 링크](https://jhryu1208.github.io/data/2023/07/25/datastructure-doubly_linked_list/))
    
    ```python
    from utils_practice.node_v2 import Node
    from utils_practice.linkedlist_v2 import DoublyLinkedList
    
    class LinkedListQueue:
    
        def __init__(self):
            self.data = DoublyLinkedList()
    
        def size(self):
            return self.data.nodeCount
    
        def isEmpty(self):
            return self.size() == 0
    
        def enqueue(self, item):
            node = Node(item)
            self.data.insertAt(self.size()+1, node)
    
        def dequeue(self):
            return self.data.popAt(1)
    
        def peek(self):
            return self.data.getAt(1).data
    ```
    
<br>

### <span style="color:navy">(4) 파이썬 라이브러리<span>

- 스택은 굉장히 많이 사용되는 알고리즘이기에 파이썬 라이브러리 형태로도 존재한다.
    
    ```python
    from pythonds.basic.queue import Queue
    ```

<br>

---

## <span style="color:navy">References<span>
- [Programmers, 어서와! 자료구조와 알고리즘은 처음이지?](https://school.programmers.co.kr/learn/courses/57/57-%EC%96%B4%EC%84%9C%EC%99%80-%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0%EC%99%80-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98%EC%9D%80-%EC%B2%98%EC%9D%8C%EC%9D%B4%EC%A7%80)