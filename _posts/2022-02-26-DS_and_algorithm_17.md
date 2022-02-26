---
layout: post
title: "Linked list 기본개념"
tags: [Python, 자료구조 알고리즘]
comments: true
---

.

Coding_test_training(20220226)

[학습자료]

패스트 캠퍼스 "알고리즘 / 기술면접 완전 정복 올인원 패키지 Online." 강의를 공부하고 정리한 내용입니다.

** URL : https://fastcampus.co.kr/dev_online_algo

[같이 참고해야하는 자료]

- Single Linked List 기초개념 및 구현실습

https://minman2115.github.io/single_linked_list

- Dummy Double Linked List 기초개념 및 구현실습

https://minman2115.github.io/dummy_double_linked_list


[학습내용]

- 링크드 리스트 정의하기


```python
class Node:
    def __init__(self, data, next=None):
        self.data = data
        self.next = next
        
node1 = Node(1)
node2 = Node(2)
node1.next = node2
head = node1
```

- 링크드 리스트에 데이터 넣기


```python
class Node:
    def __init__(self, data, next=None):
        self.data = data
        self.next = next
        
        
def add(data):
    node = head
    while node.next:
        node = node.next
    # 링크드 리스트 가장 마지막에 데이터를 넣게 된다.
    node.next = Node(data)
        
node3 = Node(1)
head = node3

# 인덱스가 2부터 시작해서 9까지 돈다.
for index in range(2,10):
    add(index)
```

- 데이터 넣을거를 출력해보기


```python
node = head
while node.next:
    # head를 제외한 모든 데이터 출력
    print(node.data)
    node = node.next
# 마지막 노드에 데이터 출력
print(node.data)
```

```text
    1
    2
    3
    4
    5
    6
    7
    8
    9
```

- 링크드 리스트 중간에 데이터 넣기


```python
# 2.5라는 노드의 데이터를 중간에 넣어보기
node4 = Node(2.5)

node = head
search = True
while search:
    # 데이터가 2인 곳을 찾기
    if node.data == 2:
        search = False
    else:
        node = node.next
        
node_next = node.next
node.next = node4
node4.next = node_next
```

- 중간에 데이터 넣은거를 출력해보기


```python
node = head
while node.next:
    # head를 제외한 모든 데이터 출력
    print(node.data)
    node = node.next
# 마지막 노드에 데이터 출력
print(node.data)
```

```text
    1
    2
    2.5
    3
    4
    5
    6
    7
    8
    9
```

- 위에서 실습한 링크드 리스트의 기본적인 기능들을 클래스로 구현해보기


```python
class Node:
    def __init__(self, data, next=None ):
        self.data = data
        self.next = next
    
class Node_mgmt:
    def __init__(self,data):
        self.head = Node(data)
    
    def add(self,data):
        if self.head == '':
            self.head = Node(data)
        else:
            node = self.head
            while node.next:
                node = node.next
            node.next = Node(data)
        
    def desc(self):
        node = self.head
        while node:
            print(node.data)
            node = node.next

linkedlist1 = Node_mgmt(0)
linkedlist1.desc()
```

```text
    0
```

- 데이터 넣고 출력해보기

```python
# 1부터 9까지 순회
for data in range(1,10):
    linkedlist1.add(data)
    
linkedlist1.desc()
```

```text
    0
    1
    2
    3
    4
    5
    6
    7
    8
    9
```

- 특정 위치의 노드를 삭제하는 기능도 만들어보기


```python
class Node:
    def __init__(self, data, next=None ):
        self.data = data
        self.next = next
    
class Node_mgmt:
    def __init__(self,data):
        self.head = Node(data)
    
    def add(self,data):
        if self.head == '':
            self.head = Node(data)
        else:
            node = self.head
            while node.next:
                node = node.next
            node.next = Node(data)
        
    def desc(self):
        node = self.head
        while node:
            print(node.data)
            node = node.next
            
    def delete(self, data):
        if self.head == '':
            print("node does not have value")
            return None
        
        # 특정위치의 노드를 삭제할때 case 1. 삭제하는 대상이 헤드인경우
        if self.head.data == data:
            temp = self.head
            self.head = self.head.next
            del temp
        
        # 특정위치의 노드를 삭제할때 case 2. 삭제하는 대상이 노드들의 중간에 위치하는 경우
        # 특정위치의 노드를 삭제할때 case 3. 삭제하는 대상이 마지막 노드일 경우
        else:
            node = self.head
            while node.next:
                if node.next.data == data:
                    temp = node.next
                    node.next = node.next.next
                    del temp
                    return None
                else:
                    node = node.next
```

- 특정위치의 노드를 삭제할때 case 1. 삭제하는 대상이 헤드인경우


```python
linkedlist2 = Node_mgmt(0)
linkedlist2.desc()

linkedlist2.head
```

```text
    <__main__.Node at 0x1101f8550>
```

```python
linkedlist2.delete(0)
linkedlist2.head
```

- 특정위치의 노드를 삭제할때 case 2. 삭제하는 대상이 노드들의 중간에 위치하는 경우


```python
linkedlist3 = Node_mgmt(0)

for data in range(1,10):
    linkedlist3.add(data)

linkedlist3.desc()
```

```text
    0
    1
    2
    3
    4
    5
    6
    7
    8
    9
```


```python
linkedlist3.delete(4)
linkedlist3.desc()
```

```text
    0
    1
    2
    3
    5
    6
    7
    8
    9
```

- 특정위치의 노드를 삭제할때 case 3. 삭제하는 대상이 마지막 노드일 경우


```python
linkedlist3.delete(9)
linkedlist3.desc()
```

