---
layout: post
title: "[자료구조] 16. 이진 트리 순회: 깊이 우선 탐색(Depth First Search, DFS)"
subtitle: "[자료구조] 16. 이진 트리 순회: 깊이 우선 탐색(Depth First Search, DFS)"
categories: data
tags: datastructure
comments: true
---
#### Contents
- [이진 트리의 순회 (Traversal)](#이진-트리의-순회-traversal)
- [깊이 우선 탐색 (DFS)](#깊이-우선-탐색-dfs)
  - [(1) 전위 순회 (Pre-order Traversal)](#1-전위-순회-pre-order-traversal)
  - [(2) 중위 순회 (In-order Traversal)](#2-중위-순회-in-order-traversal)
  - [(3) 후위 순회 (Post-order Traversal)](#3-후위-순회-post-order-traversal)

<br>

---

## <span style="color:navy">이진 트리의 순회 (Traversal)</span>

- 이진 트리의 순회 로직은 `깊이 우선 탐색`과 `넓이 우선 탐색`로 구분된다.
- **깊이 우선 탐색 (Depth First Search, DFS)**: 트리의 루트에서 시작하여 <u>가능한 깊이 들어가며 트리의 노드를 순회</u>한다. 특정 노드에서 더 이상 깊이 들어갈 수 없게되면, 이전 노드로 돌아와 다음 노드를 방문하는 방식으로 순회가 진행된다.
- **넓이 우선 탐색 (Breadth First Search, BFS)**: 트리의 루트에서 시작하여 같은 깊이에 있는 노드를 <u>왼쪽에서 오른쪽으로 순회</u>하고, 다음 깊이의 노드로 넘어가는 방식으로 순회가 진행된다.
    
    (*넓이 우선 순회는 다음 포스팅에서 자세히 정리될 예정이다.)
    
<br>

---

## <span style="color:navy">깊이 우선 탐색 (DFS)</span>

### <span style="color:navy">(1) 전위 순회 (Pre-order Traversal)</span>

- 전위 순회의 진행 방식은 **`현재` → L서브트리 → R서브트리**이며 다음의 과정이 트리의 모든 노드를 방문할 때까지 반복된다.
    1. **현재 노드 방문**: 먼저 현재 노드를 방문한다.
    2. **왼쪽 서브 트리 순회**: 현재 노드의 왼쪽 서브 트리에 대해 재귀적으로 전위 순회를 수행한다.
    3. **오른쪽 서브 트리 순회**: 왼쪽 서브 트리를 완전히 순회한 후, 오른쪽 하위 트리에 대해 재귀적으로 전위 순회를 수행합니다.

<br>

- 전위 순회 과정의 예제는 다음과 같다.

  ![Untitled](https://github.com/jhryu1208/jhryu1208.github.com/assets/53929665/f3043c28-be83-48c5-9c7c-f5cadf77e28f)

   결과: A→B→D→H→E→C→G→F→I

<br>

- 전위 순회 과정을 위한 노드와 트리 구조의 코드를 예시로 구현하면 다음과 같다.
    
    ```python
    class Node: # 트리 구조의 노드 클래스 정의
        def __init__(self, item):
            self.data = item # 노드가 담고 있는 데이터 원소
            self.left = None # 노드의 좌측 자식 노드 주소
            self.right = None # 노드의 우측 자식 노드 주소
            
        def preorder(self):
            traversal = []
    
            """현재 노드 방문"""
            traversal.append(self.data)
            """왼쪽 서브트리 탐색"""
            if self.left:
                traversal.extend(self.left.preorder())
            """오른쪽 서브트리 탐색"""
            if self.right:
                traversal.extend(self.right.preorder())
    
            return traversal
    ```
    
    ```python
    from utils_practice.tree.node import Node
    
    class BinaryTree:
        def __init__(self, rootNode: Node):
            self.root = rootNode # 트리의 root노드 정의
    
        def preorder(self):
            return self.root.preorder() if self.root else []
    ```
    
<br>

### <span style="color:navy">(2) 중위 순회 (In-order Traversal)</span>

- 중위 순회의 진행 방식은 **L서브트리→ `현재` → R서브트리**이며 다음의 과정이 트리의 모든 노드를 방문할 때까지 반복된다.
    1. **왼쪽 서브 트리 순회**: 현재 노드의 왼쪽 서브 트리에 대해 재귀적으로 중위 순회를 수행한다. 이 과정은 왼쪽 서브 트리가 완전히 순회될 때까지 반복된다.
    2. **현재 노드 방문**: 왼쪽 서브 트리를 완전히 순회한 후 현재 노드를 방문한다.
    3. **오른쪽 서브 트리 순회**: 현재 노드를 방문한 후, 오른쪽 서브 트리에 대해 재귀적으로 중위 순회를 수행한다.

<br>

- 중위 순회를 순회하는 예제는 다음과 같다.
    
    ![Untitled 1](https://github.com/jhryu1208/jhryu1208.github.com/assets/53929665/1cf591e2-4a8e-481b-9e56-fbd1275da7fd)
    
    결과: H→D→B→E→A→G→C→F→I
    

- 중위 순회 과정을 위한 노드와 트리 구조의 코드를 예시로 구현하면 다음과 같다.
    
    ```python
    class Node: # 트리 구조의 노드 클래스 정의
        def __init__(self, item):
            self.data = item # 노드가 담고 있는 데이터 원소
            self.left = None # 노드의 좌측 자식 노드 주소
            self.right = None # 노드의 우측 자식 노드 주소
    		
        def inorder(self):
            traversal = []
    
            """왼쪽 서브트리 탐색"""
            if self.left: # 빈 트리 유무 확인
                traversal.extend(self.left.inorder())
            """현재 노드 방문"""
            traversal.append(self.data)
            """오른쪽 서브트리 탐색"""
            if self.right: # 빈 트리 유무 확인
                traversal.extend(self.right.inorder())
    
            return traversal
    ```
    
    ```python
    from utils_practice.tree.node import Node
    
    class BinaryTree:
        def __init__(self, rootNode: Node):
            self.root = rootNode # 트리의 root노드 정의
    
        def inorder(self):
            return self.root.inorder() if self.root else []
    ```
  
<br>

### <span style="color:navy">(3) 후위 순회 (Post-order Traversal)</span>

- 후위 순회의 진행 방식은 **L서브트리→ R서브트리 → `현재`**이며 다음의 과정이 트리의 모든 노드를 방문할 때까지 반복된다.
    1. **왼쪽 서브 트리 순회**: 현재 노드의 왼쪽 서브 트리에 대해 재귀적으로 후위 순회를 수행한다.
    2. **오른쪽 서브 트리 순회**: 왼쪽 서브 트리를 완전히 순회한 후, 오른쪽 서브 트리에 대해 재귀적으로 후위 순회를 수행한다.
    3. **현재 노드 방문**: 왼쪽과 오른쪽 서브 트리를 완전히 순회한 후에 현재 노드를 방문한다.

<br>

- 후위 순회를 순회하는 예제는 다음과 같다.
  
  ![Untitled 2](https://github.com/jhryu1208/jhryu1208.github.com/assets/53929665/0359c9d0-5e4e-4b44-afa5-70e5fce21031)    
    
  결과: H→D→E→B→G→I→F→C→A

<br>

- 중위 순회 과정을 위한 노드와 트리 구조의 코드를 예시로 구현하면 다음과 같다.
    
    ```python
    class Node: # 트리 구조의 노드 클래스 정의
        def __init__(self, item):
            self.data = item # 노드가 담고 있는 데이터 원소
            self.left = None # 노드의 좌측 자식 노드 주소
            self.right = None # 노드의 우측 자식 노드 주소
    		
        def postorder(self):
            traversal = []
    
            """왼쪽 서브트리 탐색"""
            if self.left:
                traversal.extend(self.left.postorder())
            """오른쪽 서브트리 탐색"""
            if self.right:
                traversal.extend(self.right.postorder())
            """현재 노드 방문"""
            traversal.append(self.data)
    
            return traversal
    ```
    
    ```python
    from utils_practice.tree.node import Node
    
    class BinaryTree:
        def __init__(self, rootNode: Node):
            self.root = rootNode # 트리의 root노드 정의
    
    		def postorder(self):
            return self.root.postorder() if self.root else []
    ```

<br>

---

## <span style="color:navy">References<span>
- [Programmers, 어서와! 자료구조와 알고리즘은 처음이지?](https://school.programmers.co.kr/learn/courses/57/57-%EC%96%B4%EC%84%9C%EC%99%80-%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0%EC%99%80-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98%EC%9D%80-%EC%B2%98%EC%9D%8C%EC%9D%B4%EC%A7%80)
