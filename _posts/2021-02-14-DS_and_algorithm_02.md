---
layout: post
title: "heap 기초개념 및 구현실습 - 패캠 알고리즘&기술면접 인강"
tags: [Python, 자료구조 알고리즘]
comments: true
---

.

[학습시 참고자료]

패스트캠퍼스 '알고리즘 / 기술면접 완전 정복 올인원 패키지 Online' 를 공부하고 정리한 학습노트입니다.

URL : https://www.fastcampus.co.kr/dev_online_algo

[학습내용]

### 1. 힙 (Heap) 이란

- 트리를 기반으로해서 특정한 목적에 맞춰서 변형된 형태를 가진 자료구조

- 힙이란 데이터에서 최대값과 최소값을 빠르게 찾기 위해 고안된 완전 이진 트리(Complete Binary Tree)
  - 완전 이진 트리: 노드를 삽입할 때 최하단 왼쪽 노드부터 차례대로 삽입하는 트리

<img src="https://www.fun-coding.org/00_Images/completebinarytree.png" width=300>

- 힙을 사용하는 이유
  - 기본적으로 힙은 배열의 단점을 개선하기 위한 자료구조라고 할 수 있음
  - 배열에 데이터를 넣고, 최대값과 최소값을 찾으려면 O(n) 이 걸리기 때문에 이 최대값과 최솟값을 어떻게 하면 빠르게 검색할 수 있을지에 대한 고민에서 나온것임
  - 따라서 힙은 기본적으로 최댓값과 최솟값을 찾는데는 배열보다 빠른데 데이터를 힙에 데이터를 저장하고, 최대값과 최소값을 찾으면, $ O(log n) $ 이 걸림
  - 우선순위 큐와 같이 최대값 또는 최소값을 빠르게 찾아야 하는 자료구조 및 알고리즘 구현 등에 활용됨

### 2. 힙 (Heap) 구조

- 힙은 최대값을 구하기 위한 구조 (최대 힙, Max Heap) 와, 최소값을 구하기 위한 구조 (최소 힙, Min Heap) 로 분류할 수 있음


- 힙은 다음과 같이 두 가지 조건을 가지고 있는 자료구조임


조건 1. 각 노드의 값은 해당 노드의 자식 노드가 가진 값보다 크거나 같다. (최대 힙의 경우)

** 참고로 최소 힙의 경우는 각 노드의 값은 해당 노드의 자식 노드가 가진 값보다 크거나 작음
     
조건 2. 완전 이진 트리 형태를 가짐

#### 힙과 이진 탐색 트리의 공통점과 차이점
- 공통점: 힙과 이진 탐색 트리는 모두 이진 트리임
- 차이점: 
  - 힙은 각 노드의 값이 자식 노드보다 크거나 같음(Max Heap의 경우)
  - 이진 탐색 트리는 왼쪽 자식 노드의 값이 가장 작고, 그 다음 부모 노드, 그 다음 오른쪽 자식 노드 값이 가장 큼
  - 힙은 이진 탐색 트리의 조건인 자식 노드에서 작은 값은 왼쪽, 큰 값은 오른쪽이라는 조건은 없음
    - 힙의 왼쪽 및 오른쪽 자식 노드의 값은 오른쪽이 클 수도 있고, 왼쪽이 클 수도 있음
- 이진 탐색 트리는 탐색을 위한 구조, 힙은 최대/최소값 검색을 위한 구조 중 하나로 이해하면 됨  
<img src="https://www.fun-coding.org/00_Images/completebinarytree_bst.png" width="800" />


### 3. 힙 (Heap) 동작
- 데이터를 힙 구조에 삽입, 삭제하는 과정을 그림을 통해 선명하게 이해하기

#### 힙에 데이터 삽입하기 - 기본 동작 
- 힙은 완전 이진 트리이므로, 삽입할 노드는 기본적으로 왼쪽 최하단부 노드부터 채워지는 형태로 삽입
<img src="https://www.fun-coding.org/00_Images/heap_ordinary.png">

#### 힙에 데이터 삽입하기 - 삽입할 데이터가 힙의 데이터보다 클 경우 (Max Heap 의 예)
- 먼저 삽입된 데이터는 완전 이진 트리 구조에 맞추어, 최하단부 왼쪽 노드부터 채워짐
- 채워진 노드 위치에서, 부모 노드보다 값이 클 경우, 부모 노드와 위치를 바꿔주는 작업을 반복함 (swap)
<img src="https://www.fun-coding.org/00_Images/heap_insert.png">

