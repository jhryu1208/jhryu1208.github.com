---
layout: post
title: "[자료구조] 4.알고리즘의 복잡도(Complexity of Algorithms)"
date: 2023-07-23 00:00:00 +0900
categories: [Data, Data Structure]
tags: [Data, Data Structure, Big-O Notation, Complexity of Algorithms]
comments: true
math: true
mermaid: true
---

## 알고리즘의 복잡도

- `알고리즘의 복잡도(Complexity)`는 문제 풀이 방식 혹은 코드가 얼마나 복잡한지 혹은 단순한지를 의미하는 용어가 아니다. 해당 용어는 <u>문제의 크기(일반적으로 데이터 원소의 개수)가 커짐에 따라서</u> <u>얼마나 큰 시간을 혹은 리소스를 요구</u>하는지를 의미한다.

- 알고리즘의 복잡도는 <u>소요되는 성질에 따라</u> 다음의 두가지로 나누어진다.
    - `시간 복잡도(Time Complexity)`
    - `공간 복잡도`

<br>

---

## 시간 복잡도

- `시간 복잡도`는 문제의 크기가 커짐에 따라서, 문제를 해결하는 데 소요되는 “시간”이 “어떤 양상”으로 증가하는가를 다룬다.

- 시간 복잡도는 다음의 두 분류로 나누어진다.
    1. **평균 시간 복잡도 (Average Time Complexity)**
        
        : <u>임의의 패턴들이 인풋되었다고 가정</u>했을 때, 소요되는 <u>처리 시간의 평균</u>
        
    2. **최악 시간 복잡도 (Worst-case Time Complexity)**
        
        : 임의의 인력 패턴 중, <u>가장 긴 시간을 소요하게 만드는 입력</u>에 대한 처리 시간
        

<br>

---

## 빅오 표기법(Big-O Notation)

- 알고리즘의 복잡도를 표현하는 데 있어서 `점근 표기법(Asymptotic Notation)`을 흔히 사용한다.
- 그리고, 점근 표기법 중에는 `Big-O Noation`표기법이 존재한다.
    - Big-O Noation은 <u>함수 증가 양상을 대략적으로 표현</u>하고있기에, <br>알고리즘의 `시간 복잡도`와 `공간 복잡도`를 나타내는데 주로 사용된다.
        
    - \\(O(\log n), O(n), O(n^2), O(2^n)\\)등으로 표기한다.

<br>

### (1) 빅오 표기법 성능

<<<<<<< HEAD
<img src="1UB1KhyMkHcVoZeywdg9WfafM2-7_jQoB", alt="time complexity performance of big-o notation">
=======
<img src="1UB1KhyMkHcVoZeywdg9WfafM2-7_jQoB", alt="time complexity performance of big-o notation>
>>>>>>> origin/master

위의 자료는 빅오 표기의 시간 복잡도에 대한 성능을 비교한 차트이다. <br>해당 차트에 제시된 성능을 비교하면 다음과 같이 요약할 수 있다.<br>(<u>좌에서 우로 갈수록 알고리즘의 효율성이 낮아진다</u>)

\\[O(1) < O(\log n) < O(n) < O(n\log n) < O(n^2) < O(2^n) < O(n!)\\]

<br>

### (2) 빅오 표기법 예시

- **선형 시간 알고리즘**: \\(O(n)\\)
    - 의미: <u>입력의 크기에 비례</u>하는 시간 소요
    - 예시: 선형 배열에서 최댓값을 찾는 경우
    - Average-Case: \\(O(n)\\)
    - Worst-Case: \\(O(n)\\)

- **로그 시간 알고리즘**: \\(O(\log n)\\)
    - 의미:  <u>입력의 크기의 로그에 비례</u>하는 시간 소요
    - 예시: 이진 탐색

<<<<<<< HEAD
- **이차 시간 알고리즘**: \\(O(n^2)\\)
=======
- **이차 시간 알고리즘**: \\(O(n^2)$\\)
>>>>>>> origin/master
    - 의미: <u>입력의 크기의 제곱에 비례</u>하는 시간 소요
    - 예시: 삽입 정렬
    - Best-Case: \\(O(n)\\), 선형 배열이 이미 정렬되어있을 때는 순서대로 살펴보면됨
    - Worst-Case: \\(O(n^2)\\), 선형 배열이 목표 순서의 역순으로 정렬되어 있을 경우

