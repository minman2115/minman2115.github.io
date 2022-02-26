---
layout: post
title: "이진탐색 기본개념"
tags: [Python, 자료구조 알고리즘]
comments: true
---

.

Coding_test_training(20220226)

[학습자료]

패스트 캠퍼스 "알고리즘 / 기술면접 완전 정복 올인원 패키지 Online." 강의를 공부하고 정리한 내용입니다.

** URL : https://fastcampus.co.kr/dev_online_algo

[학습내용]

- 실제코드 구현

```python
import random

def binary_search(data_list, search_value):
    
    print(data_list)
    
    if len(data_list) == 1 and search_value == data_list[0]:
        return True

    if len(data_list) == 1 and search_value != data_list[0]:
        return False

    if len(data_list) == 0:
        return False

    
    middle_index = len(data_list) // 2

    if search_value == data_list[middle_index]:
        return True

    else:
        if search_value > data_list[middle_index]:
            return binary_search(data_list[middle_index:],search_value)
        else:
            return binary_search(data_list[:middle_index],search_value)
        
data = random.sample(range(100),10)
# 탐색 알고리즘의 기본 전제조건은 리스트가 정렬되어 있는 상태이다.
data.sort()
data
```

```text
[2, 16, 31, 51, 53, 56, 57, 59, 69, 92]
```

```python
binary_search(data, 57)
```

```text
[2, 16, 31, 51, 53, 56, 57, 59, 69, 92]
[56, 57, 59, 69, 92]
[56, 57]

True
```

```python
binary_search(data, 82)
```

```text
[2, 16, 31, 51, 53, 56, 57, 59, 69, 92]
[56, 57, 59, 69, 92]
[59, 69, 92]
[69, 92]
[69]

False
```