#### 힙의 데이터 삭제하기 (Max Heap 의 예)
- 보통 삭제는 최상단 노드 (root 노드)를 삭제하는 것이 일반적임
  - 힙의 용도는 최대값 또는 최소값을 root 노드에 놓아서, 최대값과 최소값을 바로 꺼내 쓸 수 있도록 하는 것임
- 상단의 데이터 삭제시, 가장 최하단부 왼쪽에 위치한 노드 (일반적으로 가장 마지막에 추가한 노드) 를 root 노드로 이동
- root 노드의 값이 child node 보다 작을 경우, root 노드의 child node 중 가장 큰 값을 가진 노드와 root 노드 위치를 바꿔주는 작업을 반복함 (swap)

<img src="https://www.fun-coding.org/00_Images/heap_remove.png">

### 4. 힙 구현

#### 힙은 일반적으로 배열로 표현을 많이 하는 편이다.
- 일반적으로 힙 구현시 배열 자료구조를 활용함, 이는 힙이 완전이진트리 형태이기 때문이다.

#### 배열의 인덱스를 이용해서 힙의 인덱스를 계산할 수 있다.

- 배열(list)은 인덱스가 0번부터 시작하지만, 힙 구현의 편의를 위해, root 노드 인덱스 번호를 1로 지정하면, 구현이 좀더 수월함
  - 부모 노드 인덱스 번호 (parent node's index) = 자식 노드 인덱스 번호 (child node's index) // 2
  - 왼쪽 자식 노드 인덱스 번호 (left child node's index) = 부모 노드 인덱스 번호 (parent node's index) * 2
  - 오른쪽 자식 노드 인덱스 번호 (right child node's index) = 부모 노드 인덱스 번호 (parent node's index) * 2 + 1
<img src="https://www.fun-coding.org/00_Images/heap_array.png" width=400>


```python
# 예시 - 4 노드의 부모노드 인덱스
5 // 2
```




    2




```python
# 예1 - 10 노드의 부모 노드 인덱스
2 // 2
```




    1




```python
# 예1 - 15 노드의 왼쪽 자식 노드 인덱스 번호
1 * 2
```




    2




```python
# 예1 - 15 노드의 오른쪽 자식 노드 인덱스 번호
2 * 2 + 1
```




    5



#### 힙에 데이터 삽입 구현 (Max Heap 예)

#### STEP 1) init 함수 구현


```python
class Heap:
    def __init__(self, data):
        self.heap_array = list()
        # 배열(list)은 인덱스가 0번부터 시작하지만, 힙 구현의 편의를 위해, root 노드 인덱스 번호를 1로 지정한 것을 구현한 것이다.
        self.heap_array.append(None)
        self.heap_array.append(data)
```


```python
heap = Heap(1)
heap.heap_array
```




    [None, 1]



#### STEP2) insert 함수 구현 (2/1) - 기본적인 삽입 기능

인덱스 번호는 1번부터 시작하도록 변경

<img src="https://www.fun-coding.org/00_Images/heap_ordinary.png">


```python
class Heap:
    def __init__(self, data):
        self.heap_array = list()
        self.heap_array.append(None)
        self.heap_array.append(data)
        
    def insert(self, data):
        if len(self.heap_array) == 0:
            self.heap_array.append(None)
            self.heap_array.append(data)
            return True
        
        self.heap_array.append(data)
        return True           
```

#### STEP 3) insert 함수 구현 (2/2) : 삽입한 노드가 부모 노드의 값보다 클 경우, 부모 노드와 삽입한 노드 위치를 바꾸는 기능

삽입한 노드가 부모 노드의 값보다 클 경우, 부모 노드와 삽입한 노드 위치를 바꿈

삽입한 노드가 루트 노드가 되거나, 부모 노드보다 값이 작거나 같을 경우까지 반복


---

- 특정 노드의 관련 노드 위치 알아내기
  - 부모 노드 인덱스 번호 (parent node's index) = 자식 노드 인덱스 번호 (child node's index) // 2
  - 왼쪽 자식 노드 인덱스 번호 (left child node's index) = 부모 노드 인덱스 번호 (parent node's index) * 2
  - 오른쪽 자식 노드 인덱스 번호 (right child node's index) = 부모 노드 인덱스 번호 (parent node's index) * 2 + 1

<img src="https://www.fun-coding.org/00_Images/heap_insert.png">


