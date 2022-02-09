---
layout: post
title: "quick sort 기본개념"
tags: [Python, 자료구조 알고리즘]
comments: true
---

.

Coding_test_training(20220209)

[학습자료]

패스트 캠퍼스 "알고리즘 / 기술면접 완전 정복 올인원 패키지 Online." 강의를 공부하고 정리한 내용입니다.

** URL : https://fastcampus.co.kr/dev_online_algo

[학습내용]

- 개요

(1) 기준점(=pivot)을 정해서, 기준점보다 작은 데이터는 왼쪽(left), 큰 데이터는 오른쪽(right) 으로 모으는 함수를 작성함

(2) 각 왼쪽(left), 오른쪽(right)은 재귀용법을 사용해서 다시 동일 함수를 호출하여 위 작업을 반복함

(3) 함수는 왼쪽(left) + 기준점(pivot) + 오른쪽(right) 을 리턴함

- 실제 퀵정렬 구현코드

```python
import random

def quick_sort(data):
    if len(data) <= 1:
        return data

    pivot = data[0]
    left = [item for item in data[1:] if pivot > item]
    right = [item for item in data[1:] if pivot <= item]

    return quick_sort(left) + [pivot] + quick_sort(right)

data_list = random.sample(range(100), 10)

quick_sort(data_list)
```

```text
[13, 21, 34, 37, 38, 40, 51, 52, 59, 85]
```

- 시간복잡도 : O(n log n)

** 가장 최악의 경우 : 정렬된 오름차순일때 모든 데이터를 비교하는 상황이 나옴, 이때 시간복잡도는 n의 2승임