---
layout: post
title: "선택정렬 기초개념"
tags: [Python, 자료구조 알고리즘]
comments: true
---

선택정렬 기초개념 요약 및 Python 프로그래밍 구현연습

ㅇ 'Hello Coding 그림으로 개념을 이해하는 알고리즘'을 읽고 공부한 내용을 정리한 것으로 일부 주관적이며 오류가 있을 수 있습니다.

ㅇ 참고 도서정보
- 제목 : Hello Coding 그림으로 개념을 이해하는 알고리즘
- 저자&출판사 : 아디트야 바르가바 지음, 김도형 옮김 / 한빛미디어

## 선택정렬

- $\ O(n^2) $
- 최소값을 선택하는 과정을 반복해서 선택 정렬이라 부른다고 한다.

    1. 주어진 리스트 중에 최솟값을 찾는다
    
    2. 그 값을 맨 앞에 위치한 값과 교체한다(패스(pass))
    
    3. 맨 처음 위치를 뺀 나머지 리스트를 같은 방법으로 교체한다.


```python
import random

def findSmallest(li):
    smallest = li[0]
    smallest_index = 0

    for idx in range(1,len(li)):
        if li[idx] < smallest:
            smallest = li[idx]
            smallest_index = idx
    
    return smallest_index

def selectionSort(li):
    
    result = []
    
    for count in range(len(li)):
        smallest = findSmallest(li)
        result.append(li.pop(smallest))
        
    return print("정렬 후 리스트 :", result)


li = [random.randint(1,10000) for data in range(10)]
print("정렬 전 최초 리스트 :", li )

selectionSort(li)
```

    정렬 전 최초 리스트 : [4913, 926, 458, 5017, 5512, 3870, 8681, 2505, 8766, 1617]
    정렬 후 리스트 : [458, 926, 1617, 2505, 3870, 4913, 5017, 5512, 8681, 8766]
    