```python
class Heap:
    def __init__(self, data):
        self.heap_array = list()
        self.heap_array.append(None)
        self.heap_array.append(data)
    
    # 상위노드와 바꿔줘야 하는지 아니면 루트노드이던지 아니면 상위노드와 바꿀 필요가 없는지 판단하는 메서드
    # return 값이 True일경우 바꿔줘야 하는 것으로 판단함
    def move_up(self, inserted_idx):
        if inserted_idx <= 1:
            return False
        
        parent_idx = inserted_idx // 2
        if self.heap_array[inserted_idx] > self.heap_array[parent_idx]:
            return True
        else:
            return False
        
    def insert(self, data):
        if len(self.heap_array) == 0:
            self.heap_array.append(None)
            self.heap_array.append(data)
            return True
        
        self.heap_array.append(data)
        
        # 지금 삽입하는 데이터의 인덱스 번호를 계산
        inserted_idx = len(self.heap_array) - 1
        
        # while True일경우, 다시말해서 부모노드와 스위칭 해줘야 하는 경우
        # 부모노드가 데이터 값이 더 크거나 위치가 루트노드일때까지 계속하게 되어 있음
        while self.move_up(inserted_idx):
            parent_idx = inserted_idx // 2
            
            # 부모노드와 swap 구현
            # self.heap_array[inserted_idx] = self.heap_array[parent_idx]
            # self.heap_array[parent_idx] = self.heap_array[inserted_idx]
            self.heap_array[inserted_idx], self.heap_array[parent_idx] = self.heap_array[parent_idx], self.heap_array[inserted_idx]
            
            inserted_idx = parent_idx
        
        return True
```


```python
heap = Heap(15)
heap.insert(10)
heap.insert(8)
heap.insert(5)
heap.insert(4)
heap.insert(20)
heap.heap_array
```




    [None, 20, 10, 15, 5, 4, 8]



#### 힙에서 데이터 삭제 구현 (Max Heap 예)

힙에서 데이터를 삭제하는 경우는 일반적으로 루트노드에서 최댓값을 뽑아내고, 그 루트노드를 삭제하는 경우를 말한다. 힙에서 일반적으로는 루트노드가 아니라 중간에 있는 노드들을 삭제하는 경우는 거의 없다. (Min heap에서는 이런 경우가 최솟값일 경우를 얘기하는 것이다.)

#### STEP 1) init 함수 및 루트노드 데이터를 꺼내오는 함수(아래 코드에서 pop 함수) 구현

- 보통 삭제는 최상단 노드 (root 노드)를 삭제하는 것이 일반적임
  - 힙의 용도는 최대값 또는 최소값을 root 노드에 놓아서, 최대값과 최소값을 바로 꺼내 쓸 수 있도록 하는 것임


```python
class Heap:
    def __init__(self, data):
        self.heap_array = list()
        self.heap_array.append(None)
        self.heap_array.append(data)
    
    def pop(self):
        
        # 데이터가 없는 경우
        # 노드에 있는 값이 없기 때문에 None을 리턴하는 것으로 처리함
        if len(self.heap_array) <= 1:
            return None
        
        returned_data = self.heap_array[1]
        return returned_data
```

#### STEP 1) pop함수 보완 및 move_down 함수 구현

- 상단의 데이터 삭제시, 가장 최하단부 왼쪽에 위치한 노드 (일반적으로 가장 마지막에 추가한 노드)를 root 노드로 이동

- root 노드의 값이 child node 보다 작을 경우, root 노드의 child node 중 가장 큰 값을 가진 노드와 root 노드 위치를 바꿔주는 작업을 반복함 (swap)


---


- 특정 노드의 관련 노드 위치 알아내기
  - 부모 노드 인덱스 번호 (parent node's index) = 자식 노드 인덱스 번호 (child node's index) // 2
  - 왼쪽 자식 노드 인덱스 번호 (left child node's index) = 부모 노드 인덱스 번호 (parent node's index) * 2
  - 오른쪽 자식 노드 인덱스 번호 (right child node's index) = 부모 노드 인덱스 번호 (parent node's index) * 2 + 1

<img src="https://www.fun-coding.org/00_Images/heap_insert.png">


