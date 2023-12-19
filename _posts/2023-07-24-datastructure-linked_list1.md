---
layout: post
title: "[자료구조] 5.연결 리스트-1 (Linked Lists)"
date: 2023-07-24 00:00:00 +0900
categories: [Data, Data Structure]
tags: [Data, Data Structure, ADT, Linked Lists]
comments: true
math: true
mermaid: true
---

## <span style="color:navy">추상적 자료 구조 (ADT)<span>

- `추상적 자료 구조(Abstract Data Type, ADT)`는 숫자형, 문자열, 리스트, 튜플, 셋, 딕셔너리 등의 **자료 구조**와 해당 자료 구조에서 일어날 수 있는 삽입, 삭제, 순회, 정렬, 탐색 등의 **연산들의 집합**을 **정의**한 것이다.
- 즉, <u>자료가 어떤 형태의 데이터 타입으로 저장되며, 해당 데이터에 필요한 작업은 어떤 것인지 정의만 하는 것</u>이 추상적 자료 구조이다.

<br>

---

## <span style="color:navy">연결 리스트 (Linked Lists)<span>

- `연결 리스트(Linked List)`는 데이터 원소들을 순서 지어 늘어놓는다는 점에 있어서는 `선형 배열(Linear Array)`과 비슷한 면이 있다. <u>하지만, 데이터 원소들을 늘어놓는 방식에 있어서 두 방법은 차이</u>가 있다.
    - `선형 배열`에 속하는 Python의 리스트(List)의 경우 실제 메모리 공간에 모든 원소를 연속적으로 <u>번호가 붙여진 칸에 원소들을 채워넣는 방식</u>이다.
    - 이와 달리, `연결 리스트`의 경우에는 각 데이터 원소는 다음 원소를 가리키는 `포인터(Pointer)`를 사용하여 데이터 원소의 순서를 정해 <u>각 원소들을 줄줄이(?) 엮어 관리하는 방식</u>이다.

- 선형 배열처럼 원소의 인덱스를 밀고 당기는 작업이 필요한 반면, 포인터를 가지고 있는 연결 리스트는 <u>삽입과 삭제가 유연</u>하다. 왜냐하면, <u>선형 배열에서의 작업과 동일한 작업 수행 시 데이터 원소의 포인터만 변경</u>하면 되기 때문에 더 효율적이다.

- 하지만, 원소를 탐색하는데 있어서, 인덱스 번호만 특정해주면 되는 선형 배열 \\(O(1)\\) 과 달리, <br>원소를 탐색하기 위해서는 <u>첫번째 Node의 데이터 원소부터 선형 탐색</u> \\(O(n)\\) 을 수행할 필요가 있다는 **단점**이 있다.

<br>

- 연결리스트 클래스를 다음과 같이 정의하여 해당 포스팅을 진행한다.
    
    ```python
    class LinkedList:
        def __init__(self, node_count, head_N=None, tail_N=None):
            self.nodeCount = node_count # 연결리스트 노드 수
            self.head = head_N # 연결리스트의 Head노드 클래스 객체
            self.tail = tail_N # 연결리스트의 Tail노드 클래스 객체
    ```

<br>

---

## <span style="color:navy">연결 리스트의 구성요소<span>

- 연결리스트는 **데이터 원소**와 **포인터**로 구성된 여러 개의 `노드(Node)`로 구성되어 있다.
    - 이때, 포인터는 순차적 노드 탐색을 수행하기 위해 다른 노드의 주소를 저장하고 있다.

