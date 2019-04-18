---
layout: post
title: "자료구조 개요 및 기본자료구조"
tags: [Python, 자료구조 알고리즘]
comments: true
---

.

#### # 학습시 참고 URL : https://github.com/ythwork

#### [자료구조]


- 데이터가 선형으로 저장되어 있는가 아닌가에 따라 두가지로 나뉘어진다.


1) 선형구조

배열, 연결리스트, 스택, 큐
    
    
2) 비선형구조

트리(바이너리 서치트리, B-tree 등), 그래프, 해시테이블, 힙

참고로 데이터베이스가 B-tree 자료구조로 구성된다.

#### [기본자료구조]


1) 스텍

스텍의 응용사례 : 후위표기법 계산기
   
2) 큐

큐와 리스트는 일시적인 데이터를 처리할때 많이 쓴다.

데이터를 오래 저장하고 활용하고자 할 때는 링크드리스트, 배열, 트리 등을 많이 쓴다.

3) 해시테이블

#### [stack]

- 접시쌓기, 급식판 !!


- 스택에서는 가장 최근에 삽입된 원소가 삭제된다. 스택은 후입선출(LIFO) 정책을 구현한 것이다.

LIFO : Last In, First Out


- 스택에서 삽입 연산은 Push, 삭제 연산은 Pop이라 불린다.


- 비어있는 스택에서 원소를 추출하려고 할 때 stack underflow라고 하며, 스택이 넘치는 경우 stack overflow 라고 한다.


- 활용예시 : 후입선출(LIFO)의 특징을 활용하여 여러 분야에서 활용 가능하다.

1) 웹 브라우저 방문기록 (뒤로가기)

2) 실행취소 (undo)

3) 역순 문자열 만들기

4) 수식의 괄호 검사 (연산자 우선순위 표현을 위한 괄호 검사)
    
5) 후위표기법 계산

#### [자료구조의 구성]

자료구조를 만들때 고민할 내용으로 통상 ADT(Abstract Data Type) : 추상 데이터 타입 라고 한다.

데이터를 삽입,탐색, 삭제하는 자료구조의 메서드의 함수사용법(함수 시그니처)

함수의 인터페이스와 유사하다

1) insert : 데이터를 어떻게 저장할 것인가

2) search : 데이터를 어떻게 탐색할 것인가

3) delete : 데이터를 어떻게 지울 것인가

자료구조를 만들때는 항상 먼저 ADT를 구상하고 구현해야 한다.

#### 파이썬의 리스트를 활용해서 stack을 구현해보기

- 파이썬의 내부 자료구조인 스텍을 활용해서 컨테이너라는 것을 만들어서 우리가 구현하고자하는 스텍을 구현한다.


- stack의 ADT

S.empty() : Boolean을 반환하는데 스택이 비어있으면 True, 아니면 False를 반환한다.

S.push(data) : 반환하지 않으며 스택의 맨위에 데이터를 쌓아준다.

S.pop() : 스택 맨위의 data를 삭제하면서 반환하게 된다.

S.peek() : 스텍의 맨 위의 data를 반환해준다.


- 위의 개념을 반영해서 self.container=list() 로 초기화 하는것을 통합기법이라고 한다.


- push 작동예시

