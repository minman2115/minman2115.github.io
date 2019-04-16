---
layout: post
title: "Dummy Double Linked List 기초개념 및 구현실습"
tags: [Python, 자료구조 알고리즘]
comments: true
---

.

#### # 학습시 참고 URL : https://github.com/ythwork

- 링크드 리스트라고 하면 더미 링크드 리스트를 말한다.


- 현업에서 제일 많이 쓰이는 리스트 자료구조이다.


- 싱글링크드 리스트와 비교해서 링크가 두개이다.


- 여기서 더미란 데이터를 가지고 있지 않은 노드이다. 맨 앞과 맨 뒤에 헤드와 테일이 가리키도록 할 것이다. 


- node

![1](https://user-images.githubusercontent.com/41605276/56190912-4447d600-6066-11e9-97f4-6d5e7c12c210.png)

- Dummy Double Linked List에서 dummy란 데이터를 가지지 않는 노드를 의미한다.


- DummyDoubleLinkedList 클래스의 인스턴스 멤버


헤드 = 맨 앞에 더미를 가리킴

테일 = 맨 뒤에 있는 더미를 가리킴

d_size = 자료구조에 데이터가 몇개 들어가 있는가

- ADT

Operations

S.empty() : Boolean을 반환하는데 리스트가 비어있으면 True, 아니면 False를 반환한다.

S.size() : integer를 반환하는데 리스트에 있는 요소 개수를 반환한다.

insert 계열

S.add_first(data) : 반환하지 않고, 리스트의 맨 앞에 데이터를 추가한다.

S.add_last(data) : 반환하지 않고, 리스트의 맨뒤에 데이터를 추가한다.

S.insert_after(data, node) : 반환하지 않고, node 뒤에 데이터를 추가한다.

S.insert_before(data, node) : 반환하지 않고, node 앞에 데이터를 추가한다.

search 계열

S.search_forward(target) : 반환하지 않고, 리스트의 맨 앞 데이터부터 순회하면서 target을 검색한다.

S.search_backward(target) : 반환하지 않고, 리스트의 맨 뒤 데이터부터 순회하면서 target을 검색한다.

delete 계열

S.delete_first() : 반환하지 않고, 리스트의 첫번째 데이터를 삭제한다.

S.delete_last() : 반환하지 않고, 리스트의 마지막 데이터를 삭제한다.

S.delete_node(node) : 반환하지 않고, node를 삭제한다.

- 초기화

![2](https://user-images.githubusercontent.com/41605276/56190937-4f9b0180-6066-11e9-930c-3525f5fb2229.png)

- add_first 작동예시

새 노드를 생성한다.

![3](https://user-images.githubusercontent.com/41605276/56190951-57f33c80-6066-11e9-9456-2d92b3add907.png)

- search forward 작동예시

![4](https://user-images.githubusercontent.com/41605276/56190961-5de91d80-6066-11e9-9c9a-e8830bce6e9c.png)

- delete_node 작동예시

![5](https://user-images.githubusercontent.com/41605276/56190970-63466800-6066-11e9-8020-a4e62084b07d.png)

- 파이썬 코드구현


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


```python
if __name__=="__main__":
    _list = DummyDoubleLinkedList()
    print('*'*100)
    print('데이터 삽입 : add_first')
    _list.add_first(1)
    _list.add_first(2)
    _list.add_first(3)
    _list.add_first(5)
#      print('데이터 삽입 : add_last')
#     _list.add_last(1)
#     _list.add_last(2)
#     _list.add_last(3)
#     _list.add_last(5)

    show_list(_list, start=True)
```

    ****************************************************************************************************
    데이터 삽입 : add_first
    data size : 4
    5  3  2  1  
    


```python
print('데이터 삽입 : insert_after')
_list.insert_after(4, _list.search_forward(3))
show_list(_list, start=True)

print('데이터 삽입 - insert_before')
_list.insert_before(4, _list.search_forward(5))
show_list(_list, start=True)
```

    데이터 삽입 : insert_after
    data size : 5
    5  3  4  2  1  
    데이터 삽입 - insert_before
    data size : 6
    4  5  3  4  2  1  
    


```python
print('데이터 탐색')
target=3
#res=dlist.search_forward(target)
res=_list.search_forward(target)
if res:
    print('데이터 {} 탐색 성공'.format(res.data))
else:
    print('데이터 {} 탐색 실패'.format(target))
res=None

print('*'*100)
```

    데이터 탐색
    데이터 3 탐색 성공
    ****************************************************************************************************
    


```python
print('데이터 삭제 : delete_node')
_list.delete_node(_list.search_backward(5))

print('데이터 삭제 : delete_first')
_list.delete_first()

print('데이터 삭제 : delete_last')
_list.delete_last()

show_list(_list, start=True)
```

    데이터 삭제 : delete_node
    data of 5 is deleted
    데이터 삭제 : delete_first
    data of 4 is deleted
    데이터 삭제 : delete_last
    data of 1 is deleted
    data size : 3
    3  4  2  
    
