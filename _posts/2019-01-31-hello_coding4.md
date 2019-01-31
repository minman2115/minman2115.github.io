---
layout: post
title: "퀵정렬 기초개념"
tags: [Python, 자료구조 알고리즘]
comments: true
---

퀵정렬 기초개념 요약 및 Python 프로그래밍 구현연습

ㅇ 'Hello Coding 그림으로 개념을 이해하는 알고리즘'을 읽고 공부한 내용을 정리한 것으로 일부 주관적이며 오류가 있을 수 있습니다.

ㅇ 참고 도서정보
- 제목 : Hello Coding 그림으로 개념을 이해하는 알고리즘
- 저자&출판사 : 아디트야 바르가바 지음, 김도형 옮김 / 한빛미디어

## 퀵 정렬

- 현업에서 많이 사용되는 빠른 정렬 기법

분할정복기법 (Divide and conquer)을 활용한다. (문제를 분할하여 해결, 재귀 함수를 이용)
기준값을 하나 택하고 순회를 돌면서 기준값보다 작은 데이터는 좌측에, 기준값보다 큰 데이터는 우측으로 분할하고 다시 퀵 정렬을 적용한다.

- 통상 $\ O(nlogn) $

- 이미 정렬 된 데이터의 경우(최악의 경우) $\ O(n^2) $ 를 보이기도 한다.
   
퀵소트의 경우 이미 정렬 된 데이터에 대해서 최악의 효율을 보인다. 피벗이 한쪽 끝으로 오게 되어서 파티션을 나눌 때 균형이 완전히 무너지고 한 번 정렬에 데이터가 하나씩 떨어져 나가게 된다. 따라서 데이터가 n개라면 n과 거의 동일한 수의 단계를 거쳐야 하므로 결과적으로 이미 정렬 된 데이터에 대해 효율은 $\ O(n^2) $이 된다.


```python
import random

def quicksort(li):
    
    if len(li) < 2:
        return li
    else:
        pivot = li[0]
        small_group = [li_data for li_data in li[1:] if li_data <= pivot]
        large_group = [li_data for li_data in li[1:] if li_data > pivot]
        
    return quicksort(small_group) + [pivot] + quicksort(large_group)

li = [random.randint(1,10000) for data in range(10)]
print("정렬 전 최초 리스트 :", li )

print("정렬 후 리스트:",quicksort(li))
```

    정렬 전 최초 리스트 : [5484, 7354, 6693, 1495, 342, 4792, 2475, 2124, 7746, 5394]
    정렬 후 리스트: [342, 1495, 2124, 2475, 4792, 5394, 5484, 6693, 7354, 7746]
    