- **로그선형 시간 알고리즘**: \\(O(n\log n)\\)
    - 의미: <u>입력의 크기의 로그선형에 비례</u>하는 시간 소요
<<<<<<< HEAD
    - 특징: 입력 패턴에 따라 정렬 속도에 차이가 있지만, 정렬 문제에 대해서는 \\(O(n\log n)\\)보다 낮은 복잡도를 갖는 알고리즘은 존재할 수 없음이 증명됨
=======
    - 특징: 입력 패턴에 따라 정렬 속도에 차이가 있지만, <u>정렬 문제에 대해서는 \\(O(n\log n)\\)보다 낮은 복잡도를 갖는 알고리즘은 존재할 수 없음</u>이 증명됨
>>>>>>> origin/master
    - 예시: `병합 정렬`
        - 병합정렬은 다음과 같이 Divide &  Conquer방식을 채택하는 로직이다.
[![](https://mermaid.ink/img/eyJjb2RlIjoiICAgICAgICAgICAgZmxvd2NoYXJ0IFREXG4gICAgICAgICAgICBjbGFzc0RlZiBncmVlbiBmaWxsOmdyZWVuLCBzdHJva2U6YmxhY2ssIHN0cm9rZS13aWR0aDoycHgsIGNvbG9yOndoaXRlXG4gICAgICAgICAgICBjbGFzc0RlZiBibHVlIGZpbGw6Ymx1ZSwgc3Ryb2tlOmJsYWNrLCBzdHJva2Utd2lkdGg6MnB4LCBjb2xvcjp3aGl0ZVxuICAgICAgICAgICAgXG4gICAgICAgICAgICBub2RlW1syMSwgMTAsIDIzLCAyMCwgMjcsIDI1LCAyOV1dOjo6Z3JlZW5cbiAgICAgICAgICAgIG4xW1syMSwgMTAsIDIzLCAyMF1dOjo6Z3JlZW5cbiAgICAgICAgICAgIG4yW1syNywgMjUsIDE5XV06OjpncmVlblxuICAgICAgICAgICAgbjNbWzIxLCAxMF1dOjo6Z3JlZW5cbiAgICAgICAgICAgIG40W1syMywgMjBdXTo6OmdyZWVuXG4gICAgICAgICAgICBuNVtbMjcsIDI1XV06OjpncmVlblxuICAgICAgICAgICAgbjZbWzE5XV06OjpncmVlblxuICAgICAgICAgICAgbjdbWzIxXV1cbiAgICAgICAgICAgIG44W1sxMF1dXG4gICAgICAgICAgICBuOVtbMjNdXVxuICAgICAgICAgICAgbjEwW1syMF1dXG4gICAgICAgICAgICBuMTFbWzI3XV1cbiAgICAgICAgICAgIG4xMltbMjVdXVxuICAgICAgICAgICAgbjEzW1sxOV1dXG4gICAgICAgICAgICBuMTRbWzEwLCAyMV1dOjo6Ymx1ZVxuICAgICAgICAgICAgbjE1W1syMCwgMjNdXTo6OmJsdWVcbiAgICAgICAgICAgIG4xNltbMjUsIDI3XV06OjpibHVlXG4gICAgICAgICAgICBuMTdbWzE5XV06OjpibHVlXG4gICAgICAgICAgICBuMThbWzEwLCAyMCwgMjEsIDIzXV06OjpibHVlXG4gICAgICAgICAgICBuMTlbWzE5LCAyNSwgMjddXTo6OmJsdWVcbiAgICAgICAgICAgIG4yMFtbMTAsMTksMjAsMjEsMjMsMjUsMjddXTo6OmJsdWVcbiAgICAgICAgICAgIFxuICAgICAgICAgICAgbm9kZSAtLT4gfERpdmlkZXxuMVxuICAgICAgICAgICAgbm9kZSAtLT4gfERpdmlkZXxuMlxuICAgICAgICAgICAgbjEgLS0-IHxEaXZpZGV8bjNcbiAgICAgICAgICAgIG4xIC0tPiB8RGl2aWRlfG40XG4gICAgICAgICAgICBuMiAtLT4gfERpdmlkZXxuNVxuICAgICAgICAgICAgbjIgLS0-IHxEaXZpZGV8bjZcbiAgICAgICAgICAgIG4zIC0tPiB8RGl2aWRlfG43XG4gICAgICAgICAgICBuMyAtLT4gfERpdmlkZXxuOFxuICAgICAgICAgICAgbjQgLS0-IHxEaXZpZGV8bjlcbiAgICAgICAgICAgIG40IC0tPiB8RGl2aWRlfG4xMFxuICAgICAgICAgICAgbjUgLS0-IHxEaXZpZGV8bjExXG4gICAgICAgICAgICBuNSAtLT4gfERpdmlkZXxuMTJcbiAgICAgICAgICAgIG42IC0tPiBuMTNcbiAgICAgICAgICAgIG43IC0tPiB8Q29ucXVlcnxuMTRcbiAgICAgICAgICAgIG44IC0tPiB8Q29ucXVlcnxuMTRcbiAgICAgICAgICAgIG45IC0tPiB8Q29ucXVlcnxuMTVcbiAgICAgICAgICAgIG4xMCAtLT4gfENvbnF1ZXJ8bjE1XG4gICAgICAgICAgICBuMTEgLS0-IHxDb25xdWVyfG4xNlxuICAgICAgICAgICAgbjEyIC0tPiB8Q29ucXVlcnxuMTZcbiAgICAgICAgICAgIG4xMyAtLT4gbjE3XG4gICAgICAgICAgICBuMTQgLS0-IHxDb25xdWVyfG4xOFxuICAgICAgICAgICAgbjE1IC0tPiB8Q29ucXVlcnxuMThcbiAgICAgICAgICAgIG4xNiAtLT4gfENvbnF1ZXJ8bjE5XG4gICAgICAgICAgICBuMTcgLS0-IHxDb25xdWVyfG4xOVxuICAgICAgICAgICAgbjE4IC0tPiB8Q29ucXVlcnxuMjBcbiAgICAgICAgICAgIG4xOSAtLT4gfENvbnF1ZXJ8bjIwIiwibWVybWFpZCI6eyJ0aGVtZSI6ImRlZmF1bHQifSwidXBkYXRlRWRpdG9yIjpmYWxzZX0)](https://mermaid-js.github.io/docs/mermaid-live-editor-beta/#/edit/eyJjb2RlIjoiICAgICAgICAgICAgZmxvd2NoYXJ0IFREXG4gICAgICAgICAgICBjbGFzc0RlZiBncmVlbiBmaWxsOmdyZWVuLCBzdHJva2U6YmxhY2ssIHN0cm9rZS13aWR0aDoycHgsIGNvbG9yOndoaXRlXG4gICAgICAgICAgICBjbGFzc0RlZiBibHVlIGZpbGw6Ymx1ZSwgc3Ryb2tlOmJsYWNrLCBzdHJva2Utd2lkdGg6MnB4LCBjb2xvcjp3aGl0ZVxuICAgICAgICAgICAgXG4gICAgICAgICAgICBub2RlW1syMSwgMTAsIDIzLCAyMCwgMjcsIDI1LCAyOV1dOjo6Z3JlZW5cbiAgICAgICAgICAgIG4xW1syMSwgMTAsIDIzLCAyMF1dOjo6Z3JlZW5cbiAgICAgICAgICAgIG4yW1syNywgMjUsIDE5XV06OjpncmVlblxuICAgICAgICAgICAgbjNbWzIxLCAxMF1dOjo6Z3JlZW5cbiAgICAgICAgICAgIG40W1syMywgMjBdXTo6OmdyZWVuXG4gICAgICAgICAgICBuNVtbMjcsIDI1XV06OjpncmVlblxuICAgICAgICAgICAgbjZbWzE5XV06OjpncmVlblxuICAgICAgICAgICAgbjdbWzIxXV1cbiAgICAgICAgICAgIG44W1sxMF1dXG4gICAgICAgICAgICBuOVtbMjNdXVxuICAgICAgICAgICAgbjEwW1syMF1dXG4gICAgICAgICAgICBuMTFbWzI3XV1cbiAgICAgICAgICAgIG4xMltbMjVdXVxuICAgICAgICAgICAgbjEzW1sxOV1dXG4gICAgICAgICAgICBuMTRbWzEwLCAyMV1dOjo6Ymx1ZVxuICAgICAgICAgICAgbjE1W1syMCwgMjNdXTo6OmJsdWVcbiAgICAgICAgICAgIG4xNltbMjUsIDI3XV06OjpibHVlXG4gICAgICAgICAgICBuMTdbWzE5XV06OjpibHVlXG4gICAgICAgICAgICBuMThbWzEwLCAyMCwgMjEsIDIzXV06OjpibHVlXG4gICAgICAgICAgICBuMTlbWzE5LCAyNSwgMjddXTo6OmJsdWVcbiAgICAgICAgICAgIG4yMFtbMTAsMTksMjAsMjEsMjMsMjUsMjddXTo6OmJsdWVcbiAgICAgICAgICAgIFxuICAgICAgICAgICAgbm9kZSAtLT4gfERpdmlkZXxuMVxuICAgICAgICAgICAgbm9kZSAtLT4gfERpdmlkZXxuMlxuICAgICAgICAgICAgbjEgLS0-IHxEaXZpZGV8bjNcbiAgICAgICAgICAgIG4xIC0tPiB8RGl2aWRlfG40XG4gICAgICAgICAgICBuMiAtLT4gfERpdmlkZXxuNVxuICAgICAgICAgICAgbjIgLS0-IHxEaXZpZGV8bjZcbiAgICAgICAgICAgIG4zIC0tPiB8RGl2aWRlfG43XG4gICAgICAgICAgICBuMyAtLT4gfERpdmlkZXxuOFxuICAgICAgICAgICAgbjQgLS0-IHxEaXZpZGV8bjlcbiAgICAgICAgICAgIG40IC0tPiB8RGl2aWRlfG4xMFxuICAgICAgICAgICAgbjUgLS0-IHxEaXZpZGV8bjExXG4gICAgICAgICAgICBuNSAtLT4gfERpdmlkZXxuMTJcbiAgICAgICAgICAgIG42IC0tPiBuMTNcbiAgICAgICAgICAgIG43IC0tPiB8Q29ucXVlcnxuMTRcbiAgICAgICAgICAgIG44IC0tPiB8Q29ucXVlcnxuMTRcbiAgICAgICAgICAgIG45IC0tPiB8Q29ucXVlcnxuMTVcbiAgICAgICAgICAgIG4xMCAtLT4gfENvbnF1ZXJ8bjE1XG4gICAgICAgICAgICBuMTEgLS0-IHxDb25xdWVyfG4xNlxuICAgICAgICAgICAgbjEyIC0tPiB8Q29ucXVlcnxuMTZcbiAgICAgICAgICAgIG4xMyAtLT4gbjE3XG4gICAgICAgICAgICBuMTQgLS0-IHxDb25xdWVyfG4xOFxuICAgICAgICAgICAgbjE1IC0tPiB8Q29ucXVlcnxuMThcbiAgICAgICAgICAgIG4xNiAtLT4gfENvbnF1ZXJ8bjE5XG4gICAgICAgICAgICBuMTcgLS0-IHxDb25xdWVyfG4xOVxuICAgICAgICAgICAgbjE4IC0tPiB8Q29ucXVlcnxuMjBcbiAgICAgICAgICAgIG4xOSAtLT4gfENvbnF1ZXJ8bjIwIiwibWVybWFpZCI6eyJ0aGVtZSI6ImRlZmF1bHQifSwidXBkYXRlRWRpdG9yIjpmYWxzZX0) 

<br>

---


## <span style="color:navy">References<span>
- [Programmers, 어서와! 자료구조와 알고리즘은 처음이지?](https://school.programmers.co.kr/learn/courses/57/57-%EC%96%B4%EC%84%9C%EC%99%80-%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0%EC%99%80-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98%EC%9D%80-%EC%B2%98%EC%9D%8C%EC%9D%B4%EC%A7%80)
- [Big-O Cheat Sheet](https://www.bigocheatsheet.com/)
