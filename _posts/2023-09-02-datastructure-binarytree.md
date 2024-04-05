---
layout: post
title: "[자료구조] 15. 이진 트리 (Binary Trees)"
date: 2023-09-02 00:00:00 +0900
categories: [Data, Data Structure]
tags: [Data, Data Structure, Tree, Node, ADT]
comments: true
math: true
mermaid: true
---

## <span style="color:navy">이진 트리의 추상적 자료구조: 트리 & 노드</span>

### <span style="color:navy">(1) 이진 트리: 노드(Node) 구현</span>

- 이진 트리의 노드에는 데이터 원소만이 포함되어 있을 뿐만 아니라, 좌측 자식 노드와 우측 좌식 노드를 이어주는 `링크(즉, 포인터)`가 존재한다. 따라서, 이진 트리의 노드를 객체로 구현할 때 다음의 요소들을 고려해야한다.
    - **노드의 데이터 원소 (data)**
    - **좌측 자식 노드를 가리키는 포인터 (left)**
    - **우측 자식 노드를 가리키는 포인터 (right)**

<br>

- 코드로 구현화하면 다음과 같다.
    
    ```python
    class Node:
        def __init__(self, item):
            self.data = item
            """
            노드가 처음 초기화 되었을 때, 자식 노드는 없는 상태이므로 
            포인터를 None으로 초기화한다.
            """
            self.left = None
            self.right = None
    ```

<br>    

### <span style="color:navy">(2) 이진 트리: 트리(Tree) 구현</span>

- 이진 트리 객체의 경우 <u>root노드만 지정하도록 객체를 생성</u>하면된다. 왜냐하면, 모든 노드들은 위에서 설명 및 구현된 것 처럼 내부에 자식 노드에 관한 주소 정보를 포인터에 담고 있기 때문이다.
    
    ```python
    class BinaryTree:
        def __init__(self, rootNode):
            self.root = rootNode
    ```

<br>    

---

## <span style="color:navy">이진 트리의 추상적 자료구조: 연산</span>

### <span style="color:navy">(1) 연산의 정의</span>

- 트리가 가진 재귀적인 특징을 기반으로 특정 노드를 루트로 취급하는 서브트리의 속성을 반환시킨다.
- `size()`: 현재 트리에 포함되어 있는 노드의 수 (= 루트 노드의 사이즈 = 트리의 사이즈)
- `depth()`: 현재 트리의 깊이
- `traversal()`: traversal(순회)연산은 트리에서 가장 중요한 연산 중 하나이다. 따라서, 추후 포스팅에서 중점적으로 확인할 예정이다.
    
<br>

### <span style="color:navy">(2) size()</span>