- Node 중 <u>가장 앞에 위치한 노드</u>를 `헤드노드(Head Node)`, <u>가장 마지막에 위치한 노드</u>를 `테일 노드(Tail Node)`라고 칭한다.
  
  [![](https://mermaid.ink/img/eyJjb2RlIjoiICAgICUle2luaXQ6IHtcImZsb3djaGFydFwiOiB7XCJodG1sTGFiZWxzXCI6IGZhbHNlfX0gfSUlXG4gICAgZmxvd2NoYXJ0IExSXG4gICAgICAgIEhlYWRbXCLjhaQgSGVhZCBOb2RlMVxuICAgIFx0XHQ9PT09PT09PT09PT09XG4gICAgXHRcdERhdGExIHx8IFBvaW50ZXIxXCJdXG4gICAgICAgIE5vZGUyW1wi44Wk44WkICBOb2RlMlxuICAgIFx0XHQ9PT09PT09PT09PT09XG4gICAgXHRcdERhdGEyIHx8IFBvaW50ZXIyXCJdXG4gICAgICAgIFRhaWxbXCLjhaRUYWlsIE5vZGUzXG4gICAgXHRcdD09PT09PT09PT09XG4gICAgXHRcdERhdGEzIHx8ICpOb25lXCJdICAgIFxuICAgIFxuICAgIFx0XHRIZWFkIC0tPiBOb2RlMi0tPiBUYWlsXG4gICAgIiwibWVybWFpZCI6eyJ0aGVtZSI6ImRlZmF1bHQifSwidXBkYXRlRWRpdG9yIjpmYWxzZX0)](https://mermaid-js.github.io/docs/mermaid-live-editor-beta/#/edit/eyJjb2RlIjoiICAgICUle2luaXQ6IHtcImZsb3djaGFydFwiOiB7XCJodG1sTGFiZWxzXCI6IGZhbHNlfX0gfSUlXG4gICAgZmxvd2NoYXJ0IExSXG4gICAgICAgIEhlYWRbXCLjhaQgSGVhZCBOb2RlMVxuICAgIFx0XHQ9PT09PT09PT09PT09XG4gICAgXHRcdERhdGExIHx8IFBvaW50ZXIxXCJdXG4gICAgICAgIE5vZGUyW1wi44Wk44WkICBOb2RlMlxuICAgIFx0XHQ9PT09PT09PT09PT09XG4gICAgXHRcdERhdGEyIHx8IFBvaW50ZXIyXCJdXG4gICAgICAgIFRhaWxbXCLjhaRUYWlsIE5vZGUzXG4gICAgXHRcdD09PT09PT09PT09XG4gICAgXHRcdERhdGEzIHx8ICpOb25lXCJdICAgIFxuICAgIFxuICAgIFx0XHRIZWFkIC0tPiBOb2RlMi0tPiBUYWlsXG4gICAgIiwibWVybWFpZCI6eyJ0aGVtZSI6ImRlZmF1bHQifSwidXBkYXRlRWRpdG9yIjpmYWxzZX0)
    
<br>

- 노드 클래스는 다음과 같이 구현한다.
    
    ```python
    class Node:
        def __init__(self, item, next=None):
            self.data = item # 노드가 담고 있는 데이터 원소
            self.next = next # 다음 노드에 대한 정보를 담고 있는 포인터
    ```

<br>

---

## <span style="color:navy">연결리스트 핵심 연산<span>

### <span style="color:navy">(1) N번째 특정 원소 탐색<span>

- 연결리스트에서 <u>특정 원소를 탐색하기 위해서는 Head노드부터 순차적</u>으로 나아가야한다. <br>
  <u>Head노드의 순서를 1</u>로 명시했을 때, 연결리스트 안의 노드 탐색을 위한 코드는 다음과 같다.
    
    ```python
    class Node:
        def __init__(self, item, next=None):
            self.data = item # 노드가 담고 있는 데이터 원소
            self.next = next # 다음 노드에 대한 정보를 담고 있는 포인터
    
    class LinkedList:
        def __init__(self, node_count, head_N=None, tail_N=None):
            self.nodeCount = node_count
            self.head = head_N
            self.tail = tail_N
    
        def getAt(self, pos):
            """
            노드 탐색용 함수
            - pos: 탐색하고자하는 노드의 순서
            """
    	
            # 탐색하고자하는 Node가 연결리스트내에 위치하는가?
            if pos < 1 or pos > self.nodeCount:
                return None
    
            # Head노드의 순서를 1로 초기화한다.
            i = 1
            
            # Head노드부터 시작하여 pos에 위치한 노드를 탐색한다.
            curr = self.head
            while i < pos:
                curr = curr.next
                i += 1
            return curr
    				
        def traverse(self):
            """
            연결리스트의 노드 데이터 확인용 함수
            """
            iters = self.nodeCount
            answer = []
  
            for iter in range(1,iters+1):
                curr = self.getAt(iter)
                answer.append(curr.data)
                iter+=1
      
            return answer
    
    if __name__ == '__main__':
        # 노드 객체를 생성한다.
        N1 = Node(43)
        N2 = Node(85)
        N3 = Node(62)
    
        # 노드를 연결시키고, 연결리스트 객체를 생성한다.
        N1.next = N2
        N2.next = N3
        linkdlist = LinkedList(3, N1, N2)
    
        # 생성된 연결리스트의 노드 순서대로 데이터 원소를 출력한다.
        print(linkdlist.traverse) # [43, 85, 62]
    ```

<br>

### <span style="color:navy">(2) 원소의 삽입 (Insertion)<span>

- 연결리스트에서 원소의 삽입 과정은 다음의 절차를 따른다.<br>
  (***pos***: 새로 삽입하고자 하는 Node의 위치 순서)

  1. ***prev***(*pos-1*번째 Node)가 포인터가 가리키고 있던 ***origin***(기존 *pos*번째 노드) 주소를<br>
     ***new***(새로운 *pos*번째 노드)의 포인터에 저장한다.
  2. ***prev***가 ***new***를 가리키도록 포인터를 조정한다.
  3. ***nodeCount***(# of Node)를 1 증가 킨다.

<br>

- 위의 과정을 클래스 내 함수(`insertAt`)로 정의하면 다음과 같다.
    
     ```python
     # 위의 설명에서는 "주소"를 명시했지만, 
     # Python코드 기준 이를 "클래스 객체"라고 재명시하겠습니다.
    
     def insertAt(self, pos, newNode):
         # prev노드 클래스 객체를 불러온다.
         prev = self.get(pos-1) 
         # (1) prev노드의 포인터(next)에 저장된 origin노드의 클래스 객체를 저장한다.	
         newNode.next = prev.next  
         # (2) prev노드의 포인터가 new노드의 클래스 객체를 바라보도록 한다.
         prev.next = newNode
         # (3) nodeCount를 1증가시킨다.
         self.nodeCount += 1
     ```

<br>

- 이때, 연결 리스트의 <u>원소 삽입 위치에 따라서 복잡도가 상이</u>하다.
    - **중간**
        - 위에서 제시된 과정을 따르면 된다.
        - 이때 복잡도는 `선형탐색` 과정이 요구되기 때문에 `선형시간` \\(O(n)\\)에 해당한다.
    - **맨 앞**
        - Head의 조정만 요구되며, prev의 조정은 필요없다.
        - 즉, new노드의 포인터에 기존 Head노드의  주소를 할당해야한다.
        - 이때 복잡도는 앞 부분만 고려하면 되기에 `상수시간` \\(O(1)\\)에 해당한다.
    - **맨 끝**
        - Tail의 조정만 요구된다.
        - 즉, 기존 tail노드의 포인터에 new노드의 주소를 할당해야한다.
        - 이때 복잡도는 끝 부분만 고려하면 되기에 `상수시간` \\(O(1)\\)에 해당한다.

<br>

- 마지막으로, 전체적인 원소 삽입 과정을 함수(`insertAt`)로 표현하면 다음과 같을 것이다.
    
    ```python
    
    class Node:...
    
    class LinkedList:
        def __init__(self, node_count, head_N=None, tail_N=None):...
    
        def getAt(self, pos):...
    		
        def traverse(self):...		

        def insertAt(self, pos, newNode):
        
            # 삽입 위치가 적절한지 체크
            if pos < 1 or pos > self.nodeCount+1: 
                return False	
            
            # "맨 앞"에 new노드를 삽입할 경우
            if pos == 1:
                newNode.next = self.head
                self.head = newNode
            
            else:
        
                # "맨 끝"에 new노드를 삽입할 경우
                if pos == self.nodeCount+1:
                    prev = self.tail
                    newNode.next = prev.next # new노드의 포인터 연결
                    prev.next = newNode # prev노드의 포인터(New) 연결
                    self.tail = newNode	# tail노드에 new노드 할당
            
                # "중간"에 new노드를 삽입할 경우
                else:
                    prev = self.getAt(pos-1) 		
                    newNode.next = prev.next # new노드의 포인터 연결
                    prev.next = newNode # prev노드의 포인터(New) 연결
                        
            # nodeCount를 1증가시킨다.
            self.nodeCount += 1
            return True
    
    if __name__ == '__main__':
        # 노드 객체를 생성한다.
        N1 = Node(43)
        N2 = Node(85)
        N3 = Node(62)
        N_new = Node(99) # 정수 99를 가진 새로운 노드 생성
    
        # 노드를 연결시키고, 연결리스트 객체를 생성한다.
        N1.next = N2
        N2.next = N3
        linkdlist = LinkedList(3, N1, N2)
    
        # 생성된 연결리스트 객체에 N_new노드 추가
        linkdlist.insertAt(2, N_new)
    
        print(linkdlist.traverse()) # [43, 99, 85, 62]
    ```

<br>

### <span style="color:navy">(3) 원소의 삭제 (Deletion)<span>

- 연결리스트에서 원소의 삭제 과정은 다음의 절차를 따른다.<br>
  (***pos***: 삭제하고자 하는 Node의 위치 순서)
  1. ***prev***(*pos-1*번째 Node)가 포인터가 가리키던 주소를 ***del***(삭제할 Node)의 포인터에 저장한다.
  2. ***nodeCount***(# of Node)를 1 감소시킨다.

<br>

- 이때, 연결 리스트의 원소 삭제되는 위치에 따라서 복잡도가 상이하다.
    - **중간**
        - 위에 제시된 과정을 따르면 된다.
        - 이때 복잡도는 `선형탐색` 과정이 요구되기 때문에 `선형시간` \\(O(n)\\)에 해당한다.
    - **맨 앞**
        - prev는 존재하지 않기에, Head의 조정만 요구된다.
        - 이때 복잡도는 앞 부분만 삭제하면 되기에 `상수시간` \\(O(1)\\)에 해당한다.
    - **맨 끝**
        - Tail의 조정만 요구된다.
        - 하지만, <u>기존 Tail노드를 삭제하기위해 Tail노드를 탐색할 뿐만 아니라 prev노드 탐색도 필요</u>하다. 왜냐하면, 원소의 “추가”와 달리 prev노드의 포인터에 None을 할당하여 Tail노드로 전환할 필요가 있기 떄문이다. 이때, <u>선형탐색은 Tail에서 Head방향으로 탐색이 불가</u>하기 때문에 <u>Head방향에서 시작하여 Tail노드로 전환할 prev노드를 선형탐색</u>해야한다.
        - 따라서, 이때 복잡도는 `선형시간` \\(O(n)\\)에 해당한다.

<br>

- 마지막으로, 전체적인 원소 삭제 과정을 함수(`popAt`)로 표현하면 다음과 같을 것이다.
    
    ```python
    class Node:...
    
    class LinkedList:
        def __init__(self, node_count, head_N=None, tail_N=None):...
    
        def getAt(self, pos):...
    
        def traverse(self):...		

        def popAt(self, pos):
    
            if (pos < 1) or (pos > self.nodeCount):
                raise IndexError
            else:
                # 삭제할 노드의 원소 보관
                pop_value = self.getAt(pos).data
    
            # linkdlist의 노드 수가 1개일 경우
            if self.nodeCount == 1:
                self.head = None
                self.tail = None
    
            else:
                # "맨 앞"의 노드를 삭제할 경우
                if pos == 1: 
                    self.head = self.getAt(pos).next
                            
                # "맨 끝"의 노드를 삭제할 경우
                elif pos == self.nodeCount: 
                    self.tail = self.getAt(pos-1)
                    self.tail.next = None
                            
                # "중간"의 노드를 삭제할 경우
                else: 
                    self.getAt(pos-1).next = self.getAt(pos).next
                
            # nodeCount를 1감소시킨다.
            self.nodeCount -= 1
                    
            # 삭제된 노드의 원소 반환
            return pop_value
    
    if __name__ == '__main__':
        # 노드 객체를 생성한다.
        N1 = Node(43)
        N2 = Node(85)
        N3 = Node(62)
    
        # 노드를 연결시키고, 연결리스트 객체를 생성한다.
        N1.next = N2
        N2.next = N3
        linkdlist = LinkedList(3, N1, N2)
    		
        # 생성된 연결리스트 객체의 3번째 노드를 삭제한다.
        print(linkdlist.popAt(3)) # 85
        print(linkdlist.traverse()) # [43, 99, 62]
    ```

<br>    

### <span style="color:navy">(4) 두 리스트 병합(Concatenation)<span>

- 독립된 연결리스트들의 병합 과정은 다음의 절차를 따른다.<br>
  (***L1***: 병합 시 앞 부분에 위치한 연결리스트,  ***L2***: 병합 시 뒷 부분에 위치한 연결리스트)
    
  1. ***L1.Tail***를  ***L2.Head***노드 바라보게 하기위해서<br>***L1.Tail***의 포인터에 ***L2.Head***의 주소를 할당한다.
  2. 병합된 ***L1.Tail***를 ***L2.Tail***로 전환하는 과정을 수행한다. <br>만약, 병합되는 연결리스트***L2***가 비어있을 경우 (즉, `L2.Tail = None`)<br>과정을 수행하지 않는다. 
  3. 기존 ***L1***과 ***L2***의 ***nodeCount***를 합산해 병합된 ***L1.nodeCount***에 할당한다.
  
<br>

- 위의 과정을 클래스 내 함수(`concat`)로 구현하면 다음과 같다.
    
    ```python
    class Node:...
    
    class LinkedList:
        def __init__(self, node_count, head_N=None, tail_N=None):...
    
        def getAt(self, pos):...
    
        def traverse(self):...		

        def concat(self, L2):
            
            # 1. L1.Tail의 포인터에 L2.Head의 클래스 객체를 할당한다.
            self.tail.next = L2.head
            
            # 2. L2가 유효한 경우에만 L1.Tail의 위치를 L2.Tail로 변경한다. 
            if L2.tail:
                self.tail = L2.tail
        
            # 3. 병합된 연결리스트의 nodeCount를 재집계한다.
            self.nodeCount += L2.nodeCount
    ```
<br>

---


## <span style="color:navy">References<span>
- [Programmers, 어서와! 자료구조와 알고리즘은 처음이지?](https://school.programmers.co.kr/learn/courses/57/57-%EC%96%B4%EC%84%9C%EC%99%80-%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0%EC%99%80-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98%EC%9D%80-%EC%B2%98%EC%9D%8C%EC%9D%B4%EC%A7%80)
