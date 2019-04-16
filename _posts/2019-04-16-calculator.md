---
layout: post
title: "후위표기법 계산기 구현실습"
tags: [Python, 자료구조 알고리즘]
comments: true
---

.

#### # 학습시 참고 URL : https://github.com/ythwork

- 후위표기법 계산기 구현을 위한 stack 클래스 구현


```python
class Node:
    def __init__(self, data=None):
        self.__data=data
        self.__next=None

    @property
    def data(self):
        return self.__data
    
    @data.setter
    def data(self, data):
        self.__data=data
    
    @property
    def next(self):
        return self.__next

    @next.setter
    def next(self, n):
        self.__next=n

class stack:
    def __init__(self):
        self.top=None

    def empty(self):
        if self.top is None:
            return True
        else:
            return False

    def push(self, data):
        new_node = Node(data)
        if self.empty():
            self.top = new_node
            return
        new_node.next = self.top
        self.top=new_node

    def pop(self):
        if self.empty():
            return None
        cur = self.top
        self.top = self.top.next
        return cur.data

    def peek(self):
        if self.empty():
            return None
        return self.top.data

if __name__ =="__main__":
    s = stack()

    s.push(1)
    s.push(2)
    s.push(3)
    s.push(4)
    s.push(5)

    while not s.empty():
        print(s.pop(), end="  ")
```

    5  4  3  2  1  

- stack 클래스를 이용한 후위표기 계산기 구현


```python
class Calculator:
    def __init__(self):
        self.org_exp=None
        self.postfix_exp=None
        self.result=None
    
    def set_org_exp(self, org_exp):
        self.org_exp=org_exp.replace(' ', '')
        ## org_exp 중위표기법 
        ## ==> 유저에게 입력 받으며 공백을 삭제하여 저장한다

        self.postfix_exp=None
        ## postfix_exp 후위표기법 
        ## ==> 유저에게 입력 받은 중위 표기법을 후위 표기법으로 변경하여 저장한다.

        self.result=None

    def get_org_exp(self):
        return self.org_exp

    def get_weight(self, oprt):
    ## 인자로 받은 연산자의 가중치를 리턴한다.
    ## 가중치 순서 : *, / > +, - > (
        if oprt=='*' or oprt=='/':
            return 9
        elif oprt=='+' or oprt=='-':
            return 7
        elif oprt=='(':
            return 5

    def convert_to_postfix(self):
    ## 인스턴스 변수 org_exp를 후위표기법으로 변경하여 postfix_exp 변수에 저장한다.
    ## 가중치에 따라서 연산자를 담을 스택과, 최종 후위 수식을 담을 리스트를 활용한다.
        exp_list=[]
        oprt_stack=stack()

        for ch in self.get_org_exp(): ## get_orp_exp()에서 입력받은 것들을 for문으로 돌아라
            if ch.isdigit(): ## 숫자라면 exp_list에 넣어주고
                exp_list.append(ch)
            else: ## 연산자 문자열이라면 
                if oprt_stack.empty() or ch=='(': ## 스텍안이 비어있거나 ( 라면
                    oprt_stack.push(ch) ## oprt_stack에 push해라
                elif ch==')': ## ) 라면
                    op=oprt_stack.pop() ## oprt_stack에서 pop해라
                    while op!='(': ## ( 가 나오기 전까지 계속해서
                        exp_list.append(op) ## exp_list에 넣어주고
                        op=oprt_stack.pop() ## oprt_stack에서 pop해라
                else: ## + - / 이라면
                    if self.get_weight(ch) > self.get_weight(oprt_stack.peek()):
                         ## 피크로 확인해서 가중치를 서로 비교해서 새로 가져온게 oprt_stack.peek()보다크면
                         ## oprt_stack으로 새로 가져온 것을 push해라
                        oprt_stack.push(ch)
                    else: ## oprt_stack.peek()보다 가중치가 작거나 같으면
                        while not oprt_stack.empty() and self.get_weight(ch) <= self.get_weight(oprt_stack.peek()):
                            exp_list.append(oprt_stack.pop())
                        oprt_stack.push(ch)
        while not oprt_stack.empty():
            exp_list.append(oprt_stack.pop())

        self.postfix_exp=''.join(exp_list) ## postfix_exp에 문자열로 join해서 넣어라


    def get_postfix_exp(self):
    ## postfix_exp 변수에 담긴 값을 리턴한다. 없다면 convert_to_postfix()을 실행한다.
        if not self.org_exp:
            return None

        if not self.postfix_exp:
            self.convert_to_postfix()

        return self.postfix_exp

    def calc_two_oprd(self, oprd1, oprd2, oprt):
    ## 연산자의 종류에 따라서 연산을 진행한 결과를 리턴한다.
        if oprt=='+':
            return oprd1+oprd2
        elif oprt=='-':
            return oprd1-oprd2
        elif oprt=='*':
            return oprd1*oprd2
        elif oprt=='/':
            return oprd1//oprd2

    def calculate(self):
    ## postfix_exp 변수에 담긴 후위수식 값을 연산한 결과를 리턴한다.
        oprd_stack=stack()
        for ch in self.get_postfix_exp():
            if ch.isdigit():
                oprd_stack.push(int(ch))
            else :
                oprd2=oprd_stack.pop()
                oprd1=oprd_stack.pop()
                oprd_stack.push(self.calc_two_oprd(oprd1, oprd2, ch))
        self.result=oprd_stack.pop()

    def get_result(self):
        if not self.org_exp:
            return None
        
        if not self.result:
            self.calculate()
        
        return self.result

if __name__=="__main__":
    calc=Calculator()

    while True:
        exp=input("수식을 입력하세요 (종료:0):")
        if exp=='0':
            break
        calc.set_org_exp(exp)
        print(calc.get_postfix_exp())
        print('{exp}={result}'.format(exp=calc.get_org_exp(), result=calc.get_result()))
```

    수식을 입력하세요 (종료:0):2+4
    24+
    2+4=6
    수식을 입력하세요 (종료:0):9-7
    97-
    9-7=2
    수식을 입력하세요 (종료:0):5*4
    54*
    5*4=20
    수식을 입력하세요 (종료:0):8/4
    84/
    8/4=2
    수식을 입력하세요 (종료:0):0
    
