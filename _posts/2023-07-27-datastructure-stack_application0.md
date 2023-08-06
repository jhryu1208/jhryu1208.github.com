---
layout: post
title: "[자료구조] 9. 스택의 응용: 수식의 후위 표기법"
subtitle: "[자료구조] 8. 스택의 응용: 수식의 후위 표기법"
categories: data
tags: datastructure
comments: true
---
#### Contents
- [중위 표기법 & 후위 표기법 (Infix Notation & Postfix Notation)](#중위-표기법--후위-표기법-infix-notation--postfix-notation)
- [중위 표현식 → 후위 표현식 with 스택 ](#중위-표현식--후위-표현식-with-스택)
  - [(1) 우선순위: Stack.peek ≥ 비교 연산자](#1-우선순위-stackpeek--비교-연산자)
  - [(2) 우선순위: 좌측 연산자 < 우측 연산자](#2-우선순위-좌측-연산자--우측-연산자)
  - [(3) 괄호의 처리](#3-괄호의-처리)
- [알고리즘의 설계](#알고리즘의-설계)


<br>

---

## <span style="color:navy"> 중위 표기법 & 후위 표기법 (Infix Notation & Postfix Notation)<span>

- **중위 표기법(Infix Notation)**
    - 일상에서 사용하는 표기법으로, 피연산자들의 사이에 연산자를 표기하는 방법이다.
    - ex. \\((A+B) * (C+D)\\)
- **후위 표기법(Postfix Notation)**
    - 연산자가 피연산자들의 뒤에 표기하는 방법이다.
    - 중위 표기법과 달리 괄호를 쓰지 않고도 연산의 우선순위를 수식에 표현할 수 있다.
    - ex1. \\(AB + CD + *\\)
        - \\(AB + = A + B\\)
        - \\(CD + = C + D\\)
        - \\(AB + CD + * = (A + B) * (C + D)\\)
    - ex2. \\(AB+C*\\)
        - \\(AB+=(A+B)\\)
        - \\(AB+C*=(A+B)*C\\)

<br>

---

## <span style="color:navy">중위 표현식 → 후위 표현식 with 스택 ****<span>

- 중위 표현식을 후위 표현식으로 변환하는 과정은 다음의 절차를 따른다.
    - 중위 표현식을 왼쪽에서 오른쪽으로 읽으면서 다음의 과정을 수행한다.
    - `피연산자`의 경우, 그대로 출력한다.
    - `연산자`의 경우, 연산자의 우선순위를 고려하여 스택에 push를 수행하거나 pop을 수행한다.
        - **Stack.peek ≥ 비교 연산자**: 우선순위가 낮은 peek값을 만날 때 까지 pop을 수행한다.
        - **Stack.peek < 비교 연산자**: 비교 연산자를 스택에 push를 수행한다.
    - `괄호`의 경우,
        - **여는 괄호를 만날 경우**: 우선순위와 관계없이 스택에 push
            - *여는 괄호 이후의 연산자들은 그냥 스택에 추가한다.
        - **닫는 괄호를 만날 경우**: 우선순위와 관계없이 여는 괄호가 나올 때까지 연산자들을 스택에서 pop
    - 중위 표현식을 모두 읽은 후, 스택에 남아있는 모든 연산자들을 출력한다.

<br>
    

### <span style="color:navy">(1) 우선순위: **Stack.peek ≥ 비교 연산자**<span>

- 예제) \\(A*B+C\\)

    1. <u>계산식의 좌측부터 시작</u>한다. A의 경우 피연산자이므로 때문에 출력한다.
        - 결과: \\(A\\)
    2. 다음 *는 연산자이므로 때문에 스택에 push한다.
        - 결과: \\(A, Stack = [*]\\)
    3. 다음 B는 피연산자이므로 때문에 출력한다.
        - 결과: \\(AB, Stack = [*]\\)
    4. 다음 +는 연산자이다. 이때, 스택 내에 존재하던 연산자 *는 연산자 +보다 <u>우선순위가 높기 때문에 pop</u>을 수행한다. 그리고, <u>비어있는 스택에 연산자 +를 push</u>한다.
        - 결과: \\(AB*, Stack = [+]\\)
    5. 다음 C는 피연산자이므로 때문에 출력한다.
        - 결과: \\(AB*C, Stack = [+]\\)
    6. 피연산자 C이후에는 <u>연산이 존재하지 않기 때문에 스택에 보관된 연산자 +를 pop</u>시킨다.
        - 결과: \\(AB*C+, Stack = [ ]\\)

<br>

- 예제) \\(A+B+C\\)

  1. <u>계산식의 좌측부터 시작</u>한다. A의 경우 피연산자이므로 출력한다.
      - 결과: \\(A\\)
  2. 다음 +는 연산자이다. 따라서, 스택에 push한다.
      - 결과: \\(A, Stack = [+]\\)
  3. 다음 B는 피연산자이므로 출력한다.
      - 결과: \\(AB, Stack = [+]\\)
  4. 다음 +는 연산자이다. 이때, 스택 내에 존재하던 연산자 +와 <u>우선순위가 동일하기 때문에 pop</u>을 수행한다. 그리고, <u>비어있는 스택에 연산자 +를 push</u>한다.
      - 결과: \\(AB+, Stack = [+]\\)
  5. 다음 C는 피연산자이므로 출력한다.
      - 결과: \\(AB+C, Stack = [+]\\)
  6. 피연산자 C이후에는 <u>연산이 존재하지 않기 때문에 스택에 보관된 연산자 +를 pop</u>시킨다.
      - 결과: \\(AB+C+, Stack = []\\)

<br>

### <span style="color:navy">(2) 우선순위: 좌측 연산자 < 우측 연산자<span>

- 예제) \\(A+B*C\\)

  1. <u>계산식의 좌측부터 시작</u>한다. A의 경우 피연산자이므로 출력한다.
      - 결과: \\(A\\)
  2. 다음 +는 연산자이다. 따라서, 스택에 push한다.
      - 결과: \\(A, Stack = [+]\\)
  3. 다음 B는 피연산자이므로 출력한다.
      - 결과: \\(AB, Stack = [+]\\)
  4. 다음 *는 연산자이다. 하지만, <u>스택의 peek에 해당하는 연산자 + 는 연산자 *보다 우선순위가 낮다</u>. 따라서, <u>pop시키지 않고 연산자 *를 스택에 push</u>한다.
      - 결과: \\(AB, Stack = [+, *]\\)
  5. 다음 C는 피연산자이므로 출력한다.
      - 결과: \\(ABC, Stack = [+, *]\\)
  6. 피연산자 C이후에는 <u>연산이 존재하지 않기 때문에 스택에 보관된 연산자 *, +를 차례대로 pop</u>시킨다.
      - 결과: \\(ABC*+, Stack = [ ]\\)

<br>

### <span style="color:navy">(3) 괄호의 처리<span>

- 예제) \\(A*(B+C)\\)
     
    1. <u>계산식의 좌측부터 시작</u>한다. A의 경우 피연산자이므로 출력한다.
        - 결과: \\(A\\)
    2. 다음 *는 연산자이다. 따라서, 스택에 push한다.
        - 결과: \\(A, Stack = [*]\\)
    3. 여는 괄호는 우선순위와 관계없이 스택에 push한다. 
        - 결과: \\(A, Stack = [*,(]\\)
    4. 다음 B는 피연산자이므로 출력한다.
        - 결과: \\(AB, Stack = [*,(]\\)
    5. 다음 +는 연산자이다. 스택의 peek이 여는 괄호이므로, 연산자 +를 push한다.
        - 결과: \\(AB, Stack = [*,(, +]\\)
    6. 다음 C는 피연산자이므로 출력한다.
        - 결과: \\(ABC, Stack = [*,(, +]\\)
    7. 닫는 괄호를 만났으므로 여는 괄호까지 pop을 수행한다.
    (<u>이때, 괄호는 후위 표현식의 결과로 출력하지 않는다</u>)
        - 결과: \\(ABC+, Stack = [*]\\)
    8.  닫는 괄호 이후에는 <u>연산이 존재하지 않기 때문에 스택에 보관된 연산자 *를 pop</u>시킨다.
        - 결과: \\(ABC+*, Stack = []\\)
    
<br>

- 예제) \\((A+B)*(C+D)\\)
    1. 계산식의 좌측부터 시작한다. 여는 괄호이므로 스택에 push한다.
        - 결과: \\(, Stack = [(]\\)
    2. 다음 A는 피연산자이므로 출력한다.
        - 결과: \\(A, Stack = [(]\\)
    3. 다음 +는 연산자이고, 스택의 peek이 여는 괄호이므로, 연산자 +를 push한다.
        - 결과: \\(A, Stack = [(,+]\\)
    4. 다음 B는 피연산자이므로 출력한다.
        - 결과: \\(AB, Stack = [(, +]\\)
    5. 닫는 괄호를  만났으므로 여는 괄호까지 pop을 수행한다.<br>(<u>이때, 괄호는 후위 표현식의 결과로 출력하지 않는다</u>)
        - 결과: \\(AB+, Stack = []\\)
    6. 다음 *는 연산자이므로 스택에 push한다.
        - 결과: \\(AB+, Stack = [*]\\)
    7. 여는 괄호이므로 스택에 push한다.
        - 결과: \\(AB+, Stack = [*, (]\\)
    8. 다음 C는 피연산자이므로 출력한다.
        - 결과: \\(AB+C, Stack=[*,(]\\)
    9. 다음 +는 연산자이고, 스택의 peek이 여는 괄호이므로, 연산자 +를 push한다.
        - 결과: \\(AB+C, Stack=[*,(,+]\\)
    10. 다음 D는 피연산자이므로 출력한다.
        - 결과: \\(AB+CD, Stack=[*,(,+]\\)
    11. 닫는 괄호를  만났으므로 여는 괄호까지 pop을 수행한다.<br>(<u>이때, 괄호는 후위 표현식의 결과로 출력하지 않는다</u>)
        - 결과: \\(AB+CD+\\)
    12. 닫는 괄호 이후에는 <u>연산이 존재하지 않기 때문에 스택에 보관된 연산자 *를 pop</u>시킨다.
        - 결과: \\(AB+CD+*\\)

<br>

---

## <span style="color:navy">알고리즘의 설계<span>

- 위에서 설명된 스택을 이용하여 중위 표기법을
    
    후위 표기법으로 치환하는 알고리즘의 절차를 정리하면 다음과 같을 것이다.
    
    1. 중위 표현식을 왼쪽부터 한 글자씩 읽어들인다.
    2. 읽은 글자가
        - **if 여는괄호** → 여는괄호 push 수행
        - **elif 닫는 괄호** → 여는괄호가 나올 때까지 스택에서 pop 수행
        - **elif 피연산자** → 출력
        - **else 연산자** → 스택의 peek과 우선순위 비교
            - **if peek값 < 연산자** → 연산자 push 수행
            - **else peek값 ≥ 연산자** → pop 수행
    3. 읽을 글자가 없을 경우, 스택에 남아있는 모든 연산자 pop 수행

<br>

- 그리고 이를 코드로 구현화하면 다음과 같을 것이다.<br> (선형 배열 기반의 Stack 모듈에 대한 코드는 [이전](https://jhryu1208.github.io/data/2023/07/26/datastructure-stack/) 것을 이용한다.)
    
    ```python
    from utils_practice.stack import ArrayStack
    
    def IntoPost(S):
        opStack = ArrayStack()
    
        prec = {
            '*': 3, '/': 3,
            '+': 2, '-': 2,
            '(': 1
        }
    
        answer=''
    
        for s in S:
    
            # if 여는괄호 → 여는괄호 push 수행
            if s in '({[':
                opStack.push(s)
    
            # elif 닫는 괄호 → 여는괄호가 나올 때까지 스택에서 pop 수행
            elif s in ')}]':
                while opStack.peek() not in '({[':
                    answer+=opStack.pop()
                opStack.pop() # 여는괄호 pop
    
            # elif 피연산자 → 출력
            elif s not in prec:
                answer+=s
    
            # else 연산자 → 스택의 peek과 우선순위 비교 
            else:
                # 스택이 비어있을 경우 push진행
                if opStack.isEmpty() is True:
                    opStack.push(s)
    
                # if 
                elif prec[opStack.peek()] < prec[s]:
                    opStack.push(s)
    
                # else peek값 ≥ 연산자 → pop 수행
                else:
                    # 스택이 비어있을 경우 push진행 or 우선순위:peek값 < 연산자
                    if opStack.isEmpty() is True or prec[opStack.peek()] < prec[s]:
                        opStack.push(s)
                    else:
                        try:
                            # 우선순위가 낮은 peek값을 만날 때 까지 
                            while prec[opStack.peek()] >= prec[s]:
                                answer+=opStack.pop()
                        except:
                            # peek과 비교하였던 연산자를 스택에 추가
                            opStack.push(s)
    	
    
    		# 읽을 글자가 없을 경우, 스택에 남아있는 모든 연산자 pop 수행
        while opStack.isEmpty() is not True:
            answer+=opStack.pop()
    
        return  answer
    
    if __name__ == "__main__":
    
        ex1 = 'A*B+C'
        ex2 = 'A+B+C'
        ex3 = 'A+B*C'
        ex4 = 'A*(B+C)'
        ex5 = '(A+B)*(C+D)'
        ex6 = 'A+B*C-D'
    
        print(IntoPost(ex1)) # AB*C+
        print(IntoPost(ex2)) # AB+C+
        print(IntoPost(ex3)) # ABC*+
        print(IntoPost(ex4)) # ABC+*
        print(IntoPost(ex5)) # AB+CD+*
        print(IntoPost(ex6)) # ABC*+D-
    ```

<br>

---


## <span style="color:navy">References<span>
- [Programmers, 어서와! 자료구조와 알고리즘은 처음이지?](https://school.programmers.co.kr/learn/courses/57/57-%EC%96%B4%EC%84%9C%EC%99%80-%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0%EC%99%80-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98%EC%9D%80-%EC%B2%98%EC%9D%8C%EC%9D%B4%EC%A7%80)