![1](https://user-images.githubusercontent.com/41605276/56192210-e072dc80-6068-11e9-8b2f-85fe139c9dc9.png)

- pop 작동예시

![2](https://user-images.githubusercontent.com/41605276/56192230-e7015400-6068-11e9-9a20-97200a09fe54.png)

- 파이썬 코드구현


```python
class Stack:
    def __init__(self):
        self.container=list() 
        ## 컨테이너는 실제 데이터를 가지고 있는 자료구조
        
    def empty(self):
        ## 파이썬에서는 빈 리스트를 False로 인식한다.
        if not self.container:
            return True
        else :
            return False

    def push(self, data):
        self.container.append(data)

    def pop(self):
        return self.container.pop()

    def peek(self):
        return self.container[-1]

if __name__=="__main__":
    s=Stack()
    s.push(1)
    s.push(2)
    s.push(3)
    s.push(4)
    s.push(5)
    while not s.empty():
        print(s.pop(), end='  ')
```

    5  4  3  2  1  

#### 파이썬의 배열(single linked list)을 활용해서 구현

- push 작동예시

![3](https://user-images.githubusercontent.com/41605276/56192257-f4b6d980-6068-11e9-9a8c-59d6cb45b5fb.png)

- pop 작동예시

![4](https://user-images.githubusercontent.com/41605276/56192265-faacba80-6068-11e9-8841-5475dc616c46.png)

- 파이썬 코드구현


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

class LStack:
    def __init__(self):
        self.top=None

    def empty(self):
        if self.top is None:
            return True
        else:
            return False

    def push(self, data):
        new_node = Node(data)
        new_node.next = self.top
        self.top = new_node

    def pop(self):
        if self.empty():
            return None
        cur = self.top
        self.top = self.top.next
        return cur.data
        ## cur는 지역변수로 함수실행이 종료되면 없어지기 때문에 팝하고자하는 데이터도 함수실행이 종료되면 레퍼런스
        ## 카운트가 0이되면서 없어지게 된다.

    def peek(self):
        if self.empty():
            return None
        return self.top.data

if __name__ =="__main__":
    s = LStack()

    s.push(1)
    s.push(2)
    s.push(3)
    s.push(4)
    s.push(5)

    while not s.empty():
        print(s.pop(), end="  ")
```

    5  4  3  2  1  

#### [Queue]

- 큐 에서는 집합에서 가장 오랜 시간 존재했던 원소를 삭제한다. 큐는 선입선출(FIFO) 정책을 구현한 것이다.

![5](https://user-images.githubusercontent.com/41605276/56192291-039d8c00-6069-11e9-8c0e-98360b00bd1e.png)

- 큐에서 삽입 연산은 Enqueue, 삭제연산은 Dequeue라 한다.


- 스택에서는 원소의 삽입, 삭제가 스택의 한 끝에서 이루어지고, 큐에서는 원소의 삽입, 삭제가 큐의 서로 다른 쪽 끝에서 이루어진다.


- 양방향 큐, 다시말해 데크(deque) 라는 것도 있는데 이것은 원소의 삽입, 삭제가 양쪽 방향에서 이루어진다.

(큐와 스택을 합친 것으로 선입선출/후입선출의 복합적인 성격이 필요한 경우에 사용)


- 활용예시 :선입선출(FIFO)의 특징을 활용한 활용예시

1) 우선순위가 같은 작업 예약 (인쇄 대기열)

2) 선입선출이 필요한 대기열 (티켓 카운터)

3) 콜센터 고객 대기시간

- ADT 

Q.empty() : Boolean을 반환하는데 큐가 비어있으면 True, 아니면 False를 반환한다.

Q.enqueue(data) : 반환하지 않으며 큐의 맨 뒤에 데이터를 쌓아준다.

Q.dequeue() : 큐 맨 앞의 데이터를 삭제하면서 그 데이터를 반환한다.

Q.peek() : 큐 맨 앞의 데이터를 반환한다.

#### 파이썬의 리스트를 활용해서 구현

- Enqueue 작동예시

![6](https://user-images.githubusercontent.com/41605276/56192322-0dbf8a80-6069-11e9-90d8-fb2de43dc069.png)

- Dequeue 작동예시

![7](https://user-images.githubusercontent.com/41605276/56192336-131cd500-6069-11e9-9bfd-1f386dc5a42a.png)

- 파이썬 코드 구현


```python
class Queue:
    def __init__(self):
        self.container=list()

    def empty(self):
        if not self.container:
            return True
        else :
            return False

    def enqueue(self, data):
        self.container.append(data)

    def dequeue(self):
        return self.container.pop(0)

    def peek(self):
        return self.container[0]

if __name__=="__main__":
    s=Queue()
    s.enqueue(1)
    s.enqueue(2)
    s.enqueue(3)
    s.enqueue(4)
    s.enqueue(5)
    while not s.empty():
        print(s.dequeue(), end='  ')
```

    1  2  3  4  5  

#### 파이썬 배열(single linked list)을 활용해서 구현

- Enqueue 작동예시

![8](https://user-images.githubusercontent.com/41605276/56192375-27f96880-6069-11e9-95f8-753ae9d8f093.png)

- Dequeue 작동예시

![9](https://user-images.githubusercontent.com/41605276/56192387-2def4980-6069-11e9-896c-91690fe44b10.png)

- 파이썬 코드구현


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

class LQueue:
    def __init__(self):
        self.front = None
        self.rear = None

    def empty(self):
        if not self.front:
            return True
        return False

    def enqueue(self, data):
        new_node = Node(data)
        if self.empty():
            self.front = new_node
            self.rear = new_node
            return

        self.rear.next = new_node
        self.rear = new_node

    def dequeue(self):
        if self.empty():
            return None
            ## None으로 리턴해도 되고 아니면 애러레이징해도 된다.

        if self.front is self.rear:
            self.rear = self.rear.next
            
        cur = self.front
        self.front = self.front.next
        return cur.data

    def peek(self):
        if self.empty():
            return None
        
        return self.front.data

if __name__ == "__main__":
    q = LQueue()
    q.enqueue(1)
    q.enqueue(2)
    q.enqueue(3)
    q.enqueue(4)
    q.enqueue(5)

    while not q.empty():
        print(q.dequeue(), end='  ')
```

    1  2  3  4  5  

#### 스텍 2개를 이용해서 구현

- 스택 2개를 이용해서 마치 큐를 쓰는 것처럼 구현하기


```python
#implementation of queue with two stacks
from stack1 import LStack

class SQueue:
    def __init__(self):
        self.first = LStack()
        self.second = LStack()

    def empty(self):
        if self.first.empty() and self.second.empty():
            return True
        return False

    def enqueue(self, data):
        self.first.push(data)
        
    ## 유저 프로그래머에게 공개 안하고 내부 클래스로 처리할 경우 앞에  __붙여준다.
    def __shift_stack(self):
        if self.empty():
            return None
        
        if self.second.empty():
            while not self.first.empty():
                self.second.push(self.first.pop())

    def dequeue(self):
        self.__shift_stack()
        return self.second.pop()

    def peek(self):
        self.__shift_stack()
        return self.second.peek()

if __name__ == "__main__":
    q= SQueue()
    q.enqueue(1)
    q.enqueue(2)
    q.enqueue(3)
    q.enqueue(4)
    q.enqueue(5)

    while not q.empty():
        print(q.dequeue(), end= '  ')
```

    1  2  3  4  5  
