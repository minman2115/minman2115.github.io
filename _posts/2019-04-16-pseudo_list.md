---
layout: post
title: "pseudo_list 구현실습"
tags: [Python, 자료구조 알고리즘]
comments: true
---

.

#### # 학습시 참고 URL : https://github.com/ythwork

#### 파이썬의 리스트를 더미더블링크드 리스트로 구현하기

- 만든 더미더블 링크드 리스트를 쓰다가 내가 짠 자료구조를 변경하고자 할때 코드를 튜닝할 수도 있다.

예를 들어서 파이썬의 리스트에 치명적인 오류가 생겨서 리스트를 당분간 쓰지말라는 말도안되는 파이썬 공식문서의 공지가 떴을때

그런데 나는 리스트를 그대로 쓰고 싶다 ! 이럴때

from pseudo_list import PseudoList

li = list() 요렇게 되어 있던 기존에 리스트를

li = PseudoList() 아래와 같은 코드로 대체해서 기존에 리스트처럼 쓰고자 할때


- 인터페이스는 그대로 유지하지만 내부 구현만 대체하고자 할때 튜닝이 가능하다.


- 실습할 pseudo_list는 interface는 파이썬의 list를 이용할 것이고, implementation은 dummy double linked list이다.


- PseudoList 구현을 위한 dummy_double_linked_list 구현


```python
class Node:
    def __init__(self, data = None):
        self.__data = data
        self.__next = None
        self.__before = None
        
    def __del__(self):
        print("data of {} is deleted".format(self.data))
        
    @property
    def data(self):
        return self.__data
    
    @data.setter
    def data(self,data):
        self.__data = data
        
    @property
    def next(self):
        return self.__next
    
    @next.setter
    def next(self, next):
        self.__next = next
        
    @property
    def before(self):
        return self.__before
    
    @before.setter
    def before(self,before):
        self.__before = before

class DummyDoubleLinkedList:
    def __init__(self):
        self.head = Node()
        self.tail = Node()
        self.head.next = self.tail
        self.tail.before = self.head
        self.d_size = 0
        
    def empty(self):
        if self.d_size == 0:
            return True
        else:
            return False
        
    def size(self):
        return self.d_size
    
    ## 삽입 관련된 코드는
    ## 코드의 순서가 중요하며 뉴 노드에 대한 next와 before를 먼저 설정한다.
    
    def add_first(self, data):
        new_node=Node(data)
        
        new_node.next=self.head.next
        new_node.before=self.head

        self.head.next.before=new_node
        self.head.next=new_node

        self.d_size+=1

    def add_last(self, data):
        new_node=Node(data)

        new_node.before=self.tail.before
        new_node.next=self.tail

        self.tail.before.next=new_node
        self.tail.before=new_node

        self.d_size+=1

    def insert_after(self, data, node):
        new_node = Node(data)

        new_node.next = node.next
        new_node.before = node

        node.next.before = new_node
        node.next = new_node

        self.d_size+=1

    def insert_before(self, data, node):
        new_node = Node(data)

        new_node.before = node.before
        new_node.next = node

        node.before.next = new_node
        node.before = new_node

        self.d_size+=1
    
    def search_forward(self, target):
        
        cur = self.head.next
        
        ## 참고로 is not 과 == 는 다르다
        ## == 는 값이 같은가에 관한것이고
        ## is는 같은 메모리 공간인가에 관한 것이다.
        ## 여기서는 next가 메모리 공간을 가리키는 것에 관한것이어서 is를 쓴것이다.
        while cur is not self.tail:
            if cur.data == target:
                return cur
            cur = cur.next
            
        return None
    
    def search_backward(self, target):
        
        cur = self.tail.before
        
        while cur is not self.head:
            if cur.data == target:
                return cur
            cur = cur.before        
        return None
    
    ## 써치 함수는 아래와 같이 구현할 수도 있다.
    ## def search(self, data, start=True):
    ##   if start :
    ##     cur=self.head.next
    ##     while cur is not self.tail:
    ##         if data == cur.data:
    ##             return cur
    ##          cur=cur.next
    ##      return None
    ##   else :
    ##      cur=self.tail.before
    ##      while cur is not self.head:
    ##          if data == cur.data:
    ##              cur=cur.before
    ##      return None
    
    def delete_first(self):
        if self.empty():
            return
        self.head.next=self.head.next.next
        self.head.next.before=self.head

        self.d_size-=1
            
    def delete_last(self):
        if self.empty():
            return
        self.tail.before=self.tail.before.before
        self.tail.before.next=self.tail

        self.d_size-=1
    
    def delete_node(self, node):
        
        del_node = node
        
        node.before.next = node.next
        node.next.before = node.before
        
        self.d_size -= 1
        
        return del_node
    
    def traverse(self, start=True):
        
        ## start=True --> from head
        ## start=False --> from tail
        
        if start:
            cur=self.head.next
            while cur is not self.tail:
                yield cur
                cur=cur.next
        else:
            cur=self.tail.before
            while cur is not self.head:
                yield cur
                cur=cur.before


def show_list(dlist, start=True):
    print('data size : {}'.format(_list.size()))
    nodes = _list.traverse(start)
    for node in nodes:
        print(node.data, end='  ')
    print()
```

