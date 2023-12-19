---
layout: post
title: "[자료구조] 13. 우선순위 큐 (Priority Queues)"
date: 2023-08-01 00:00:00 +0900
categories: [Data, Data Structure]
tags: [Data, Data Structure, Priority Queue, Doubly Linked Lists, ADT]
comments: true
math: true
mermaid: true
---

## <span style="color:navy">우선순위 큐 (Priority Queue)<span>

- `우선순위 큐(Priority Queue)`는 일반적인 큐와 달리 선입선출(FIFO, First-In First Out) 방식을 따르지 않고, <u>우선순위가 높은 데이터 원소가 먼저 큐에서 빠져나오는 방식</u>이다.

<br>

- 예를 들어 다음과 같이 [6, 2, 3, 5] 정수형 원소들을 순차적으로 enqueue한 큐가 있다고 가정하자.
    
  [![](https://mermaid.ink/img/eyJjb2RlIjoiICAgIGZsb3djaGFydCBUQlxuICAgIGNsYXNzRGVmIGdyZWVuIGZpbGw6Z3JlZW4sIHN0cm9rZTpibGFjaywgc3Ryb2tlLXdpZHRoOjJweCwgY29sb3I6d2hpdGVcbiAgICBcbiAgICBOb2RlMShcIjZcIilcbiAgICBOb2RlMihcIjJcIilcbiAgICBOb2RlMyhcIjNcIilcbiAgICBOb2RlNChcIjVcIilcbiAgICBcbiAgICBzdWJncmFwaCBcIlF1ZXVlXCJcbiAgICBcdE5vZGUxIC0tLSBOb2RlMiAtLS0gTm9kZTMgLS0tIE5vZGU0XG4gICAgZW5kOyIsIm1lcm1haWQiOnsidGhlbWUiOiJkZWZhdWx0In0sInVwZGF0ZUVkaXRvciI6ZmFsc2V9)](https://mermaid-js.github.io/docs/deprecated-editor/#/edit/eyJjb2RlIjoiICAgIGZsb3djaGFydCBUQlxuICAgIGNsYXNzRGVmIGdyZWVuIGZpbGw6Z3JlZW4sIHN0cm9rZTpibGFjaywgc3Ryb2tlLXdpZHRoOjJweCwgY29sb3I6d2hpdGVcbiAgICBcbiAgICBOb2RlMShcIjZcIilcbiAgICBOb2RlMihcIjJcIilcbiAgICBOb2RlMyhcIjNcIilcbiAgICBOb2RlNChcIjVcIilcbiAgICBcbiAgICBzdWJncmFwaCBcIlF1ZXVlXCJcbiAgICBcdE5vZGUxIC0tLSBOb2RlMiAtLS0gTm9kZTMgLS0tIE5vZGU0XG4gICAgZW5kOyIsIm1lcm1haWQiOnsidGhlbWUiOiJkZWZhdWx0In0sInVwZGF0ZUVkaXRvciI6ZmFsc2V9)
    
  <br>  
  그리고, <u>해당 큐의 우선순위는 작은 수가 우선순위가 높다고 가정</u>했을 때, dequeue의 순서는 2, 3, 5, 6순서대로 빠져나올 것이다. 왜냐하면, 들어온 순서와 상관없이 우선순위에 따라서 나가는 순서가 결정되었기 때문이다.
    
  [![](https://mermaid.ink/img/eyJjb2RlIjoiICAgIGZsb3djaGFydCBMUlxuICAgIGNsYXNzRGVmIGdyZWVuIGZpbGw6Z3JlZW4sIHN0cm9rZTpibGFjaywgc3Ryb2tlLXdpZHRoOjJweCwgY29sb3I6d2hpdGVcbiAgICBcbiAgICBOb2RlMShcIjZcIilcbiAgICBOb2RlMihcIjJcIilcbiAgICBOb2RlMyhcIjNcIilcbiAgICBOb2RlNChcIjVcIilcbiAgICBcbiAgICBzdWJncmFwaCBcIlF1ZXVlXCJcbiAgICBcdE5vZGUxIC0tLSBOb2RlNFxuICAgIGVuZDtcbiAgICBOb2RlNCAtLT4gTm9kZTMgLS0-IE5vZGUyIiwibWVybWFpZCI6eyJ0aGVtZSI6ImRlZmF1bHQifSwidXBkYXRlRWRpdG9yIjpmYWxzZX0)](https://mermaid-js.github.io/docs/deprecated-editor/#/edit/eyJjb2RlIjoiICAgIGZsb3djaGFydCBMUlxuICAgIGNsYXNzRGVmIGdyZWVuIGZpbGw6Z3JlZW4sIHN0cm9rZTpibGFjaywgc3Ryb2tlLXdpZHRoOjJweCwgY29sb3I6d2hpdGVcbiAgICBcbiAgICBOb2RlMShcIjZcIilcbiAgICBOb2RlMihcIjJcIilcbiAgICBOb2RlMyhcIjNcIilcbiAgICBOb2RlNChcIjVcIilcbiAgICBcbiAgICBzdWJncmFwaCBcIlF1ZXVlXCJcbiAgICBcdE5vZGUxIC0tLSBOb2RlNFxuICAgIGVuZDtcbiAgICBOb2RlNCAtLT4gTm9kZTMgLS0-IE5vZGUyIiwibWVybWFpZCI6eyJ0aGVtZSI6ImRlZmF1bHQifSwidXBkYXRlRWRpdG9yIjpmYWxzZX0)
    

<br>

- 우선순위 큐는 운영체제에서 **CPU스케줄링**에 중요한 역할을 수행한다.
    - CPU 스케줄링이란, CPU의 처리 능력을 효율적으로 사용하기 위해, 어떤 프로세스가 언제 CPU를 사용할 것인지를 결정하는 방법이다.
    - 이때, 우선순위 큐는 <u>여러 프로세스 중에서 어떤 프로세스가 먼저 CPU의 리소스를 사용할지 결정</u>하는 데 사용된다.

<br>

---

## <span style="color:navy">우선순위 큐의 구현 방식 with 알고리즘<span>

- 우선순위 큐는 다음과 같은 2가지 방식의 알고리즘으로 구현이 가능하다.
  1. **Enqueue 수행 시, 우선순위 순서를 유지하도록 구현**
  2. **Dequeue 수행 시, 우선순위가 높은 것을 선택하도록 구현**
  
  두 방법 모두 우선순위 큐의 목적을 달성하지만, 각각의 장단점이 존재한다.
    
<br>

- Case1) **Enqueue 수행 시, 우선순위 순서를 유지하도록 구현**
    - 새로운 데이터가 추가될 때마다, 그  요소를 우선순위에 맞는 위치에 삽입하여, <u>큐가 정렬된 상태를 유지하는 방법</u>이다.
    - **장점**: <u>Dequeue연산이 간단</u>해진다. 항상 첫 번째 요소가 우선순위가 가장 높기 때문에, Dequeue연산은 \\(O(1)\\)의 시간 복잡도를 가진다.
    - **단점**: <u>Enqueue연산이 비효율적</u>일 수 있다. 우선순위를 고려한 적절한 위치를 찾기 위해 큐에 Input될 원소의 우선순위를 기준으로 큐 내부의 데이터 우선순위를 모두 검사해야하므로, 이 경우 \\(O(n)\\)의 시간 복잡도를 가지게 된다.

<br>

- Case2) **Dequeue 수행 시, 우선순위가 높은 것을 선택하도록 구현**
    - 새로운 데이터가 추가될 때마다, 그 요소를 큐의 끝에 추가하고, <u>Dequeue 연산이 일어날 때 큐 내에서 우선순위가 가장 높은 요소를 찾는 방법</u>이다.
    - **장점**: <u>Enqueue연산이 간단</u>해진다. 새로운 데이터 원소는 항상 큐의 끝에 추가되므로, Enqueue연산은 \\(O(1)\\)의 시간 복잡도를 가진다.
    - **단점**: <u>Dequeue연산이 비효율적</u>일 수 있다. 우선순위가 가장 높은 요소를 찾기 위해 큐의 모든 요소를 검사해야 하므로, 이 경우 Dequeue연산은 \\(O(n)\\)의 시간 복잡도를 가진다.

<br>

- 위의 두 방식 중 <u>무엇이 더 효율적인지는 환경의 요구사항에 따라 다르다</u>. <br> 예를 들면, 연산의 빈번도에 따라서 다음과 같이 케이스를 나누어 볼 수 있을 것 같다.
    
    - **Dequeue연산 빈번도 > Enqueue연산 빈번도**<br>
    : Dequeue연산에 관해서 효율성을 가진 Case1방식이 효율적일 것이다.
    
    - **Dequeue연산 빈번도 < Enqueue연산 빈번도**<br>
    : Enqueue연산에 관해서 효율성을 가진 Case2방식이 효율적일 것이다.

<br>

---

## <span style="color:navy">우선순위 큐의 구현 방식 with 자료구조<span>

- 다른 큐와 마찬가지로 우선순위 큐도 **선형 배열**과 **연결 리스트**를 이용하여 구현할 수 있다. <br>이때, 무엇이 더 효율적인지는 **공간적 측면**과 **시간적 측면**을 나누어 비교해볼 수 있다.

<br>

- **공간적 측면**
    - **선형 배열 기반**: 배열은 메모리 공간을 연속적으로 사용하기 때문에, <u>메모리 공간을 효율적으로 사용</u>할 수 있다.
    - **양방향 연결 리스트 기반**: 각 노드는 데이터와 두 개의 링크(앞 노드와 뒷 노드를 가리키는)를 갖고 있다. 이로 인해 <u>추가적인 메모리가 필요</u>하다.

<br>
    
- **시간적 측면**
    - *(주의) 해당 측면을 비교 시 (\\(O(n)\\)의 시간 복잡도를 가지는 우선순위 비교 탐색과정은 삽입/삭제 과정과 독립되었다고 가정한다.
    - **선형 배열 기반**: 배열에서의 삽입 및 삭제는 일반적으로 <u>인덱싱의 이동을 동반</u>하기 때문에 탐색과정을 고려하지 않아도 \\(O(n)\\)의 시간 복잡도를 가진다.
    - **양방향 연결 리스트 기반**: 선형 배열처럼 인덱싱의 이동이 발생하는 것이 아닌, 링크의 재구성 만이 이루어지기 때문에 <u>연결 리스트에서의 삽입과 삭제는 탐색과정을 고려하지 않았을 때</u> \\(O(1)\\)의 시간 복잡도를 가진다.

<br>

- 알고리즘 구성과 마찬가지로 기반이되는 자료구조 선정 시에도 양측 모두 장단점이 있어서 상황에 따라 효율성이 나누어질 것이다. 따라서, 무엇이 더 효율적이라고 단순히 제시할 수 없다.

<br>

---

## <span style="color:navy">우선순위 큐의 추상적 자료구조 구현<span>

- 위에서 우선순위 큐를 구성하는 알고리즘 방식과 기반이 되는 자료구조에 관해서 설명했다. 그리고, 두 요소로 만들 수 있는 경우의 수 중  **Enqueue 수행 시, 우선순위 순서를 유지하도록 양방향 연결리스트 기반의 방식으로 우선순위 큐**를 구현해보고자 한다.

<br>

### <span style="color:navy">(1) 연산의 정의<span>

- `size()`: 현재 큐에 들어있는 데이터 원소의 수를 구한다.
- `isEmpty()`: 현재 큐가 비어있는지를 판단한다.
- `enqueue()`: 데이터 원소 x를 큐에 추가한다.
- `dequeue()`: 큐의 맨 앞에 저장된 데이터 원소를 제거하고 반환한다.
- `peek()`: 큐의 맨 앞에 저장된 데이터 원소를 반환한다. (*공개 라이브러리에는 X)

<br>

### <span style="color:navy">(2) 양방향 연결리스트를 이용하여 구현<span>

- 제시된 우선순위 큐의 ADT를 코드로 구현하면 다음과 같다.
  ```python
  # 노드, 양방향연결리스트의 모듈 코드는 이전 포스팅을 참고 부탁드립니다.
  from utils_practice.node_v2 import Node
  from utils_practice.linkedlist_v2 import DoublyLinkedList
  
  class PriorityLinkedListQueue:
      def __init__(self):
          self.queue = DoublyLinkedList()
  
      def size(self):
          return self.queue.nodeCount
  
      def isEmpty(self):
          return self.size == 0
  
      def enqueue(self, x):
          newNode = Node(x)
          curr = self.queue.head
          while curr.next.data is not None and x < curr.next.data:
              curr = curr.next
          self.queue.insertAfter(curr, newNode)
  
      def dequeue(self):
          return self.queue.popAt(self.queue.nodeCount)
  
      def peek(self):
          return self.queue.tail.prev.data
  
  if __name__ == '__main__':
  
      queue = PriorityLinkedListQueue()
      data_list = [90, 35, 100]

      """
      [ 출력값 ]
      Size: 1, isEmpty: False, peek: 90  
      Size: 2, isEmpty: False, peek: 35
      Size: 3, isEmpty: False, peek: 35
      """      
      for data in data_list:
        queue.enqueue(data)
        print(f'Size: {queue.size()}, isEmpty: {queue.isEmpty()}, peek: {queue.peek()}')
            
  
      print(queue.dequeue()) # 35
  ```

<br>

---

## <span style="color:navy">References<span>
- [Programmers, 어서와! 자료구조와 알고리즘은 처음이지?](https://school.programmers.co.kr/learn/courses/57/57-%EC%96%B4%EC%84%9C%EC%99%80-%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0%EC%99%80-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98%EC%9D%80-%EC%B2%98%EC%9D%8C%EC%9D%B4%EC%A7%80)