```text
    0
    1
    2
    3
    5
    6
    7
    8
```

- 더블 링크드 리스트 만들어보기


```python
class Node:
    def __init__(self,data,prev=None,next=None):
        self.prev = prev
        self.data = data
        self.next = next
        
class Node_mgmt:
    def __init__(self,data):
        self.head = Node(data)
        self.tail = self.head
        
    def insert(self,data):
        if self.head == None:
            self.head = Node(data)
            self.tail = self.head
        else:
            node = self.head
            while node.next:
                node = node.next
            new = Node(data)
            node.next = new
            new.prev = node
            self.tail = new
            
    def desc(self):
        node = self.head
        while node:
            print(node.data)
            node = node.next
```


```python
double_linked_list = Node_mgmt(0)
for data in range(1,10):
    double_linked_list.insert(data)
    
double_linked_list.desc()
```

```text
    0
    1
    2
    3
    4
    5
    6
    7
    8
    9
```

- 특정 데이터를 헤드부터 순차적으로 검색해서 찾는 기능을 만들어보자

```python
class Node:
    def __init__(self,data,prev=None,next=None):
        self.prev = prev
        self.data = data
        self.next = next
        
class Node_mgmt:
    def __init__(self,data):
        self.head = Node(data)
        self.tail = self.head
        
    def insert(self,data):
        if self.head == None:
            self.head = Node(data)
            self.tail = self.head
        else:
            node = self.head
            while node.next:
                node = node.next
            new = Node(data)
            node.next = new
            new.prev = node
            self.tail = new
            
    def desc(self):
        node = self.head
        while node:
            print(node.data)
            node = node.next
            
    def search_from_head(self,data):
        if self.head == None:
            return False
        
        node = self.head
        while node:
            if node.data == data:
                return node
            else:
                node = node.next
        return False
```


```python
double_linked_list = Node_mgmt(0)
for data in range(1,10):
    double_linked_list.insert(data)
    
double_linked_list.desc()
```

```text
    0
    1
    2
    3
    4
    5
    6
    7
    8
    9
```

```python
node3 = double_linked_list.search_from_head(3)
if node3:
    print(node3.data)
else:
    print("no data")
```

```text
    3
```

- 특정 데이터를 테일부터 순차적으로 검색해서 찾는 기능을 만들어보자

```python
class Node:
    def __init__(self,data,prev=None,next=None):
        self.prev = prev
        self.data = data
        self.next = next
        
class Node_mgmt:
    def __init__(self,data):
        self.head = Node(data)
        self.tail = self.head
        
    def insert(self,data):
        if self.head == None:
            self.head = Node(data)
            self.tail = self.head
        else:
            node = self.head
            while node.next:
                node = node.next
            new = Node(data)
            node.next = new
            new.prev = node
            self.tail = new
            
    def desc(self):
        node = self.head
        while node:
            print(node.data)
            node = node.next
            
    def search_from_head(self,data):
        if self.head == None:
            return False
        
        node = self.head
        while node:
            if node.data == data:
                return node
            else:
                node = node.next
        return False
            
    def search_from_tail(self,data):
        if self.head == None:
            return False
        
        node = self.tail
        while node:
            if node.data == data:
                return node
            else:
                node = node.prev
        return False
```


```python
double_linked_list = Node_mgmt(0)
for data in range(1,10):
    double_linked_list.insert(data)
    
double_linked_list.desc()
```

```text
    0
    1
    2
    3
    4
    5
    6
    7
    8
    9
```


```python
node3 = double_linked_list.search_from_tail(3)
if node3:
    print(node3.data)
else:
    print("no data")
```

```text
    3
```

- 임의의 어떤 데이터를 특정위치에 삽입하는 기능을 만들어보자

```python
class Node:
    def __init__(self,data,prev=None,next=None):
        self.prev = prev
        self.data = data
        self.next = next
        
class Node_mgmt:
    def __init__(self,data):
        self.head = Node(data)
        self.tail = self.head
        
    def insert(self,data):
        if self.head == None:
            self.head = Node(data)
            self.tail = self.head
        else:
            node = self.head
            while node.next:
                node = node.next
            new = Node(data)
            node.next = new
            new.prev = node
            self.tail = new
            
    def desc(self):
        node = self.head
        while node:
            print(node.data)
            node = node.next
            
    def search_from_head(self,data):
        if self.head == None:
            return False
        
        node = self.head
        while node:
            if node.data == data:
                return node
            else:
                node = node.next
        return False
            
    def search_from_tail(self,data):
        if self.head == None:
            return False
        
        node = self.tail
        while node:
            if node.data == data:
                return node
            else:
                node = node.prev
        return False
    
    def insert_before(self,data,before_data):
        if self.head == None:
            self.head = Node(data)
            return True
        
        else:
            node = self.tail
            while node.data != before_data:
                node = node.prev
                if node == None:
                    return False
            
            new = Node(data)
            before_new = node.prev
            before_new.next = new
            new.prev = before_new
            new.next = node
            node.prev = new
            return True
```


```python
double_linked_list = Node_mgmt(0)
for data in range(1,10):
    double_linked_list.insert(data)
    
double_linked_list.desc()
```

```text
    0
    1
    2
    3
    4
    5
    6
    7
    8
    9
```

```python
double_linked_list.insert_before(1.5,2)
double_linked_list.desc()
```

```text
    0
    1
    1.5
    2
    3
    4
    5
    6
    7
    8
    9
```

```python
node3 = double_linked_list.search_from_tail(1.5)
if node3:
    print(node3.data)
else:
    print("no data")
```

```text
    1.5
```