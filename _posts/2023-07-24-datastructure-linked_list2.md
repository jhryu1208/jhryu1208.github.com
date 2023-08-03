---
layout: post
title: "[자료구조] 6.연결 리스트-2 (Linked Lists)"
subtitle: "[자료구조] 6.연결 리스트-2 (Linked Lists))"
categories: data
tags: datastructure
comments: true
---
#### Contents
- [더미 노드 (Dummy Node)](#더미-노드-Dummy-Node)
- [연결리스트 핵심 연산 with 더미 노드](#연결리스트-핵심-연산-with-더미-노드)
  - [(1) N번째 특정 원소 탐색](#1-N번째-특정-원소-탐색)
  - [(2) 원소의 삽입 (Insertion)](#2-원소의-삽입-insertion)
  - [(3) 원소의 삭제 (Deletion)](#3-원소의-삭제-deletion)
  - [(4) 두 리스트 병합 (Concatenation)](#4-두-리스트-병합-concatenation)

<br>

---

## <span style="color:navy">더미 노드 (Dummy Node)<span>

- 연결 리스트는 포인터를 이용하기 때문에 선형 배열보다 <u>삽입과 삭제가 유연</u>하다는 장점을 가지고 있다. 하지만, 연결 리스트에 새로운 데이터 노드를 추가/삭제하는 과정에서 선형 탐색이 이루어진다는 단점을 가지고 있다.

- 위의 단점에 의해 발생하는 <u>비효율성 문제를 줄이기 위해 prev 노드(특정 N번째 노드)를 지정하여 노드를 추가/삭제하는 방법</u>이 존재한다. 하지만 <u>해당 방법은 prev노드가 없는 유효한 Head 노드를 추가/삭제하는 과정에서 문제</u>가 발생한다.

- 이때, 맨 앞의 노드를 추가/삭제하는 과정에서 발생되는 문제를 해결하기위해서 `더미 노드(Dummy Node)`를 연결리스트에 추가할 필요가 있다.
    - 더미 노드란, <u>데이터 원소를 담지 않고 있으며</u>, 이 중 Head 노드에 위치한 더미 노드를 `더미 헤드 노드(Dummy Head Node)`라 한다. (*[지난 포스팅](https://jhryu1208.github.io/data/2023/07/24/datastructure-linked_list1/)의 경우 Head노드의 인덱스를 1로 취급했는데, 더미 노드가 고려된 해당 포스팅에서는 Head노드의 인덱스를 0으로 취급할 것이다.)
    
  [![](https://mermaid.ink/img/eyJjb2RlIjoiICAgICUle2luaXQ6IHtcImZsb3djaGFydFwiOiB7XCJodG1sTGFiZWxzXCI6IGZhbHNlfX0gfSUlXG4gICAgZmxvd2NoYXJ0IExSXG4gICAgY2xhc3NEZWYgZ3JlZW4gZmlsbDpncmF5LCBzdHJva2U6YmxhY2ssIHN0cm9rZS13aWR0aDoycHgsIGNvbG9yOndoaXRlXG4gICAgXG4gICAgICAgIEhlYWRbXCJIZWFkKER1bW15KSBOb2RlMFxuICAgIFx0XHQ9PT09PT09PT09PT09PVxuICAgIFx0XHQqTm9uZSB8fCBQb2ludGVyMFwiXTo6OmdyZWVuXG4gICAgICAgIE5vZGUwW1wiTm9kZTFcbiAgICBcdFx0PT09PT09PT09PT09PT1cbiAgICBcdFx0RGF0YTEgfHwgUG9pbnRlcjFcIl1cbiAgICAgICAgTm9kZTFbXCJOb2RlMlxuICAgIFx0XHQ9PT09PT09PT09PT09PVxuICAgIFx0XHREYXRhMiB8fCBQb2ludGVyMlwiXVxuICAgICAgICBUYWlsW1wiVGFpbCBOb2RlM1xuICAgIFx0XHQ9PT09PT09PT09PT09PVxuICAgIFx0XHREYXRhMyB8fCAqTm9uZVwiXTo6OmdyZWVuXG4gICAgXG4gICAgXHRcdEhlYWQgLS0-IE5vZGUwIC0tPiBOb2RlMSAtLT4gVGFpbCIsIm1lcm1haWQiOnsidGhlbWUiOiJkZWZhdWx0In0sInVwZGF0ZUVkaXRvciI6ZmFsc2V9)](https://mermaid-js.github.io/docs/mermaid-live-editor-beta/#/edit/eyJjb2RlIjoiICAgICUle2luaXQ6IHtcImZsb3djaGFydFwiOiB7XCJodG1sTGFiZWxzXCI6IGZhbHNlfX0gfSUlXG4gICAgZmxvd2NoYXJ0IExSXG4gICAgY2xhc3NEZWYgZ3JlZW4gZmlsbDpncmF5LCBzdHJva2U6YmxhY2ssIHN0cm9rZS13aWR0aDoycHgsIGNvbG9yOndoaXRlXG4gICAgXG4gICAgICAgIEhlYWRbXCJIZWFkKER1bW15KSBOb2RlMFxuICAgIFx0XHQ9PT09PT09PT09PT09PVxuICAgIFx0XHQqTm9uZSB8fCBQb2ludGVyMFwiXTo6OmdyZWVuXG4gICAgICAgIE5vZGUwW1wiTm9kZTFcbiAgICBcdFx0PT09PT09PT09PT09PT1cbiAgICBcdFx0RGF0YTEgfHwgUG9pbnRlcjFcIl1cbiAgICAgICAgTm9kZTFbXCJOb2RlMlxuICAgIFx0XHQ9PT09PT09PT09PT09PVxuICAgIFx0XHREYXRhMiB8fCBQb2ludGVyMlwiXVxuICAgICAgICBUYWlsW1wiVGFpbCBOb2RlM1xuICAgIFx0XHQ9PT09PT09PT09PT09PVxuICAgIFx0XHREYXRhMyB8fCAqTm9uZVwiXTo6OmdyZWVuXG4gICAgXG4gICAgXHRcdEhlYWQgLS0-IE5vZGUwIC0tPiBOb2RlMSAtLT4gVGFpbCIsIm1lcm1haWQiOnsidGhlbWUiOiJkZWZhdWx0In0sInVwZGF0ZUVkaXRvciI6ZmFsc2V9)
    

<br>

- 노드 클래스의 경우 이전 포스팅과 동일하게 구현한다.
    
    ```python
    class Node:
        def __init__(self, item, next=None):
            self.data = item # 노드가 담고 있는 데이터 원소
            self.next = next # 다음 노드에 대한 정보를 담고 있는 포인터
    ```

  <br>
  연결 리스트의 경우에는 이전 포스팅과 달리, <u>더미 헤드 노드를 포함한 빈 연결리스트</u>로 구성하여 직접 노드의 탐색, 삽입, 삭제, 병합 등을 수행보고자 한다.

  ```python
  class LinkedList:
      def __init__(self):
          self.nodeCount = 0 # Dummy Head Node는 nodeCount에 포함시키지 않는다.
          self.head = Node(None) # 정수형 원소 대신 None값을 가진다
          self.tail = None # Dummy Head Node만 존재하기에 Tail은 정의되지 않았다.
          self.head.next = self.tail
  
      def traverse(self):
          """
          연결리스트의 노드 데이터 확인용 함수
          """
          result=[]
          curr = self.head
          while curr.next:
              curr = curr.next
              result.append(curr.data)
      
          return result
  ```

<br>

---

## <span style="color:navy">연결리스트 핵심 연산 with 더미 노드<span>

### <span style="color:navy">(1) N번째 특정 원소 탐색<span>

- 더미 헤드 노드가 주어졌을 경우, 연결리스트의 안의 노드 탐색을 위한 코드는 다음과 같다. 기존 더미 헤든 노드를 고려하지 않은 함수와 비교했을 때, <u>i=0부터 시작하는 것</u> 외에는 동일한 것을 확인할 수 있따.
    
    ```python
    class LinkedList:
        def __init__(self):...

        def traverse(self):...

        def getAt(self, pos):...
            """
            노드 탐색용 함수
            - pos: 탐색하고자하는 노드의 순서
            """
            if pos < 0 or pos > self.nodeCount:
                return None

            curr = self.head
            i = 0
            while i < pos:
                    curr = curr.next
                    i += 1
            return curr
    
    if __name__ == '__main__':
        linkedlist = LinkedList()
        print(linkedlist.getAt(0)) # None
    ```

<br>

### <span style="color:navy">(2) 원소의 삽입 (Insertion)<span>

- 연결리스트에서 더미 헤드 노드가 추가되었을 때, 원소의 삽입 과정은 다음의 절차를 따른다.<br>(***newNode***: 새로 삽입할 노드, ***prevNode***: ***newNode***앞에 위치할 노드, ***nextNode***: ***newNode***뒤에 위치할 노드, *삽입 이전 ***prev***뒤의 노드는 ***next***이다.)
    
    1. ***newNode*** 포인터에는 ***nextNode***의 ******주소를 할당한다.
       - [주의사항] ***prevNode***가 연결리스트의 ***Tail***노드에 해당할 경우 <br> (`if prevNode.next is None`)<br>
       1번 과정 수행 후, 연결리스트의 Tail노드를 newNode를 치환하는 과정을 수행해야한다.
    2. ***prevNode***의 포인터에는 ***newNode***주소를 할당한다.
    
    3. ***nodeCount***( # of Node)를 1 증가시킨다.

<br>

- 위의 원소 삽입 과정을 함수(`insertAfter`)로 표현하면 다음과 같다.
    
    ```python
    def insertAfter(self, prevNode, newNode):
        
        # 1번 과정 수행
        newNode.next = prevNode.next

        # 1번 과정의 주의사항 수행
        if prevNode.next is None:
            self.tail = newNode
    		
        # 2번 과정 수행
        prevNode.next = newNode
    
        self.nodeCount += 1
        return True
    ```
<br>

- 이때, 연결 리스트의 원소 삽입 위치를 고려해서 함수(`insertAt`)를 구현하면 다음과 같다.
    
    ```python
    class Node:...
    
    class LinkedList:
          def __init__(self):...
  
          def traverse(self):...
  
          def getAt(self, pos):...
  
          def insertAfter(self, prevNode, newNode):
              
              newNode = prevNode.next
              if prevNode == self.tail: 
                  self.tail = newNode
  
              prevNode.next = newNode
          
              self.nodeCount += 1
              return True
  
          def insertAt(self, pos, newNode):
              
              if pos < 1 or pos >= self.nodeCount+1:
                  return False
              
              # [Head] -> [newNode]와 같이
              # pos = 1을 인풋하여 "맨 끝"에 newNode를 삽입할 경우
              # 이때, newNode기준 Dummy Head Node을 
              # self.tail(즉, None)로 할당하는 것은 적합하지 않다.
              # (*시행할 경우 Tail앞에 newNode가 위치하기에 적합하지 않음)
              # 따라서, Head만 있는 빈 연결리스트의 pos=1위치에 삽입하는 조건에서 생략한다.
              if pos != 1 and pos == self.nodeCount+1:
                  prevNode = self.tail
              
              # "중간(or Head앞에)"에 newNode를 삽입할 경우
              else:
                  prevNode = self.getAt(pos-1)
              
              return self.insertAfter(prevNode, newNode)
    
    if __name__ == '__main__':
    
        linkedlist = LinkedList()
    
        N1 = Node(30)
        N2 = Node(40)
        N3 = Node(35)
        linkedlist.insertAt(1, N1)
        linkedlist.insertAt(2, N2)
        linkedlist.insertAt(2, N3)
    
        print(linkedlist.traverse()) # [30, 35, 40]
    ```
<br>

### <span style="color:navy">(3) 원소의 삭제 (Deletion)<span>

- 연결리스트에서 더미 헤드 노드가 추가되었을 때, 원소의 삭제 과정은 다음의 절차를 따른다.
    
    (***prevNode***: 삭제할 노드 앞에 위치한 노드, ***nextNode***: 삭제할 노드 뒤에 위치할 노드)
    
    1. ***prevNode***의 포인터에 ***nextNode***의 주소를 할당한다.
        - [주의사항] ***Tail***노드를 삭제할 경우 (즉, `nextNode == None`)<br>: ***Tail***노드 앞에 위치한 ***prevNode***를 ***Tail***노드로 조정한다.
    2. ***nodeCount***(# of Node)를 1 감소시킨다.

<br>

- 위의 원소 삭제 과정을 함수(`popAfter`)로 표현하면 다음과 같다.
    
    ```python
    def popAfter(self, prevNode):
    
        nextNode = prevNode.next.next
        pop_value = prevNode.next.data
    
        # 1번 과정 수행
        prevNode.next = nextNode
    		
        # 1번 주의사항
        if nextNode is None:
            self.tail = prevNode
    				
        # 2번 과정 수행
        self.nodeCount -= 1
    
        return pop_value
    ```
  
<br>
    
- 이때, 연결 리스트의 원소 삭제 위치를 고려해서 함수(`popAt`)를 구현하면 다음과 같다.
    
    ```python
    class Node:...
    
    class LinkedList:
        def __init__(self):...

        def traverse(self):...

        def getAt(self, pos):...

        def popAfter(self, prevNode):
        
            nextNode = prevNode.next.next
            pop_value = prevNode.next.data
        
            prevNode.next = nextNode		
  
            if nextNode is None:
                self.tail = prevNode
            
            self.nodeCount -= 1
            return pop_value
    		
        def popAt(self, pos):
    
            if pos < 1 or pos > self.nodeCount:
                raise IndexError
    
            prevNode = self.getAt(pos-1)
    
            return self.popAfter(prevNode)
    
    if __name__ == '__main__':
    
        linkedlist = LinkedList()
    
        N1 = Node(30)
        N2 = Node(40)
        N3 = Node(35)
        linkedlist.insertAt(1, N1)
        linkedlist.insertAt(2, N2)
        linkedlist.insertAt(2, N3)
    		
        print(linkedlist.popAt(2)) # 35
        print(linkedlist.traverse()) # [30, 40]
    ```
    
<br>

### <span style="color:navy">(4) 두 리스트 병합 (Concatenation)<span>

- 더미 헤드 노드가 포함된 연결리스트 사이의 병합 과정은 다음의 절차를 따른다.<br>(***L1***: 병합 시 앞에 위치한 연결리스트,  ***L2***: 병합 시 뒤에 위치한 연결리스트)
    
    1. ***L1.Tail***이 ***L2.Head***포인터에 할당된 노드를 바라보게 하기 위해서<br>***L2.Head***의 포인터에 할당된 주소를 ***L1.Tail***의 포인터에 할당한다.
    2. ***L1.Tail***을 ***L2.Tail***로 치환한다. <br>만약, 병합되는 연결리스트***L2***가 비어있을 경우 (즉, `L2.tail = None`)<br>과정을 수행하지 않는다. 
    3. 기존 ***L1***과 ***L2***의 ***nodeCount***를 합산해 병합된 ***L1***의 ***nodeCount***에 할당한다.

<br>

- 위의 과정을 클래스 내 함수(`concat`)로 구현하면 다음과 같다.
    
    ```python
    class Node:...
    
    class LinkedList:
          def __init__(self):...
  
          def traverse(self):...
  
          def getAt(self, pos):...
  
          def concat(self, L2):...
          """
          L2: 기존 연결리스트에 부착할 연결리스트 객체
          """
  
          # 1번 과정 수행
          self.tail.next = L2.head.next
  
          # 2번 과정 수행
          if L2.tail:
                  self.tail = L2.tail
  
          # 3번 과정 수행
          self.nodeCount += L2.nodeCount
    
  
    if __name__ == '__main__':
    
        L1 = LinkedList()
        L2 = LinkedList()
    
        L1_N1 = Node(30)
        L1_N2 = Node(40)
        L2_N1 = Node(50)
        L2_N2 = Node(60)
    
        L1.insertAt(1, L1_N1)
        L1.insertAt(2, L1_N2)
        L2.insertAt(1, L2_N1)
        L2.insertAt(2, L2_N2)
    
        L1.concat(L2)
        print(L1.traverse()) # [30, 40, 50, 60]
    ```

<br>

---


## <span style="color:navy">References<span>
- [Programmers, 어서와! 자료구조와 알고리즘은 처음이지?](https://school.programmers.co.kr/learn/courses/57/57-%EC%96%B4%EC%84%9C%EC%99%80-%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0%EC%99%80-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98%EC%9D%80-%EC%B2%98%EC%9D%8C%EC%9D%B4%EC%A7%80)
