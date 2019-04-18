---
layout: post
title: "Single Linked List 기초개념 및 구현실습"
tags: [Python, 자료구조 알고리즘]
comments: true
---

.

#### # 학습시 참고 URL : https://github.com/ythwork


- 현업에서 얘기하는 링크드리스트는 대부분 더미 더블 링크드 리스트를 의미한다.


- single linked list : 여기서 싱글은 링크가 가리키는게 하나만 있다는 얘기이다.


- single linked list의 구성요소

1) 노드 클래스

데이터를 저장하는 틀

노드의 두가지요소 

data : 실제로 저장하고자 하는 데이터

next : 다음 노드를 가리키는 참조

![1](https://user-images.githubusercontent.com/41605276/56191349-29299600-6067-11e9-83ed-8e550cf407a4.png)

2) SingleLinkedList 클래스

- 인스턴스 멤버 

head : 리스트의 첫번째 노드를 가리킴

d_size : 리스트의 요소개수

- ADT

operations

S.empty() : Boolean을 return하는데 리스트가 비어있으면 True, 아니면 False

S.size() : integer를 return하는데 리스트에 있는 요소 개수

S.add() : 아무것도 반환하지 않고, 노드를 리스트의 맨 앞에 추가한다.

S.search(target) : Node를 반환하는데 리스트에서 target을 찾는다. 찾으면 노드를, 못찾으면 None을 반환한다.

S.delete() : 아무것도 반환하지 않고 맨 앞의 노드를 삭제한다.

ADT 상세

1) 삽입 -> add

s.add(data)->None

case 1) 데이터가 비어있을때

헤드=None 일것이고 그 상태에서 new를 만들면 새로운 노드가 데이터가 들어갈 것이다. 그리고 그 헤더가 new 노드를 가리키면 끝이다.

case 2) 데이터가 비어있지 않을때

기존에 데이터가 있을것이다. 

step1) 새로운 데이터를 만든다. 그 데이터는 None을 가리키고 있을 것이다. 

tep2) 이 new node의 넥스트를 헤드를 가리키게한다.

step3) 헤드를 뉴 노드를 가리키게 한다.
  
2) 삭제 -> delete

s.delete()-> Node

3) 탐색 -> search

cur를 이용해서 찾는다. 순회를 이용해서 찾는다. 순회 : 특정 자료구조에 저장되어 있는 모든데이터를 방문하는 것

s.search(target) -> 찾는게 있으면 그 노드를 반환하고 아니면 None을 반환한다.

- garbage collection 

파이썬에서 사용하지 않는 heap 메모리를 언어 또는 os차원에서 알아서 지워주는 것

반면에 c는 일일히 사용하고 지워줘야 하는 번거로움이 있고 이걸 제대로 안해주면 메모리 누수가 발생한다.
  
이 가비지 컬렉션은 reference count(참조횟수)로 수행한다.

ex) a = 10, b=a 면 레퍼런스 카운트는 2이다.

레퍼런스 카운트가 0이 되는 순간 아무도 안쓰기 때문에 메모리에서 날려주는 것이 가비지 컬렉션이다.

#### [작동예시]

- 초기화