- dummy_double_linked_list를 이용한 수도리스트 구현


```python
class PseudoList(DummyDoubleLinkedList):
    def __init__(self, *args): ## *args(가변인자)에 리스트를 넣어준다.
        super().__init__() ## 부모클래스의 이닛 함수를 실행한다.
        for elem in args: ## 인자로 넣어준 리스트를 받아서 튜플로 읽어오는데 이 엘리먼트 별로 에드 라스트를 해준다.
            self.add_last(elem)

    def __len__(self): ## __len__은 자동으로 데이터가 들어오고 나가면 size를 보여준다.
        return self.size()

    def append(self, data): ## warpping함수
        self.add_last(data)

    def __find_position(self, pos): 
        ## 함수이름에도 __를 앞에 붙어주면 외부에서 접근이 안된다.(인포메이션 하이딩된 함수다)
        ## 리스트로 보면 인덱스라고 보면 된다. 포지션이 이름만 다르지 인덱스랑 같은 개념이다.
        ## pos(포지션)에다가 0을 넣으면 노드 자체를 반환하게 구현되어 있다.
        if pos >=self.size():
            raise IndexError('list index out of range')
        cur = self.head.next
        for _ in range(pos):
            cur = cur.next
        return cur

    def insert(self, pos, data): 
        ## 리스트의 인서트 함수를 구현하는 코드다.
        ## 예를들어서 li.insert(2,7)이라고 하면 2번째 인덱스 위치에 7을 넣는 코드다.
        ## find포지션이라는 함수를 이용해서 노드번호를 반환받고 그 노드 앞에 insert_before를 구현한다.
        cur = self.__find_position(pos)
        self.insert_before(data, cur)

    def count(self, data):
        ## 인자에 데이터를 넣으면 노드를 하나씩 순회하면서 인자에 넣은 데이터와 동일한게 노드중에 있으면
        ## 그 갯수를 반환한다.
        count = 0
        cur = self.head.next
        while cur is not self.tail:
            if cur.data == data:
                count += 1
            cur = cur.next
        return count
        
    def index(self, data, start=0):
        ## 데이터를 입력하면 인덱스를 반환하는 함수이다.
        ## 리스트함수에서 li.index(7) 인덱스가 반환되는 것을 구현하고자 한다.
        ## 스타트는 노드의 위치
        ## 리스트에서는 찾고자 하는 데이터가 없다면 원래는 인덱스 애러를 반환한다. 이것도 구현하고자 한다.
        cur = self.__find_position(start)
        index = start
        
        while cur is not self.tail:
            if cur.data == data:
                return index
            cur = cur.next
            index+=1
            
        raise ValueError('{} is not in the list'.format(data))

    def __getitem__(self, index):
        ##print(li[3])처럼 [] 연산자를 통해 값을 가져올때 내부에서 호출
        ## index는 pos를 의미
        ## index에 위치한 노드의 데이터를 반환
        
        node = self.__find_position(index)
        return node.data
    
    def __setitem__(self, index, data):
        ## li[3]=10처럼 [] 연산자로 값을 대입할때 내부에서 호출
        ## index에 위치한 노드의 값을 data로 바꿈
        
        node = self.__find_position(index)
        node.data = data

    def pop(self, pos=None):
        # 인자가 pos가 있는경우
        if pos == True:
            node = self.__find_position(pos)
        
        # 인자가 pos가 없는 경우
        else:        
            node = self.tail.before
        
        cur = node
        self.delete_node(node)
        return cur.data 
        
    def remove(self, data):
        self.delete_node(self.search_forward(data))

    def __str__(self):
        string = '['
        cur = self.head.next
        while cur is not self.tail:
            string+=str(cur.data)
            if cur.next is not self.tail:
                string+=', '
            cur= cur.next

        string+=']'
        return string
```

