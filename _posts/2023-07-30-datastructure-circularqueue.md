---
layout: post
title: "[자료구조] 12. 환형 큐 (Circular Queue)"
subtitle: "[자료구조] 12. 환형 큐 (Circular Queue)"
categories: data
tags: datastructure
comments: true
---
#### Contents
- [큐(Queue)의 활용](#큐queue의-활용)
- [환형 큐 (Circular Queue)](#환형-큐-circular-queue)
- [환형 큐 VS 양방향 연결리스트 기반의 큐](#환형-큐-vs-양방향-연결리스트-기반의-큐)
- [환형 큐의 추상적 자료구조 구현](#환형-큐의-추상적-자료구조-구현)
  - [(1) 연산의 정의](#1-연산의-정의)
  - [(2) 배열(Array)를 이용하여 구현](#2-배열array를-이용하여-구현)

<br>

---

## <span style="color:navy">큐(Queue)의 활용<span>

- 큐(Queue)는 **자료를 생성 작업**과 **자료를 이용하는 작업**간의 관계를 이어주는 멀티스레딩 또는 멀티프로세싱 환경에서 주로 사용한다.
    - 편의상 다음과 같이 임의로 명명하면서 설명을 진행한다.
    - **Producer**: 자료를 생성하고, 생성한 데이터를 큐에 넣는 쓰레드(Enqueue)
    - **Customer**: 큐 내에서 데이터를 가져와서(Dequeue) 처리하는 쓰레드

<br>

- Producer와 Customer 쓰레드는 큐에 의해서 다음과 같이 다양한 관계성을 맺어 작업을 `비동기적(Asynchronously)`으로 수행할 수 있다.
    - Producer와 Customer쓰레드가 `1:1`로 작업을 수행하는 경우
    - Producer와 Customer쓰레드가 `1:N`으로 작업을 수행하는 경우
    - Producer와 Customer쓰레드가 `N:1`로 작업을 수행하는 경우
    - Producer와 Customer쓰레드가 `M:N`으로 작업을 수행하는 경우

<br>

- 또한, 자료를 처리하여 새로운 자료를 생성하고, 그 자료를 또 처리해야하는 작업의 경우에도 큐가 사용된다.

<br>

---

## <span style="color:navy">환형 큐 (Circular Queue)<span>

- `배열 기반의 선형 큐`를 이용하여 정해진 개수의 데이터만 보관할 수 있도록 지정할 수 있지만, 선형 배열 큐의 경우 dequeue작업이 진행될 때마다, 남은 데이터 원소를 앞으로 당겨온다. 따라서, <u>배열 기반의 선형 큐는 선형 시간</u>\\(O(n)\\) <u>알고리즘에 해당하여 담고 있는 데이터 원소의 수가 증가할수록 비효율적</u>이다.
- `양방향 연결리스트 기반의 큐`의 경우, 위의 비효율성을 타개할 수 있었다. 그리고, 현재 소개되어지는 `환형 큐(Circular Queue)`의 경우에도 위의 비효율성 문제를 타개할 수 있다. 즉, 양방향 연결리스트 기반의 큐와 환형 큐는 <u>배열 기반의 선형 큐에 의해서 발생되는 비효율성을 해결하기위한 자료구조</u>에 해당한다.
- 현재 소개되는 <u>환형 큐는 기존 선형 배열 큐의 처음과 끝을 붙인 원형의 형태로 생각하여 정해진 개수의 저장 공간을 돌려가며 사용하는 큐</u>이다.

  <img src="/assets/img/circularqueue/Untitled.png">
 
<br>

- 환형 큐는 `Dequeue`<u>로 발생한 배열의 빈공간을 적극적으로 활용하는 방식</u>으로 선형 배열의 큐와 동일하게 `Enqueue`와 `Dequeue`작업을 수행한다. 하지만, 이는 선형 배열 기반의 큐처럼 빈공간을 채우기 위해 비효율적으로 데이터 원소를 앞으로 당기는 방식과 다르다.
    - 설명하기에 앞서, 위의 그림과 같이 선형 큐의 앞과 뒤를 가리키는 포인터를 각각 Front, Rear라고 칭한다. 여기서 주의할 것은 <u>Front는 배열의 유효한 맨 앞 원소를 가리키지 않는다</u>.
    - **Enqueue**: <u>Rear포인터가 이동 후, 해당 자리에 새로운 데이터를 저장</u>한다.

        <img src="/assets/img/circularqueue/Untitled_1.png">
  
  <br>
  
    - **Dequeue**: <u>Front포인터를 이동 후, 이동한 Front포인터 가리키는 자리의 데이터를 반환 후 삭제</u>한다. 따라서, `Dequeue` 작업이 수행된 결과 <u>Front가 가리키는 자리에는 데이터가 존재하지 않는다</u>.<br> (*이 부분은 환형 큐의 코드 구현 시 주의해야하는 부분에 해당한다.)
        
        <img src="/assets/img/circularqueue/Untitled_2.png">
           
      따라서, 환형 큐는 Front와 Rear 포인터가 이동하며 처리할 데이터 원소를 가리키는 형태이기에, <u>삽입 & 삭제 작업 시에 선형 배열처럼 데이터 원소를 이동시키거나 순차적으로 탐색할 필요가 없다</u>. 따라서, <u>환형 큐의 삽입과 삭제 연산은</u> \\(O(1)\\) <u>의 시간 복잡도</u>를 가진다.

<br>

---

## <span style="color:navy">환형 큐 VS 양방향 연결리스트 기반의 큐<span>

- 앞서 양방향 연결리스트 기반의 큐와 환형 큐는 모두 배열 기반의 큐에서 발생되는 비효율성을 해결하기 위한 자료구조고 하였다. 하지만, 두 자료구조는 각자의 목적과 특성에 따라 사용되는 케이스가 다를 수 있다. 두 차이점은 큐의 크기 유연성과 시간 미 공간의 효율성 측면에서 바라볼 수 있다.

<br>

- **큐의 크기 유연성 측면**
    - **양방향 연결리스트 기반의 큐**: 작업에 필요한 큐의 사이즈가 얼마나 될지 예측하기 어려운 상황에서 유연하게 대처 가능하다.
    - **환형 큐**: 양방향 연결리스트 기반과 달리 큐의 사이즈가 제한되어있기 때문에 공간 이용 측면에서 효율이 떨어진다. 예를 들어, 너무 크게 환형 큐의 사이즈를 잡아두면 낭비 공간이 많아지고, 그렇다고 크기를 너무 작게 잡으면 추가적인 데이터 원소를 더 쌓기 어렵다.
    - 따라서, <u>큐의 사이즈가 매우 가변적이며 필수적인 데이터 원소의 수를 예상하기 어려운 경우</u>에는 `양방향 연결리스트 기반의 큐`가 적합하다.

<br>

- **시간 및 공간의 효율성 측면**
    - **양방향 연결리스트 기반의 큐**: `Enqueue & Dequeue` 수행 시 노드 사이의 링크들을 조절이 필요하다. 이때, 링크를 조절하는 데 시간이 소모되며, 링크에 대한 정보를 저장하는데 공간이 소모된다.
    - **환형 큐**: 큐의 사이즈가 정해져 있으며, `Enqueue & Dequeue` 작업 수행 시 Front/Real 포인터만 이동시키면된다. 따라서, 노드를 연결하는 새로운 링크를 필요한 공간 소요가 발생하지 않으며, 삽입/삭제 시 기존에 지정된 포인터만 조정하면 되기에 큐를  업데이트하는데 쓰이는 시간 소모도 적다.
    - 따라서, `환형 큐`는 <u>Producer-Consumer 동기화 같은 멀티스레드 환경에서 유용</u>하다. 
    인터넷 동영상의 버퍼링이 예시이다.
        - 종종 유튜브와 같이 인터넷 동영상을 시청하다가 인터넷이 끊어져도 영상이 일정 구간까지 재생되는 것을 확인할 수 있다. 이때 Producer가 큐를 가득 채웠기 때문에 추가적인 데이터를 가져오지 않고 쉴 수 있다.
        - 반면, 영상의 버퍼링을 경험하는 경우도 있는데, 이는 Consumer가 모든 데이터를 소비하여 큐가 Empty가 된 케이스에 해당한다. 이때 Producer가 일정 분량의 데이터를 채울 때까지 기다려야한다.
    - ⚠️ 만약, `양방향 연결 리스트 기반의 큐`로 Producer-Customer 동기화를 구현했으면, `Enqueue`를 수행할 때마다 노드를 생성해야하기 때문에, 큐의 크기가 커질수록 노드 생성에 따른 오버헤드가 증가할 것이다. 그리고, 사이즈 제한이 없기 때문에 많은 메모리를 사용하여 하드웨어 다른 기능에 부정적인 영향을 줄 것이다. (고품질의 영상을 시청하면서 게임을 하는 것이 불가능할 것이다.)

<br>

---

## <span style="color:navy">환형 큐의 추상적 자료구조 구현<span>

### <span style="color:navy">(1) 연산의 정의<span>

- `size()`: 현재 큐에 들어있는 데이터 원소의 수를 구한다.
- `isEmpty()`: 현재 큐가 비어있는지를 판단한다.
- *`isFull()`: 큐에 데이터 원소가 가득 차 있는지를 판단한다.
- `enqueue()`: 데이터 원소 x를 큐에 추가한다.
- `dequeue()`: 큐의 맨 앞에 저장된 데이터 원소를 제거하고 반환한다.
- `peek()`: 큐의 맨 앞에 저장된 데이터 원소를 반환한다. (*공개 라이브러리에는 X)

<br>

### <span style="color:navy">(2) 배열(Array)를 이용하여 구현<span>

- 우선 Empty상태의 환형 큐 객체에 대해서 구현할 필요가 있다.
다음은 빈 환형 큐를 초기화하는 코드이다.
    
    ```python
    class CircularQueue:
    		def __init__(self, n): # n: 초기화할 환형 큐의 사이즈
    				self.maxCount = n # 환형 큐의 사이즈 제한
    				self.data = [None]*n # 환형 큐의 데이터 원소가 담길 배열 초기화
    				self.count = 0 # 환형 큐에 저장된 유효 데이터 수 
    
    				# Empty상태에서는 Front포인터와 Rear포인터를 같은 위치로 초기화한다.
    				self.front = -1 
    				self.rear = -1
    ```

<br>    

- 환형 큐는 **제한된 공간을 순환하는 구조이**다. 따라서, `Enqueue & Dequeue` <u>작업 구현 시 Front와 Rear의 위치가 1씩 증가하다가 최대 배열의 크기가 동일해졌을 경우, 다시 처음</u>(`index=0`)<u>으로 초기화</u>할 필요가 있다. <u>이를 통해 큐가 꽉 차더라도 처음에 사용하던 큐의 공간을 재활용</u>할 수 있게 된다.
    
  따라서, 단순히 `self.front+=1`로 포인터를 증가시키기 보다는 `self.maxCount`<u>에 도달았을 때, </u>`self.front=0`<u>으로 초기화</u> 할 수 있도록 아래와 같이 구현한다.
    
    ```python
    self.front = (self.front+1)%self.maxCount
    self.rear = (self.rear+1)%self.maxCount
    ```

<br>    

- 배열로 환형 큐의 ADT를 코드로 구현하면 다음과 같다.
    
    ```python
    class CircularQueue:
        def __init__(self, n):
            self.maxCount = n
            self.data = [None] * n
            self.count = 0
            self.front = -1
            self.rear = -1
    
        def size(self):
            return self.count # 현재 큐 길이를 반환
    
        def isEmpty(self):
            return self.count == 0  # 큐가 비어 있는가?
    
        def isFull(self):
            return self.count == self.maxCount  # 큐가 꽉 차 있는가?
    
        def enqueue(self, x):
            if self.isFull():  # 큐가 꽉차 있어 enqueue가 불가한 경우
                raise IndexError("Queue Full")
            self.rear = (self.rear+1)%self.maxCount  # rear포인터를 한 칸 이동시킨다.
            self.data[self.rear] = x  # 이동한 rear포인터가 가리키는 위치에 데이터를 삽입한다.
            self.count += 1  # 저장된 데이터 원소 사이즈를 1증가시킨다.
    
        def dequeue(self):
            if self.isEmpty():  # 큐가 비어 있어 dequeue가 불가한 경우
                raise IndexError("Queue Empty")
            self.front = (self.front+1)%self.maxCount  # front포인터를 한 칸 이동시킨다.

            """
            [*Dequeue임에도 제거를 수행하지 않은 이유?]
            
            하단의 코드는 단순히 front가 가리키는 위치의 데이터를 삭제하지 않고 반환만한다.
            왜냐하면, 굳이 제거하지 않아도 enqueue과정에서 초기화된 Rear포인터가
            해당 위치를 가리켜 신규 데이터로 기존 데이터를 덮어쓰기 때문이다.
            
            따라서, 굳이 pop을 수행하여 리스트의 길이를 변동시켜 구조를 더복잡하게하거나
            None으로 데이터 원소를 치환시키는 등 부가적인 절차를 수행할 필요가 없다.
            """
            x = self.data[self.front]

            self.count -= 1  # 저장된 데이터 원소 사이즈를 1감소시킨다.
            return x
    
        def peek(self):
            if self.isEmpty():  # 큐가 비어있어 peek할 원소가 없을 경우
                raise IndexError("Queue Empty")

            """
            [peek연산의 인덱싱에서 self.front+1을 수행한 이유?]
            
            dequeue에 의해서 Front포인터가 한 칸 전진할 경우,
            Front가 가리키는 데이터 원소는 삭제된다. 
            따라서, Front는 실질적으로 큐 내 유효한 맨 앞 원소를 가리키지 않는다.
            달리 말하면, 맨 앞 원소 앞의 공간을 가리키고 있다.
            그러므로, self.front+1을 수행한다.
            """
            return self.data[(self.front+1)%self.maxCount]  # 맨 앞에 위치한 데이터 원소 반환
    
    if __name__ == '__main__':
        print(queue_status(queue)) # Size: 1, Front: -1, Rear: 0, isEmpty: False, isFull: False, Peek: 10
        queue = CircularQueue(3)
        print(f'isEmpty: {queue.isEmpty()}, isFull: {queue.isFull()}') # isEmpty: True, isFull: False
        
        def queue_status(queue):
            return (f'Size: {queue.size()}, Front: {queue.front}, Rear: {queue.rear}, '
                    f'isEmpty: {queue.isEmpty()}, isFull: {queue.isFull()}, '
                    f'Peek: {queue.peek()}')
        
        queue.enqueue(10)
        print(queue.data) # [10, None, None]
        
        queue.enqueue(20)
        print(queue.data) # [10, 20, None]
        print(queue_status(queue)) # Size: 2, Front: -1, Rear: 1, isEmpty: False, isFull: False, Peek: 10
        
        queue.enqueue(30)
        print(queue.data) # [10, 20, 30]
        print(queue_status(queue)) # Size: 3, Front: -1, Rear: 2, isEmpty: False, isFull: True, Peek: 10
        
        print(queue.dequeue()) # 10
        print(queue.data) # [None, 20, 30]
        print(queue_status(queue)) # Size: 2, Front: 0, Rear: 2, isEmpty: False, isFull: False, Peek: 20
        
        
        print(queue.dequeue()) # 20
        print(queue.data) # [None, None, 30]
        print(queue_status(queue)) # Size: 1, Front: 1, Rear: 2, isEmpty: False, isFull: False, Peek: 30
    		
    		
        print(queue.dequeue()) # 30
        print(queue.data) # [None, None, None]
        print(f'isEmpty: {queue.isEmpty()}, isFull: {queue.isFull()}') # isEmpty: True, isFull: False
    ```

<br>

---

## <span style="color:navy">References<span>
- [Programmers, 어서와! 자료구조와 알고리즘은 처음이지?](https://school.programmers.co.kr/learn/courses/57/57-%EC%96%B4%EC%84%9C%EC%99%80-%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0%EC%99%80-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98%EC%9D%80-%EC%B2%98%EC%9D%8C%EC%9D%B4%EC%A7%80)