---
layout: post
title: "선형탐색과 이진탐색 기초개념 및 구현실습"
tags: [Python, 자료구조 알고리즘]
comments: true
---

.

#### 그림, 실습코드 등 학습자료 출처 : https://github.com/ythwork

### [선형탐색]

- 파이썬코드 구현


```python
def linear_search(li, target):
    
    for idx in range(len(li)):
        if target==li[idx]:
            return idx
    return None

if __name__=="__main__":
    data=[i**2 for i in range(1, 9+1)]
    
    print(data)

    target=4
    idx=linear_search(data, target)

    if idx:
        print('index : {}, data : {}'.format(idx, data[idx]))
    else:
        print('Failed to find the data of {}'.format(target))
```

    [1, 4, 9, 16, 25, 36, 49, 64, 81]
    index : 1, data : 4
    

linear_search(li(리스트), target(찾고자 하는 타겟))

return 값으로 리스트에 있는 데이터를 반환한다.

li는 리스트를 의미한다.

target는 찾고자 하는 데이터

return은 찾았다면 찾은 데이터, 없다면 None을 반환해준다.
    
기본원리 : 말그대로 리스트를 넣어서 그 리스트의 인덱스에 해당하는 value값과 target 데이터랑 비교하여 그 벨류값과 타겟 데이터가 같을 경우 인덱스를 리턴해준다.

### [이진탐색]

- 이진탐색 성립조건 : 데이터가 반드시 정렬된 상태이어야 한다.


- 이진탐색 구현하기

예를 들어서 list = [1,2,3,4,5,6,7,8,9,10], target = 2라고 할때 이진탐색의 탐색과정


step 1) 가운데 인덱스(=mid)를 먼저 구한다.

ex)


```python
start = 0 
# 파이썬 인덱스가 가장 앞에는 0이기 때문에

end = len(list)-1 
# 파이썬 리스트에서 가장 끝의 인덱스는 list의 길이 -1이다.
 
middle = (start + end) // 2 
# 따라서 가운데 인덱스는 start 인덱스와 end 인덱스를 더해서 2로 나누었을때 몫을 의미한다.
```

![1](https://user-images.githubusercontent.com/41605276/56191674-bec52580-6067-11e9-86e1-1cfd7e9abb4c.png)

step 2) target과 mid 의 값을 비교한다.

mid = 5

target = 2


step 3) mid > target일 경우

mid 값을 업데이트 한다. end = middle-1로 업데이트 해주고 기존에 middle 이후의 것들은 신경 안써도 된다.

그래서 start = 0, end = 4이기 때문에 middle = 2가 된다.

![2](https://user-images.githubusercontent.com/41605276/56191684-c684ca00-6067-11e9-8447-6c1331ce6d66.png)

step 4) mid < target 일 경우

- mid 이하는 아예 무시한다.


- mid값을 업데이트 한다. 먼저 start를 mid+1로 옮겨준다. 그 다음에 (start / end) // 2 로 새로운 mid값을 설정해준다.

step 5) start와 end가 같아지고 mid와 start,end가 교차되는 순간(start > end) while문을 빠져나오도록 조건을 걸어준다
   

- 파이썬코드 구현


```python
def binary_search(li, target):
    start=0
    end=len(li)-1

    while start <= end:
        middle=(start+end)//2
        if li[middle]==target:
            return middle
        elif li[middle] > target:
            end=middle-1
        else:
            start=middle+1

    return None

if __name__=="__main__":
    data=[i**2 for i in range(1, 10)]

    target=9
    idx=binary_search(data, target)

    if idx:
        
        print('index : {}, data : {}'.format(idx, data[idx]))
    else:
        print('Failed to find the data of {}'.format(target))
```

    index : 2, data : 9
    