- 트리의 크기는 **자신을 포함한 모든 자손 노드의 수**이며, 트리의 재귀적인 성질을 고려했을 때 트리의 크기는 좌측 서브트리의 크기와 우측 서브트리의 크기에 자기 자신(루트 노드)를 합이다.

  <br>
  
  \\[
  size\;of\;트리 = size\;of\;L서브 트리 +size \;of\; R서브트리 + 1(자기 자신)
  \\]
   
  <br> 

  [![](https://mermaid.ink/img/eyJjb2RlIjoiICAgIGZsb3djaGFydCBUQlxuICAgIGNsYXNzRGVmIGdyZWVuIGZpbGw6Z3JlZW4sIHN0cm9rZTpibGFjaywgc3Ryb2tlLXdpZHRoOjJweCwgY29sb3I6d2hpdGVcbiAgICBjbGFzc0RlZiBicm93biBmaWxsOmJyb3duLCBzdHJva2U6YmxhY2ssIHN0cm9rZS13aWR0aDoycHgsIGNvbG9yOndoaXRlXG4gICAgY2xhc3NEZWYgcmVkX3cgc3Ryb2tlLXdpZHRoOjJweCwgY29sb3I6cmVkXG4gICAgXG4gICAgTm9kZV9hKFwiQSAoU2l6ZSA9IDMrMysxID0gNylcIik6Ojpicm93blxuICAgIE5vZGVfYigoXCJCXCIpKVxuICAgIE5vZGVfYygoXCJDXCIpKVxuICAgIE5vZGVfZCgoXCJEXCIpKTo6OmdyZWVuXG4gICAgTm9kZV9lKChcIkVcIikpOjo6Z3JlZW5cbiAgICBOb2RlX2YoKFwiRlwiKSk6OjpncmVlblxuICAgIE5vZGVfZygoXCJHXCIpKTo6OmdyZWVuXG4gICAgXG4gICAgc3ViZ3JhcGggXCJSLVN1YnRyZWUoU2l6ZT0zKVwiXG4gICAgTm9kZV9iXG4gICAgTm9kZV9kXG4gICAgTm9kZV9lXG4gICAgZW5kXG4gICAgXG4gICAgc3ViZ3JhcGggXCJMLVN1YnRyZWUoU2l6ZT0zKVwiXG4gICAgTm9kZV9jXG4gICAgTm9kZV9mXG4gICAgTm9kZV9nXG4gICAgZW5kXG4gICAgXG4gICAgTm9kZV9hIC0tLSBOb2RlX2JcbiAgICBOb2RlX2EgLS0tIE5vZGVfY1xuICAgIE5vZGVfYiAtLS0gTm9kZV9kXG4gICAgTm9kZV9iIC0tLSBOb2RlX2VcbiAgICBOb2RlX2MgLS0tIE5vZGVfZlxuICAgIE5vZGVfYyAtLS0gTm9kZV9nIiwibWVybWFpZCI6eyJ0aGVtZSI6ImRlZmF1bHQifSwidXBkYXRlRWRpdG9yIjpmYWxzZX0)](https://mermaid-js.github.io/docs/deprecated-editor/#/edit/eyJjb2RlIjoiICAgIGZsb3djaGFydCBUQlxuICAgIGNsYXNzRGVmIGdyZWVuIGZpbGw6Z3JlZW4sIHN0cm9rZTpibGFjaywgc3Ryb2tlLXdpZHRoOjJweCwgY29sb3I6d2hpdGVcbiAgICBjbGFzc0RlZiBicm93biBmaWxsOmJyb3duLCBzdHJva2U6YmxhY2ssIHN0cm9rZS13aWR0aDoycHgsIGNvbG9yOndoaXRlXG4gICAgY2xhc3NEZWYgcmVkX3cgc3Ryb2tlLXdpZHRoOjJweCwgY29sb3I6cmVkXG4gICAgXG4gICAgTm9kZV9hKFwiQSAoU2l6ZSA9IDMrMysxID0gNylcIik6Ojpicm93blxuICAgIE5vZGVfYigoXCJCXCIpKVxuICAgIE5vZGVfYygoXCJDXCIpKVxuICAgIE5vZGVfZCgoXCJEXCIpKTo6OmdyZWVuXG4gICAgTm9kZV9lKChcIkVcIikpOjo6Z3JlZW5cbiAgICBOb2RlX2YoKFwiRlwiKSk6OjpncmVlblxuICAgIE5vZGVfZygoXCJHXCIpKTo6OmdyZWVuXG4gICAgXG4gICAgc3ViZ3JhcGggXCJSLVN1YnRyZWUoU2l6ZT0zKVwiXG4gICAgTm9kZV9iXG4gICAgTm9kZV9kXG4gICAgTm9kZV9lXG4gICAgZW5kXG4gICAgXG4gICAgc3ViZ3JhcGggXCJMLVN1YnRyZWUoU2l6ZT0zKVwiXG4gICAgTm9kZV9jXG4gICAgTm9kZV9mXG4gICAgTm9kZV9nXG4gICAgZW5kXG4gICAgXG4gICAgTm9kZV9hIC0tLSBOb2RlX2JcbiAgICBOb2RlX2EgLS0tIE5vZGVfY1xuICAgIE5vZGVfYiAtLS0gTm9kZV9kXG4gICAgTm9kZV9iIC0tLSBOb2RlX2VcbiAgICBOb2RlX2MgLS0tIE5vZGVfZlxuICAgIE5vZGVfYyAtLS0gTm9kZV9nIiwibWVybWFpZCI6eyJ0aGVtZSI6ImRlZmF1bHQifSwidXBkYXRlRWRpdG9yIjpmYWxzZX0)

<br>

- 특정 노드를 루트 노드로 취급하는 서브트리의 사이즈를 코드로 구현하면 다음과 같다.

  ```python
  class Node:
      def __init__(self, item):...
          self.data = item
          self.left = None
          self.right = None
  
      def size(self):
          # 빈 노드로 판정될 경우 노드의 사이즈는 0이다.
          cnt_left = self.left.size() if self.left else 0
          cnt_right = self.right.size() if self.right else 0 
          return cnt_left + cnt_right + 1
  ```

<br>

- 그리고, 트리 클래스를 구성하여 구체적인 root노드를 지정해준다. <br>
(*이때, 트리 내에 루트 노드를 포함해 노드가 비어있는 경우를 고려하여 예외 처리를 수행한다.)
    
    ```python
    class BinaryTree:
      def __init__(self, rootNode):
          self.root = rootNode
      
      def size(self):
          # 이진 트리가 비어 있을 경우 고려 (비었음 -> 사이즈 = 0)
          return self.root.size() if self.root else 0
    ```

<br>

### <span style="color:navy">(3) depth()</span>

- 트리의 깊이는 **트리 내 모든 노드 중에서 가장 깊은 노드의 깊이**를 의미한다.
- 이를 집계하기 위해 재귀적으로 가장 깊은 노드를 탐색하기 위해  루트 노드의 좌측 서브트리와 우측 서브트리의 깊이를 비교할 필요가 있다. 
- 이때, 비교된 결과를 단순히 반환하는 것이 아니라, <br>해당 서브트리의 부모 노드인 현재 노드의 깊이를 고려하기 위해 1을 더한다.
    
  <br>

  \\[
  Depth \;of\;트리 = MAX(Depth\;of\;L서브 트리, Depth \;of\; R서브트리) + 1
  \\]
    
  <br>  

  [![](https://mermaid.ink/img/eyJjb2RlIjoiICAgZmxvd2NoYXJ0IFRCXG4gICAgY2xhc3NEZWYgZ3JlZW4gZmlsbDpncmVlbiwgc3Ryb2tlOmJsYWNrLCBzdHJva2Utd2lkdGg6MnB4LCBjb2xvcjp3aGl0ZVxuICAgIGNsYXNzRGVmIGJyb3duIGZpbGw6YnJvd24sIHN0cm9rZTpibGFjaywgc3Ryb2tlLXdpZHRoOjJweCwgY29sb3I6d2hpdGVcbiAgICBjbGFzc0RlZiByZWRfdyBzdHJva2Utd2lkdGg6MnB4LCBjb2xvcjpyZWRcbiAgICBjbGFzc0RlZiBlbXAgc3Ryb2tlLXdpZHRoOjJweCwgY29sb3I6d2hpdGUsIHN0cm9rZS1kYXNoYXJyYXk6IDUgNVxuICAgIFxuICAgIE5vZGVfYShcIkEgKERlcHRoID0gTWF4KDMsMikrMSA9IDQpXCIpOjo6YnJvd25cbiAgICBOb2RlX2IoKFwiQlwiKSlcbiAgICBOb2RlX2MoKFwiQ1wiKSlcbiAgICBOb2RlX2QoKFwiRFwiKSk6OjpncmVlblxuICAgIE5vZGVfZSgoXCJFXCIpKTo6OmdyZWVuXG4gICAgTm9kZV9mKChcIkZcIikpXG4gICAgTm9kZV9nKChcIkdcIikpOjo6Z3JlZW5cbiAgICBOb2RlX2goKFwiSFwiKSk6OjpncmVlblxuICAgIGVtcHR5KFwiZW1wdHlcIik6OjplbXBcbiAgICBcbiAgICBzdWJncmFwaCBcIlItU3VidHJlZShEZXB0aD0yKVwiXG4gICAgTm9kZV9iXG4gICAgTm9kZV9kXG4gICAgTm9kZV9lXG4gICAgZW5kXG4gICAgXG4gICAgc3ViZ3JhcGggXCJMLVN1YnRyZWUoRGVwdGg9MylcIlxuICAgIE5vZGVfY1xuICAgIE5vZGVfZlxuICAgIE5vZGVfZ1xuICAgIE5vZGVfaFxuICAgIGVtcHR5XG4gICAgZW5kXG4gICAgXG4gICAgTm9kZV9hIC0tLSBOb2RlX2JcbiAgICBOb2RlX2EgLS0tIE5vZGVfY1xuICAgIE5vZGVfYiAtLS0gTm9kZV9kXG4gICAgTm9kZV9iIC0tLSBOb2RlX2VcbiAgICBOb2RlX2MgLS0tIE5vZGVfZlxuICAgIE5vZGVfYyAtLS0gTm9kZV9nXG4gICAgTm9kZV9mIC0tLSBOb2RlX2hcbiAgICBOb2RlX2YgLS4tIGVtcHR5IiwibWVybWFpZCI6eyJ0aGVtZSI6ImRlZmF1bHQifSwidXBkYXRlRWRpdG9yIjpmYWxzZX0)](https://mermaid-js.github.io/docs/deprecated-editor/#/edit/eyJjb2RlIjoiICAgZmxvd2NoYXJ0IFRCXG4gICAgY2xhc3NEZWYgZ3JlZW4gZmlsbDpncmVlbiwgc3Ryb2tlOmJsYWNrLCBzdHJva2Utd2lkdGg6MnB4LCBjb2xvcjp3aGl0ZVxuICAgIGNsYXNzRGVmIGJyb3duIGZpbGw6YnJvd24sIHN0cm9rZTpibGFjaywgc3Ryb2tlLXdpZHRoOjJweCwgY29sb3I6d2hpdGVcbiAgICBjbGFzc0RlZiByZWRfdyBzdHJva2Utd2lkdGg6MnB4LCBjb2xvcjpyZWRcbiAgICBjbGFzc0RlZiBlbXAgc3Ryb2tlLXdpZHRoOjJweCwgY29sb3I6d2hpdGUsIHN0cm9rZS1kYXNoYXJyYXk6IDUgNVxuICAgIFxuICAgIE5vZGVfYShcIkEgKERlcHRoID0gTWF4KDMsMikrMSA9IDQpXCIpOjo6YnJvd25cbiAgICBOb2RlX2IoKFwiQlwiKSlcbiAgICBOb2RlX2MoKFwiQ1wiKSlcbiAgICBOb2RlX2QoKFwiRFwiKSk6OjpncmVlblxuICAgIE5vZGVfZSgoXCJFXCIpKTo6OmdyZWVuXG4gICAgTm9kZV9mKChcIkZcIikpXG4gICAgTm9kZV9nKChcIkdcIikpOjo6Z3JlZW5cbiAgICBOb2RlX2goKFwiSFwiKSk6OjpncmVlblxuICAgIGVtcHR5KFwiZW1wdHlcIik6OjplbXBcbiAgICBcbiAgICBzdWJncmFwaCBcIlItU3VidHJlZShEZXB0aD0yKVwiXG4gICAgTm9kZV9iXG4gICAgTm9kZV9kXG4gICAgTm9kZV9lXG4gICAgZW5kXG4gICAgXG4gICAgc3ViZ3JhcGggXCJMLVN1YnRyZWUoRGVwdGg9MylcIlxuICAgIE5vZGVfY1xuICAgIE5vZGVfZlxuICAgIE5vZGVfZ1xuICAgIE5vZGVfaFxuICAgIGVtcHR5XG4gICAgZW5kXG4gICAgXG4gICAgTm9kZV9hIC0tLSBOb2RlX2JcbiAgICBOb2RlX2EgLS0tIE5vZGVfY1xuICAgIE5vZGVfYiAtLS0gTm9kZV9kXG4gICAgTm9kZV9iIC0tLSBOb2RlX2VcbiAgICBOb2RlX2MgLS0tIE5vZGVfZlxuICAgIE5vZGVfYyAtLS0gTm9kZV9nXG4gICAgTm9kZV9mIC0tLSBOb2RlX2hcbiAgICBOb2RlX2YgLS4tIGVtcHR5IiwibWVybWFpZCI6eyJ0aGVtZSI6ImRlZmF1bHQifSwidXBkYXRlRWRpdG9yIjpmYWxzZX0)
  
<br>  

- 특정 노드를 루트 노드로 취급하는 서브트리의 깊이를 코드로 구현하면 다음과 같다.
    
    ```python
    class Node:
        def __init__(self, item):
            self.data = item
            self.left = None
            self.right = None

        def depth(self):
            # 빈 노드로 판정될 경우 노드의 깊이는 0이다.
            depth_left = self.left.depth() if self.left else 0
            depth_right = self.right.depth() if self.right else 0
            return max(depth_left, depth_right) + 1
    ```

<br>

- 그리고, 트리 클래스를 구성하여 구체적인 root노드를 지정해준다. <br>
(*이때, 트리 내에 루트 노드를 포함해 노드가 비어있는 경우를 고려하여 예외 처리를 수행한다.)
    
    ```python
    class BinaryTree:
        def __init__(self, rootNode):
            self.root = rootNode
        
        def depth(self):
            # 이진 트리가 비어 있을 경우 고려 (비었음 -> 깊이 = 0)    
            return self.root.depth() if self.root else 0
    ```
  
<br>

---

## <span style="color:navy">References<span>
- [Programmers, 어서와! 자료구조와 알고리즘은 처음이지?](https://school.programmers.co.kr/learn/courses/57/57-%EC%96%B4%EC%84%9C%EC%99%80-%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0%EC%99%80-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98%EC%9D%80-%EC%B2%98%EC%9D%8C%EC%9D%B4%EC%A7%80)