![2](https://user-images.githubusercontent.com/41605276/56191380-35155800-6067-11e9-9126-ae7a8e385597.png)

- 노드 insert 시 

새 노드(new_node)를 먼저 생성한다.

![3](https://user-images.githubusercontent.com/41605276/56191391-39da0c00-6067-11e9-8600-98ccbd5e3b0e.png)

새 노드가 head를 가리키게 만든다.

![4](https://user-images.githubusercontent.com/41605276/56191400-3f375680-6067-11e9-9fc4-f42249eee688.png)

head가 새 노드를 가리키게 만든다.

![5](https://user-images.githubusercontent.com/41605276/56191413-465e6480-6067-11e9-97be-f21b08506892.png)

- 예를들어 target이 2일때 노드 search 시 

cur가 head를 가리키게 만든다.

![6](https://user-images.githubusercontent.com/41605276/56191433-4f4f3600-6067-11e9-8e4c-4af278259fa1.png)

cur가 리스트를 순회하면서 target과 요소를 비교한다.

![7](https://user-images.githubusercontent.com/41605276/56191446-55451700-6067-11e9-9ba7-bd4cb9fc07fa.png)

![8](https://user-images.githubusercontent.com/41605276/56191461-5b3af800-6067-11e9-9ce6-03548d805f09.png)

- 노드 delete 시

head를 head.next로 옮겨준다.

![9](https://user-images.githubusercontent.com/41605276/56191481-62fa9c80-6067-11e9-9036-7b84e22ce390.png)

- 파이썬 코드 구현 예시


```python
class Node:
    def __init__(self, data = None):
        self.__data = data
        self.__next = None
    
    ## __del__은 소멸자라고 하고 객체가 메모리에서 사라질때 반드시 한번 호출하는 것
    def __del__(self):
        print("data of {} is deleted".format(self.__data))

    @property
    def data(self):
        return self.__data
    
    @data.setter
    def data(self, data):
        self.__data = data

    @property
    def next(self):
        return self.__next

    @next.setter
    def next(self, next):
        self.__next = next

class SingleLinkedList:
    def __init__(self):
        self.head = None 
        ## 헤드는 저장되어 있는 리스트중에 첫번째를 가리키는 것이다.
        ## 헤드는 싱글링크드리스트의 자료의 존재를 위해서도 필요하다. head가 없다면 예를들어 맨앞에 데이터의 reference count가 0이 되면서
        ## 연쇄적으로 데이터가 가비지 컬렉션될 것이고 자료 전체가 유지되지 않을것이다.
        self.d_size = 0 ## d_size는 이 싱글연결리스트가 갖고 있는 데이터 개수를 말한다.


    ## 아래 empty와 size 함수는 사용자가 이 싱글연결리스트에 데이터가 비어있는지 몇개 있는지 확인할때 쓴다.

    def empty(self): ## single 링크드 리스트가 비어있을때는 트루, 아니면 false
        if self.d_size == 0:
            return True
        else:
            return False

    def size(self): ## integer를 반환하는데 데이터의 개수를 반환한다.
        return self.d_size


    ## 아래 add, search, delete 함수는 ADT 삽입, 탐색, 삭제 구현
    
    def add(self, data):
        new_node = Node(data)
        new_node.next = self.head ## 새로운 노드 next를 기존에 있던 헤드를 가리키게 한다.
        self.head = new_node ## 헤드를 새로운 노드로 바꿔준다.
        self.d_size += 1

    def search(self, target): ## cur가 처음부터 하나씩 비교를해서 찾는다.
        cur = self.head ## cur를 헤드를 가리키도록 한다.
        while cur: ## cur가 있다면, 다시말해 링크드리스트를 돌면서(cur가 next를 계속하다가 None을 만나면 while문을 빠져나오게 된다.)
            if cur.data == target:
                return cur
            cur = cur.next ## 순회(traversal)를 구현함
        return None

    def delete(self): ## 싱글링크드 리스트의 맨 앞을 지우게 된다.
        self.head = self.head.next ## 기존에 데이터를 참조횟수를 0으로 만들어서 없애버림
        self.d_size -= 1


    ## 아래에 트레버스 함수는 ADT에는 들어가지는 않는데 
    ## 데이터가 있으면 헤더부터 시작해서 반환해서 넘겨주는 함수 search와 비슷함

    def traverse(self):
        cur = self.head
        while cur:
            yield cur 
            cur = cur.next

## 쇼리스트 함수는 트레버스 함수를 이용해서 리스트를 사용자에게 보여주는 함수
## 자료구조에서 중요한 부분은 아니다.
def show_list(slist):
    print('data size : {}'.format(slist.size()))
    g = slist.traverse()
    for node in g:
        print(node.data, end = '  ')
    print()


## 사용자 부분의 구현
if __name__ == "__main__":

    print('*'*10)
    slist = SingleLinkedList() ## 우리가 쓸 자료구조를 하나 인스턴스를 만든다.

    print('데이터 삽입')
    slist.add(3)
    slist.add(4)
    slist.add(5)
    slist.add(1)
    show_list(slist)

    print('데이터 탐색')
    target = 3
    res = slist.search(target)
    if res:
        print('데이터 {} 검색 성공'.format(res.data))
    else:
        print('데이터 {} 검색 실패'.format(target))
    del res

    print('데이터 삭제')
    slist.delete()
    slist.delete()
    show_list(slist)
    
    print('*'*10)
```

    **********
    data of 4 is deleted
    data of 3 is deleted
    데이터 삽입
    data size : 4
    1  5  4  3  
    데이터 탐색
    데이터 3 검색 성공
    데이터 삭제
    data of 1 is deleted
    data of 5 is deleted
    data size : 2
    4  3  
    **********
    