- 객체 생성 동작테스트 


```python
initial = [1, 2, 3, 4]
#pseudo_list
li = PseudoList(*initial)
#python list
py_li = initial

print("pseudo list : "+ str(li))
print("python list : " + str(py_li))
```

    pseudo list : [1, 2, 3, 4]
    python list : [1, 2, 3, 4]
    

- append 테스트


```python
#pseudo_list
li.append(2)
li.append(1)
li.append(2)
li.append(7)
#python_list
py_li.append(2)
py_li.append(1)
py_li.append(2)
py_li.append(7)

print("pseudo list : "+ str(li))
print("python list : " + str(py_li))
```

    pseudo list : [1, 2, 3, 4, 2, 1, 2, 7]
    python list : [1, 2, 3, 4, 2, 1, 2, 7]
    

- count 테스트


```python
target = 2
print('count of {} : {} in pseudo_list'.format(target, li.count(target)))
print('count of {} : {} in python_list'.format(target, py_li.count(target)))
```

    count of 2 : 3 in pseudo_list
    count of 2 : 3 in python_list
    

- pop 테스트


```python
#pseudo_list
li.pop(); li.pop()
#python_list
py_li.pop(); py_li.pop()

print("pseudo list : "+ str(li))
print("python list : " + str(py_li))
```

    data of 7 is deleted
    data of 2 is deleted
    pseudo list : [1, 2, 3, 4, 2, 1]
    python list : [1, 2, 3, 4, 2, 1]
    

- pop(index) 테스트


```python
#pseudo_list
li.pop(2)
#python_list
py_li.pop(2)

print("pseudo list : "+ str(li))
print("python list : " + str(py_li))
```

    data of 1 is deleted
    pseudo list : [1, 2, 3, 4, 2]
    python list : [1, 2, 4, 2, 1]
    

- insert 테스트


```python
#pseudo_list
li.insert(3, 9)
#python_list
py_li.insert(3, 9)

print("pseudo list : "+ str(li))
print("python list : " + str(py_li))
```

    pseudo list : [1, 2, 3, 9, 4, 2]
    python list : [1, 2, 4, 9, 2, 1]
    

- index 테스트


```python
target = 9

print("index of {} : {} in pseudo_list".format(target, li.index(target)))
print("index of {} : {} in python_list".format(target, py_li.index(target)))
```

    index of 9 : 3 in pseudo_list
    index of 9 : 3 in python_list
    

- indexing 테스트


```python
#pseudo_list
li[3]=7
#python_list
py_li[3]=7

print("pseudo list : "+ str(li))
print("python list : " + str(py_li))
```

    pseudo list : [1, 2, 3, 7, 4, 2]
    python list : [1, 2, 4, 7, 2, 1]
    


```python
#pseudo_list
for i in range(len(li)):
    print(li[i], end='  ')
```

    1  2  3  7  4  2  


```python
#python_list
for i in range(len(py_li)):
    print(py_li[i], end='  ')
```

    1  2  4  7  2  1  

- remove 테스트


```python
#pseudo_list
li.remove(7)
#python_list
py_li.remove(7)

print("pseudo list : "+ str(li))
print("python list : " + str(py_li))
```

    data of 7 is deleted
    pseudo list : [1, 2, 3, 4, 2]
    python list : [1, 2, 4, 2, 1]
    
