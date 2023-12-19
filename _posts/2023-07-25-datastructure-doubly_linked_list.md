---
layout: post
title: "[자료구조] 7. 양방향 연결리스트 (Doubly Linked Lists)"
date: 2023-07-25 00:00:00 +0900
categories: [Data, Data Structure]
tags: [Data, Data Structure, Linked Lists, Doubly Linked Lists]
comments: true
math: true
mermaid: true
---

## <span style="color:navy">양방향 연결 리스트 (Doubly Linked Lists)<span>

- 지금까지 다루었던 연결 리스트의 경우에는 앞에서 뒤로만 진행이 가능하였다. <br>하지만, `양방향 연결 리스트`의 경우 앞으로도 뒤로도 진행이 가능하다.<br>즉, 단방향이 아니라 <u>양방향으로 진행이 가능</u>하다. 
  
  [![](https://mermaid.ink/img/eyJjb2RlIjoiICAgICUle2luaXQ6IHtcImZsb3djaGFydFwiOiB7XCJodG1sTGFiZWxzXCI6IGZhbHNlfX0gfSUlXG4gICAgZmxvd2NoYXJ0IExSXG4gICAgICAgIEhlYWRbUHJldl1cbiAgICAgICAgTm9kZTBbXCJOb2RlXG4gICAgXHRcdD09PT09PT09PT09PT09PT09PT09PT09PT09PT09PVxuICAgIFx0XHRQcmV2X1BvaW50ZXIgfHwgRGF0YSB8fCBOZXh0X1BvaW50ZXJcIl1cbiAgICAgICAgVGFpbFtOZXh0XSAgICBcbiAgICBcbiAgICBcdFx0SGVhZCAtLT4gTm9kZTAgLS0-IFRhaWxcbiAgICBcdFx0VGFpbCAtLT4gTm9kZTAgLS0-IEhlYWQiLCJtZXJtYWlkIjp7InRoZW1lIjoiZGVmYXVsdCJ9LCJ1cGRhdGVFZGl0b3IiOmZhbHNlfQ)](https://mermaid-js.github.io/docs/deprecated-editor/#/edit/eyJjb2RlIjoiICAgICUle2luaXQ6IHtcImZsb3djaGFydFwiOiB7XCJodG1sTGFiZWxzXCI6IGZhbHNlfX0gfSUlXG4gICAgZmxvd2NoYXJ0IExSXG4gICAgICAgIEhlYWRbUHJldl1cbiAgICAgICAgTm9kZTBbXCJOb2RlXG4gICAgXHRcdD09PT09PT09PT09PT09PT09PT09PT09PT09PT09PVxuICAgIFx0XHRQcmV2X1BvaW50ZXIgfHwgRGF0YSB8fCBOZXh0X1BvaW50ZXJcIl1cbiAgICAgICAgVGFpbFtOZXh0XSAgICBcbiAgICBcbiAgICBcdFx0SGVhZCAtLT4gTm9kZTAgLS0-IFRhaWxcbiAgICBcdFx0VGFpbCAtLT4gTm9kZTAgLS0-IEhlYWQiLCJtZXJtYWlkIjp7InRoZW1lIjoiZGVmYXVsdCJ9LCJ1cGRhdGVFZGl0b3IiOmZhbHNlfQ)

<br>

- 양방향 연결 리스트는 이전 연결 리스트와 달리 양방향으로 진행이 가능하기 때문에 이전 Node와 달리 <u>이전</u>(***prev***)<u> 노드에 대한 주소를 할당하는 포인터를 추가할 필요</u>가 있다. 따라서, 기존 Node클래스에 `self.prev`<u>인스턴스 변수를 추가</u>한다.
    
    ```python
    class Node:
        def __init__(self, item):
            self.data = item # 노드가 담고 있는 데이터 원소
            self.prev = None # *이전 노드에 대한 정보를 담고 있는 포인터
            self.next = None # 다음 노드에 대한 정보를 담고 있는 포인터
    ```
<br>    

- 양방향 연결 리스트의 경우 <u>Head외에도</u> `더미 테일 노드(Dummy Tail Node)`를 배치시켜 모든 방향에서의 비효율성 문제를 극복할 수 있다. 더불어, <u>더미 노드외의 유효한 노드들의 경우 전부 같은 일관된 형태</u>를 가져 코드를 구성하는데 있어서 편의성이 도모된다. 예를 들어, Head 더미 노드만 있었을 경우에는 <u>Tail의 조정이 필요한 경우가 있었지만, Tail 더미 노드가 추가된 지금에는 Tail 더미 노드에 대한 조정 및 예외 처리 등에 관한 피로도가 이전보다 낮아졌다</u>.
    
  [![](https://mermaid.ink/img/eyJjb2RlIjoiICAgICUle2luaXQ6IHtcImZsb3djaGFydFwiOiB7XCJodG1sTGFiZWxzXCI6IGZhbHNlfX0gfSUlXG4gICAgXG4gICAgZmxvd2NoYXJ0IExSXG4gICAgY2xhc3NEZWYgZ3JlZW4gZmlsbDpncmF5LCBzdHJva2U6YmxhY2ssIHN0cm9rZS13aWR0aDoycHgsIGNvbG9yOndoaXRlXG4gICAgXG4gICAgICAgIEhlYWRbXCJIZWFkKER1bW15KSBOb2RlXG4gICAgXHRcdD09PT09PT09PT09PT09PT09PT09PT09XG4gICAgXHRcdCpOb25lIHx8ICpOb25lIHx8IE5leHRfUG9pbnRlclwiXTo6OmdyZWVuICAgIFxuICAgICAgICBOb2RlMFtcIk5vZGVcbiAgICBcdFx0PT09PT09PT09PT09PT09PT09PT09PT09PT09XG4gICAgXHRcdFByZXZfUG9pbnRlciB8fCBEYXRhIHx8IE5leHRfUG9pbnRlclwiXVxuICAgICAgICBOb2RlMVtcIi4uLlwiXVxuICAgICAgICBUYWlsW1wiVGFpbChEdW1teSkgTm9kZVxuICAgIFx0XHQ9PT09PT09PT09PT09PT09PT09PT09PVxuICAgIFx0XHRQcmV2X1BvaW50ZXIgfHwgKk5vbmUgfHwgKk5vbmVcIl06OjpncmVlblxuICAgIFxuICAgIFx0XHRIZWFkIC0tPiBOb2RlMCAtLT4gTm9kZTEgLS0-IFRhaWxcbiAgICBcdFx0VGFpbCAtLT4gTm9kZTEgLS0-IE5vZGUwIC0tPiBIZWFkIiwibWVybWFpZCI6eyJ0aGVtZSI6ImRlZmF1bHQifSwidXBkYXRlRWRpdG9yIjpmYWxzZX0)](https://mermaid-js.github.io/docs/deprecated-editor/#/edit/eyJjb2RlIjoiICAgICUle2luaXQ6IHtcImZsb3djaGFydFwiOiB7XCJodG1sTGFiZWxzXCI6IGZhbHNlfX0gfSUlXG4gICAgXG4gICAgZmxvd2NoYXJ0IExSXG4gICAgY2xhc3NEZWYgZ3JlZW4gZmlsbDpncmF5LCBzdHJva2U6YmxhY2ssIHN0cm9rZS13aWR0aDoycHgsIGNvbG9yOndoaXRlXG4gICAgXG4gICAgICAgIEhlYWRbXCJIZWFkKER1bW15KSBOb2RlXG4gICAgXHRcdD09PT09PT09PT09PT09PT09PT09PT09XG4gICAgXHRcdCpOb25lIHx8ICpOb25lIHx8IE5leHRfUG9pbnRlclwiXTo6OmdyZWVuICAgIFxuICAgICAgICBOb2RlMFtcIk5vZGVcbiAgICBcdFx0PT09PT09PT09PT09PT09PT09PT09PT09PT09XG4gICAgXHRcdFByZXZfUG9pbnRlciB8fCBEYXRhIHx8IE5leHRfUG9pbnRlclwiXVxuICAgICAgICBOb2RlMVtcIi4uLlwiXVxuICAgICAgICBUYWlsW1wiVGFpbChEdW1teSkgTm9kZVxuICAgIFx0XHQ9PT09PT09PT09PT09PT09PT09PT09PVxuICAgIFx0XHRQcmV2X1BvaW50ZXIgfHwgKk5vbmUgfHwgKk5vbmVcIl06OjpncmVlblxuICAgIFxuICAgIFx0XHRIZWFkIC0tPiBOb2RlMCAtLT4gTm9kZTEgLS0-IFRhaWxcbiAgICBcdFx0VGFpbCAtLT4gTm9kZTEgLS0-IE5vZGUwIC0tPiBIZWFkIiwibWVybWFpZCI6eyJ0aGVtZSI6ImRlZmF1bHQifSwidXBkYXRlRWRpdG9yIjpmYWxzZX0)
  
  <br>  
  따라서, 빈 양방향 연결 리스트의 경우에는 다음과 같이 클래스를 정의할 수 있다.
    
    ```python
    from .node import Node
    
    class DoublyLinkedList:
        def __init__(self, item):
            self.nodeCount = 0
            self.head = Node(None)
            self.tail = Node(None)
            # Head노드의 포인터 정의
            self.head.prev = None
            self.head.next = self.tail
            # Tail노드의 포인터 정의
            self.tail.prev = self.head
            self.tail.next = None
        
        def traverse(self):
            """
            (Head -> Tail방향) 연결리스트의 노드 데이터 확인용 함수
            """
            result = []
            # 순회 시작점: Tail노드
            curr = self.head
            # curr.next로 조건 생성 시 Tail Dummy Node의 값도 반환됨
            # 더불어, 빈 리스트의 순회도 가능하게함
            while curr.next.next
                    curr = curr.next
                    result.append(curr.data)

            return result
        
        def reverse(self):
            """
            (Tail -> Head방향) 연결리스트의 노드 데이터 확인용 함수
            """
            result = []
            # 순회 시작점: Tail노드
            curr = self.tail
            # curr.next로 조건 생성 시 Tail Dummy Node의 값도 반환됨
            # 더불어, 빈 리스트의 순회도 가능하게함
            while curr.next.next
                    curr = curr.next
                    result.append(curr.data)

            return result
          
    				
    ```

  [![](https://mermaid.ink/img/eyJjb2RlIjoiICAgICUle2luaXQ6IHtcImZsb3djaGFydFwiOiB7XCJodG1sTGFiZWxzXCI6IGZhbHNlfX0gfSUlXG4gICAgXG4gICAgZmxvd2NoYXJ0IExSXG4gICAgY2xhc3NEZWYgZ3JlZW4gZmlsbDpncmF5LCBzdHJva2U6YmxhY2ssIHN0cm9rZS13aWR0aDoycHgsIGNvbG9yOndoaXRlXG4gICAgXG4gICAgICAgIEhlYWRbXCJIZWFkKER1bW15KSBOb2RlXG4gICAgXHRcdD09PT09PT09PT09PT09PT09PT09PT09XG4gICAgXHRcdCpOb25lIHx8ICpOb25lIHx8IE5leHRfUG9pbnRlclwiXTo6OmdyZWVuICAgIFxuICAgICAgICBUYWlsW1wiVGFpbChEdW1teSkgTm9kZVxuICAgIFx0XHQ9PT09PT09PT09PT09PT09PT09PT09PVxuICAgIFx0XHRQcmV2X1BvaW50ZXIgfHwgKk5vbmUgfHwgKk5vbmVcIl06OjpncmVlblxuICAgIFxuICAgIFx0XHRIZWFkIC0tPiBUYWlsXG4gICAgXHRcdFRhaWwgLS0-IEhlYWQiLCJtZXJtYWlkIjp7InRoZW1lIjoiZGVmYXVsdCJ9LCJ1cGRhdGVFZGl0b3IiOmZhbHNlfQ)](https://mermaid-js.github.io/docs/deprecated-editor/#/edit/eyJjb2RlIjoiICAgICUle2luaXQ6IHtcImZsb3djaGFydFwiOiB7XCJodG1sTGFiZWxzXCI6IGZhbHNlfX0gfSUlXG4gICAgXG4gICAgZmxvd2NoYXJ0IExSXG4gICAgY2xhc3NEZWYgZ3JlZW4gZmlsbDpncmF5LCBzdHJva2U6YmxhY2ssIHN0cm9rZS13aWR0aDoycHgsIGNvbG9yOndoaXRlXG4gICAgXG4gICAgICAgIEhlYWRbXCJIZWFkKER1bW15KSBOb2RlXG4gICAgXHRcdD09PT09PT09PT09PT09PT09PT09PT09XG4gICAgXHRcdCpOb25lIHx8ICpOb25lIHx8IE5leHRfUG9pbnRlclwiXTo6OmdyZWVuICAgIFxuICAgICAgICBUYWlsW1wiVGFpbChEdW1teSkgTm9kZVxuICAgIFx0XHQ9PT09PT09PT09PT09PT09PT09PT09PVxuICAgIFx0XHRQcmV2X1BvaW50ZXIgfHwgKk5vbmUgfHwgKk5vbmVcIl06OjpncmVlblxuICAgIFxuICAgIFx0XHRIZWFkIC0tPiBUYWlsXG4gICAgXHRcdFRhaWwgLS0-IEhlYWQiLCJtZXJtYWlkIjp7InRoZW1lIjoiZGVmYXVsdCJ9LCJ1cGRhdGVFZGl0b3IiOmZhbHNlfQ)
    
<br>

---

## <span style="color:navy">양방향 연결 리스트 핵심 연산<span>

### <span style="color:navy">(1) 리스트 순회 (Traversal/Reverse)<span>

- 양방향 연결 리스트의 순회 연산은 Head뿐만 아니라 Tail에서도 시작이 가능하다.
    
    ```python
    class Node:...
    
    class DoublyLinkedList:
        def __init__(self, item):...
        
        def traverse(self):
            """
            (Head -> Tail방향) 연결리스트의 노드 데이터 확인용 함수
            """
            result = []
            # 순회 시작점: Tail노드
            curr = self.head
            # curr.next로 조건 생성 시 Tail Dummy Node의 값도 반환됨
            # 더불어, 빈 리스트의 순회도 가능하게함
            while curr.next.next
                curr = curr.next
                result.append(curr.data)

            return result
        
        def reverse(self):
            """
            (Tail -> Head방향) 연결리스트의 노드 데이터 확인용 함수
            """
            result = []
            # 순회 시작점: Tail노드
            curr = self.tail
            # curr.tail로 조건 생성 시 Tail Dummy Node의 값도 반환됨
            # 더불어, 빈 리스트의 순회도 가능하게함
            while curr.tail.tail
                curr = curr.tail
                result.append(curr.data)

            return result
    ```
    
<br>

### <span style="color:navy">(2) N번째 특정 원소 탐색<span>

- 기존 탐색 함수의 경우 앞에서 부터 선형 탐색 과정이 이루어졌다. 이는 연결리스트 내에 위치한 노드의 수가 많아질수록 비효율적이다. 하지만, Head노드와 동일한 생김새를 가진 Tail노드를 고려했을 때, 위의 Reverse 순회 처럼 <u>Tail부터 탐색이 진행되도록 구성될 수 있다</u>. 하지만, 알고리즘의 타입 자체는 앞에서 시작하나 뒤에서 시작하나 선형 시간 알고리즘에 해당하는 것은 동일하다.
    
    ```python
    from .node import Node
    
    class DoublyLinkedList:
        def __init__(self, item):...

        def traverse(self):...

        def reverse(self):...

        def getAt(self, pos):...
            """
            노드 탐색용 함수
            - pos: 탐색하고자하는 노드의 순서
            """
            if pos < 0 or pos > self.nodeCount:
                return None
            
            i = 0				

            # 탐색하는 위치가 중간보다 뒤일 때, Tail부터 탐색 시작
            if pos > self.nodeCount//2:
                curr = self.tail
                while i < self.nodeCount-pos+1:
                    curr = curr.prev
                    i +=1
            
            # 탐색하는 위치가 중간보다 앞일 때, Head부터 탐색 시작 
            else:	
                curr = self.head
                while i < pos:
                        curr = curr.next
                        i += 1
                    
            return curr
  
    if __name__ == '__main__':
        linkedlist = LinkedList()
        print(linkedlist.getAt(0)) # None
    ```
    
<br>

### <span style="color:navy">(3) 원소 삽입 (Insertion)<span>

- 양방향 연결리스트에서 원소의 삽입 과정은 다음의 절차를 따른다.<br>(***newNode***: 새로 삽입할 노드, ***prevNode***: ***newNode*** 앞에 위치할 노드, ***nextNode***: ***newNode***뒤에 위치할 노드,  *삽입 이전 ***prev***뒤의 노드는 ***next***이다.)
    

  1. ***newNode***의 ***prev***와 ***next*** 포인터에 각각 ***prevNode***와  ***nextNode***의 주소를 할당한다.
     - ***Tail***노드가 유효했을 경우 수행했던 예외 처리를 수행할 필요가 없어졌다. 
     - 즉, 단방향 연결 리스트보다 코드가 단순해졌다.

  2. ***prevNode***의 ***next***포인터에 ***newNode***의 주소를 할당한다.<br>
     ***nextNode***의 ***prev***포인터에 ***newNode***의 주소를 할당한다.
    
  3. ***nodeCount***(# of Node)를 1 증가시킨다.

<br>

- 위의 원소 삽입 과정을 함수(`insertAfter`)로 표현하면 다음과 같다.
    
    ```python
    def insertAfter(self, prevNode, newNode):
    
        nextNode = prevNode.next

        # 1번 과정 수행/
        newNode.prev = prevNode
        newNode.next = nextNode
        
        # 2번 과정 수행
        prevNode.next = newNode
        nextNode.prev = newNode

        # 3번 과정 수행
        self.nodeCount += 1

        return True
    ```
<br>

- 원소의 삽입 위치를 고려했을 떄 함수(`insertAt`)는 다음과 같다.
    
    ```python
    class Node:...
    
    class DoublyLinkedList:
        def __init__(self, item):...

        def traverse(self):...

        def reverse(self):...

        def getAt(self, pos):...
        
        def insertAfter(self, prevNode, newNode):

            nextNode = prevNode.next
            
            newNode.prev = prevNode
            newNode.next = nextNode
            
            prevNode.next = newNode
            nextNode.prev = newNode
            
            self.nodeCount += 1				
            return True
                
        def insertAt(self, pos, newNode):
                
            if pos <1 or pos > self.nodeCount+1:
                return False
            
            # 기존 Head Dummy Node와 함께 Tail Dummy Node가 추가되어
            # 단방향 연결리스트에서 고려된 예외 처리를 수행할 필요가 없어졌다.
            prevNode = self.getAt(pos).prev
            return self.insertAfter(prevNode, newNode)
    ```
<br>

### <span style="color:navy">(4) 원소의 삭제 (Deletion)<span>

- 양방향 연결리스트에서 원소의 삭제 과정은 다음의 절차를 따른다.<br>(***prevNode***: 삭제할 노드 앞에 위치한 노드, ***nextNode***: 삭제할 노드 뒤에 위치할 노드)
    
    1. ***prevNode***의 ***next***포인터에 ***nextNode***의 주소를 할당한다.
       - ***Tail***노드가 유효했을 경우 수행했던 예외 처리를 수행할 필요가 없어졌다.
       - 즉, 단방향 연결 리스트보다 코드가 단순해졌다.
    
    2. ***nextNode***의 ***prev***포인터에 ***prevNode***의 주소를 할당한다.
    
    3. ***nodeCount***(# of Node)를 1 감소시킨다.

<br>

- 위의 원소 삭제 과정을 함수(`popAfter`)로 표현하면 다음과 같다.
    
    ```python
    def popAfter(self, prevNode):
    		
        pop_value = prevNode.next.data
        nextNode = prevNode.next.next
    
        # 1번 과정 수행
        prevNode.next = nextNode
    
        # 2번 과정 수행
        nextNode.prev = prevNode
    
        # 2번 과정 수행
        self.nodeCount-=1
    
        return pop_value
    ```
<br>

- 이때, 연결리스트의 원소 삭제 위치를 고려해서 함수(`popAt`)을 구현하면 다음과 같다.
    
    ```python
    class Node:...
    
    class DoublyLinkedList:
        def __init__(self, item):...

        def traverse(self):...

        def reverse(self):...

        def getAt(self, pos):...
        
        def popAfter(self, prevNode):
                
            pop_value = prevNode.next.data
            nextNode = prevNode.next.next
        
            prevNode.next = nextNode
            nextNode.prev = prevNode

            self.nodeCount-=1		
            return pop_value
                
        def popAt(self, pos):
  
            if pos <= 0 or pos >= self.nodeCount+1:
                raise IndexError
    
            prevNode = self.getAt(pos).prev
    
            return self.popAfter(prevNode)
    ```

<br>

### <span style="color:navy">(5) 두 리스트 병합 (Concatenation)<span>

- 양방향 연결 리스트의 경우 단방향 연결 리스트와 달리 더미 테일 노드가 추가되면서 병합되는 <u>두 연결리스트가 모두 비어있는지 혹은 둘 중 하나만 비어있는지 고려</u>할 필요가 있다.<br>
  (***L1***: 병합 시 앞에 위치한 연결리스트,  ***L2***: 병합 시 뒤에 위치한 연결리스트)
    
    - ***L1, L2***모두 비어있거나, ***L2***만 비어있는 경우: 병합 과정을 패스한다.
    
    - ***L1***만 비어있는 경우: ***L1***의 ***Head***와 ***Tail***을 각각 ***L2***의 ***Head***와 ***Tail***로 교체한다.
    
    - ***L1, L2***가 모두 비어있지 않는 경우
      1. ***L1.Tail*** 앞에 위치한 노드를 ***L2.Head*** 뒤에 위치한 노드의 ***prev***포인터에 할당한다.
      2. ***L2.Head*** 앞에 위치한 노드를 ***L1.Tail*** 앞에 위치한 노드의 ***next***포인터에 할당한다.

<br>
    
- 위의 과정을 클래스 내 함수(`concat`)로 구현하면 다음과 같다.
    
    ```python
    class Node:...
    
    class DoublyLinkedList:
        def __init__(self, item):...

        def traverse(self):...

        def reverse(self):...

        def getAt(self, pos):...
        
        def concat(self, L2):
            """
            L2: 기존 연결리스트에 부착할 연결리스트 객체
            """
            # ***L1, L2***모두 비어있거나, ***L2***만 비어있는 경우
            if L2.nodeCount == 0 or (self.nodeCount+L2.nodeCount==0):
                pass
                    
            # ***L1***만 비어있는 경우
            elif self.nodeCount == 0:
                self.head = L2.head
                self.tail = L2.tail
                    
            # ***L1, L2***가 모두 비어있지 않는 경우
            else:
                L1_Node = self.tail.prev
                L2_Node = L2.head.next
                L1_Node.next = L2_Node
                L2_Node.prev = L1_Node
                self.tail = L.tail
    
            self.nodeCount += L2.nodeCount
    
            return True
    ```

<br>

---


## <span style="color:navy">References<span>
- [Programmers, 어서와! 자료구조와 알고리즘은 처음이지?](https://school.programmers.co.kr/learn/courses/57/57-%EC%96%B4%EC%84%9C%EC%99%80-%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0%EC%99%80-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98%EC%9D%80-%EC%B2%98%EC%9D%8C%EC%9D%B4%EC%A7%80)
