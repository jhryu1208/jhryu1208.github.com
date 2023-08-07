---
layout: post
title: "[자료구조] 10. 스택의 응용: 후위 표기 수식 계산"
subtitle: "[자료구조] 10. 스택의 응용: 후위 표기 수식 계산"
categories: data
tags: datastructure
comments: true
---
#### Contents
- [후위 표기 수식 계산 with 스택](#후위-표기-수식-계산-with-스택)


<br>

---

## <span style="color:navy">후위 표기 수식 계산 with 스택<span>

- 스택을 응용하여 중위 표현식을 후위 표현식으로 전환할 뿐만 아니라, 후위 표현식 계산을 수행할 수 있다. 전반적인 알고리즘은 다음과 같다.
    1. 중위 표현식을 후위 표현식으로 전환할 때와 동일하게 좌에서 우로 표현식을 읽는다.
    2. 피연산자를 읽었을 경우, 피연산자를 스택에 push를 수행한다.
    3. 연산자를 읽었을 경우, 연산자에 적용될 두 피연산자들을 반환하기 위해 스택에 내제된  2번의 pop작업을 수행한다.그리고 두 피연산자들을 사용하여 연산을 수행한 결과를 다시 스택에 push한다.
        - 이때, 뺄셈과 나눗셈 등은 연산의 순서에 따라 결과가 달라지므로,<br>
        연산을 수행할 때에는 주의해야한다.
    4. 더 이상 처리할 피연산자 혹은 연산자가 남지 않을 때 까지 위의 과정을 반복한다.<br>반복이 완료된 이후 스택에 남아 있는 최종 결과 값을 반환한다.

<br>

- 다음은 \\((A+B) * (C+D)\\)의 후위 표현식인 \\(AB + CD + * \\)를 스택 알고리즘을 이용하여 계산한 과정이다.
    1. \\(A\\)의 경우 피연산자이므로 스택에 push한다.
        
        결과: \\(Stack= [A]\\)
        
    2. 다음 \\(B\\)의 경우 피연산자이므로 스택에 push한다.
        
        결과: \\(Stack = [A, B]\\)
        
    3. 다음 \\(+\\)의 경우 연산자이므로 스택에 저장된 \\(B\\)와 \\(A\\)를 차례대로 pop하여 \\(+\\) 연산을 수행한다. 그리고, 수행된 결과값인 \\((A+B)\\)를 스택에 push한다.
        
        결과: \\(Stack =[(A+B)]\\)
        
    4. 다음 \\(C\\)의 경우 피연산자이므로 스택에 push한다.
        
        결과: \\(Stack = [(A+B), C]\\)
        
    5. 다음 \\(D\\)의 경우 피연산자이므로 스택에 push한다.
        
        결과:\\(Stack = [(A+B), C, D]\\)
        
    6. 다음 \\(+\\)의 경우 연산자이므로 스택에 저장된 \\(D\\)와 \\(C\\)를 차례대로 pop하여 \\(+\\) 연산을 수행한다. 그리고, 수행된 결과값인 \\((C+D)\\)를 스택에 push한다.
        
        결과: \\(Stack = [(A+B), (C+D)]\\)
        
    7. 다음 \\( * \\) 의 경우 연산자이므로 스택에 저장된 \\((C+D)\\)와 \\((A+B)\\)를 차례대로 pop하여 \\( * \\)연산을 수행한다. 그리고, 수행된 결과값인 \\((A+B)*(C+D)\\)를 스택에 push한다.
        
        결과: \\(Stack = [(A+B)*(C+D)]\\)
        
    8. 읽어드릴 연산자 혹은 피연사자가 없기 때문에 스택에 저장된 최종 결과값을 반환한다.
        
        결과: \\((A+B)*(C+D)\\)

<br>

- 다음은 위의 과정을 구현화한 함수이다. 
함수내에 인풋된 파라미터는 후외 표현식을 list화 시킨 변수를 의미한다.
    
    ```python
    from utils_practice.stack import ArrayStack
    
    def postfixAgg(tokenList):
    
        postStack = ArrayStack()
    
        for token in tokenList:
    
            # Stack이 비어있거나, 연산자가 아닐 경우
            if postStack.isEmpty() is True or str(token) not in "+-/*":
                postStack.push(token)
            # 연산자일 경우
            else:
                var1 = postStack.pop()
                var2 = postStack.pop()
                # var1과 var2의 연산값을 스택에 push
                eval(f"postStack.push(var2 {token} var1)")
    	
        # 루프 종료 후, 스택에 저장된 최종 결과값 반환
        return postStack.pop()
    
    if __name__ == '__main__':
    
        ex1 = [5, 3, '+']
        ex2 = [7, 9, 3, 2, '+', '-', '*']
    
        print(postfixAgg(ex1)) # 8
        print(postfixAgg(ex2)) # 28
    ``` 


<br>

---


## <span style="color:navy">References<span>
- [Programmers, 어서와! 자료구조와 알고리즘은 처음이지?](https://school.programmers.co.kr/learn/courses/57/57-%EC%96%B4%EC%84%9C%EC%99%80-%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0%EC%99%80-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98%EC%9D%80-%EC%B2%98%EC%9D%8C%EC%9D%B4%EC%A7%80)
