---
layout: post
title: "Dummy Double Linked List 기초개념 및 구현실습"
tags: [Python, 자료구조 알고리즘]
comments: true
---

.

#### # 학습시 참고 URL : https://github.com/ythwork

#### 배열

- 배열의 두가지 조건이 있다.

1) 같은 데이터 타입(자료형)을 가진 변수의 모임

2) 모든 데이터의 메모리가 붙어있어야 한다.(메모리가 서로 인접해있다.)

따라서 메모리에 접근을 할때 편하다.

캐시히트가 날 가능성이 높다. (이런 장점때문에 배열을 쓴다)

- 배열의 단점 : 크기가 고정되어 있다.(최악의 단점)


#### 동적배열(Dynamic array, 가변배열)

1) 길이가 변할 수 있다.

2) heap에다 저장된다.

3) 배열의 특징을 갖고 있다.

4) 힙의 다른공간에 기존배열의 두배의 공간을 다시 만든다.

예를들어 기존에 4개의 데이터였다면 8개의 데이터로 늘린다.	

거기에 새로운 데이터를 추가한다.

#### 동적배열 vs 연결리스트(double linked list)

동적배열 
      
1) 메모리공간이 서로 연결되어 있다.

2) 배열 공간이 비어있을시 종단 삽입,삭제(배열의 맨 처음부분과 맨 마지막부분)할때 O(1)이다.

3) 그러나(최악의 경우) 배열이 꽉차있는데 새로운 데이터를 삽입하려면 O(n)이 소요된다.

메모리할당 -> 복사 -> 새로운 데이터 삽입

4) 이런경우도 최악의 경우인데 배열중간에 삽입(일반 삽입) 시 그 중간에 공간을 마련하기 위해 계속 기존의 데이터를 복사해서 배열 옆쪽 공간으로 붙어야 한다. 그리고 비로소 중간에 공간이 났을때 그때 그 부분에 새로운 데이터를 삽입한다. 이 경우 O(n)이 소요된다.

5) 일반삭제도 마찬가지로 O(n)이 소요된다. 중간 부분에 데이터를 삭제하면 옆에 있는 데이터를 붙어서 빈공간이 나지 않도록 매꾸어줘야 한다.

6) 탐색시 인덱싱(해당 주소로 바로 접근)으로 접근하기 때문에 O(1)으로 엄청빠르다.
   
연결리스트

메모리공간이 제각각이다. 다만 링크를 통해 연결해줄 뿐이다.

단점 :  

1) 캐시 히트를 기대할 수 없다.

2) 캐시 미스 뿐만 아니라 데이터를 요청할때마다 page fault가 날 가능성이 커진다.

3) 탐색시 O(n)이다.

장점 :

리스트의 맨처음 부분과 맨 끝부분, 리스트의 중간 부분에 삽입과 삭제 모두 가능하며 O(1)으로 엄청빠르다.	

#### 탐색을 많이하고 삽입 삭제를 별로 안할때 -> 동적 배열로 구현


#### 삽입 삭제는 많이 하는데 탐색을 별로 안할때 -> 링크드 리스트로 구현


#### 삽입삭제 탐색 모두 많이 하면 -> 트리로 구현. 참고로 데이터베이스는 트리로 구현하는 것이다.

#### Amortized analysis(분할상환기법)

컴퓨터 공학에서, 분할상환분석(amortized analysis)은 주어진 알고리즘의 시간 복잡도나 프로그램을 수행하는데 소요되는 시간 또는 메모리 같은 자원 사용량을 분석하기 위해서 사용하는 기법이다.알고리즘을 분석할 때에 각각의 연산마다 최악의 경우를 따져본다는 것은 굉장히 힘든 일이기 때문에, 이를 쉽게 해결하기 위해 분할상환분석이라는 방법론이 나오게 되었다.

어떠한 임의의 알고리즘에 대해서, 어떤 연산은 자원적 측면에서 상당한 비용을 소모할 수 있지만, 반면 다른 연산은 그렇게 고비용을 소모하지 않을 수 있다. 분할상환 분석은 알고리즘의 전반적인 연산 집합에 대해 비용이 높은 연산, 그리고 비용이 덜한 연산 모두를 함께 고려하는 기법이라 하겠다. 이것은 다른 종류의 입력, 입력의 길이, 이 알고리즘의 성능에 영향을 미치는 다른 요인들을 전부 고려한다. 수행된 모든 연산에 대해 자료구조 연산만의 어떤 시퀀스를 수행하는데 필요한 시간의 평균을 구한다. 비록 그 시퀀스에서 하나의 연산비용이 비싸더라도, 그 일련의 연산에 대해 평균을 구하면 연산 하나의 평균 비용이 작다는 것을 분할 상환 분석을 이용해 보일 수 있다. 분할 상환 분석은 확률이 포함되지 않으므로 평균비용 분석과는 다르다. 분할 상환 분석은 최악의 경우에도 각 연산의 평균 수행성능을 보장한다 

#### 링크드 리스트라고 하면 보통 더미 링크드 리스트를 말한다.


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
    