```python
class Heap:
    def __init__(self, data):
        self.heap_array = list()
        self.heap_array.append(None)
        self.heap_array.append(data)
    
    def move_down(self, popped_idx):
        left_child_popped_idx = popped_idx * 2
        right_child_popped_idx = popped_idx * 2 + 1
        
        # case1: 왼쪽 자식 노드도 없을 때
        if left_child_popped_idx >= len(self.heap_array):
            return False
        # case2: 오른쪽 자식 노드만 없을 때
        elif right_child_popped_idx >= len(self.heap_array):
            if self.heap_array[popped_idx] < self.heap_array[left_child_popped_idx]:
                return True
            else:
                return False
        # case3: 왼쪽, 오른쪽 자식 노드 모두 있을 때
        else:
            if self.heap_array[left_child_popped_idx] > self.heap_array[right_child_popped_idx]:
                if self.heap_array[popped_idx] < self.heap_array[left_child_popped_idx]:
                    return True
                else:
                    return False
            else:
                if self.heap_array[popped_idx] < self.heap_array[right_child_popped_idx]:
                    return True
                else:
                    return False
    
    def pop(self):
        if len(self.heap_array) <= 1:
            return None
        
        returned_data = self.heap_array[1]
        
        # 힙의 맨 마지막에 있는 데이터를 루트위치로 바꿔주는 부분
        self.heap_array[1] = self.heap_array[-1]
        
        # 그리고 마지막에 있는 데이터가 필요없기 때문에 맨 마지막 노드 삭제
        del self.heap_array[-1]
        
        # pop된 데이터는 루트데이터이기 때문에 인덱스가 1이다.
        popped_idx = 1
        
        while self.move_down(popped_idx):
            left_child_popped_idx = popped_idx * 2
            right_child_popped_idx = popped_idx * 2 + 1

            # case2: 오른쪽 자식 노드만 없을 때
            if right_child_popped_idx >= len(self.heap_array):
                if self.heap_array[popped_idx] < self.heap_array[left_child_popped_idx]:
                    self.heap_array[popped_idx], self.heap_array[left_child_popped_idx] = self.heap_array[left_child_popped_idx], self.heap_array[popped_idx]
                    popped_idx = left_child_popped_idx
            # case3: 왼쪽, 오른쪽 자식 노드 모두 있을 때
            else:
                if self.heap_array[left_child_popped_idx] > self.heap_array[right_child_popped_idx]:
                    if self.heap_array[popped_idx] < self.heap_array[left_child_popped_idx]:
                        self.heap_array[popped_idx], self.heap_array[left_child_popped_idx] = self.heap_array[left_child_popped_idx], self.heap_array[popped_idx]
                        popped_idx = left_child_popped_idx
                else:
                    if self.heap_array[popped_idx] < self.heap_array[right_child_popped_idx]:
                        self.heap_array[popped_idx], self.heap_array[right_child_popped_idx] = self.heap_array[right_child_popped_idx], self.heap_array[popped_idx]
                        popped_idx = right_child_popped_idx
        
        return returned_data
    
    def move_up(self, inserted_idx):
        if inserted_idx <= 1:
            return False
        parent_idx = inserted_idx // 2
        if self.heap_array[inserted_idx] > self.heap_array[parent_idx]:
            return True
        else:
            return False

    def insert(self, data):
        if len(self.heap_array) == 1:
            self.heap_array.append(data)
            return True
        
        self.heap_array.append(data)
        inserted_idx = len(self.heap_array) - 1
        
        while self.move_up(inserted_idx):
            parent_idx = inserted_idx // 2
            self.heap_array[inserted_idx], self.heap_array[parent_idx] = self.heap_array[parent_idx], self.heap_array[inserted_idx]
            inserted_idx = parent_idx
        return True    
```


```python
heap = Heap(15)
heap.insert(10)
heap.insert(8)
heap.insert(5)
heap.insert(4)
heap.insert(20)
heap.heap_array
```




    [None, 20, 10, 15, 5, 4, 8]




```python
heap.pop()
```




    20




```python
heap.heap_array
```




    [None, 15, 10, 8, 5, 4]



### 5. 힙 (Heap) 시간 복잡도
  - depth (트리의 높이) 를 h라고 표기한다면,
  - n개의 노드를 가지는 heap 에 데이터 삽입 또는 삭제시, 최악의 경우 root 노드에서 leaf 노드까지 비교해야 하므로 $h = log_2{n} $ 에 가까우므로, 시간 복잡도는 $ O(log{n}) $ 
     - 참고: 빅오 표기법에서 $log{n}$ 에서의 log의 밑은 10이 아니라, 2임
     - 한번 실행시마다, 50%의 실행할 수도 있는 명령을 제거한다는 의미. 즉 50%의 실행시간을 단축시킬 수 있다는 것을 의미함

