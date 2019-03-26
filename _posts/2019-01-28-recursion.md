---
layout: post
title: "recursion 개념을 이용한 알고리즘 연습문제 풀이"
tags: [Python]
comments: true
---

.


![2](https://user-images.githubusercontent.com/41605276/51818756-5cd00c00-2313-11e9-85ea-6d2e4874b067.png)


```python
def solution(n):
    
    result_count = 0
    
    for data in range(10**n):
        data = str(data)
            
        if data == data[::-1]:
            result_count += 1
            
    return result_count
```


```python
solution(2)
```




    19



- 문제식별 : 최악의 조건에서 프로세싱 속도가 저하되는 현상

solution : 규칙을 찾아내면 복잡도를 상당히 줄일 수 있다.

- 1자리 수일때 : 0,1,2,3,4,5,6,7,8,9 -> 10개
- 2자리 수일때 : 11,22,33,44,55,66,77,88,99 ->9개
- 3자리 수일때 : 1x1, 2x2, 3x3, ... , 9x9 -> 9 X 10개
- 4자리 수일때 : 1xx1, 2xx2, 3xx3, ... , 9xx9 -> 9 X 10개
- 5자리 수일때 : 1xyx1, 2xyx2, 3xyx3, ... , 9xyx9 -> 9 X 10 X 10개
- 6자리 수일때 : 1xyyx1, 2xyyx2, 3xyyx3, ... , 9xyyx9 -> 9 X 10 X 10개


```python
# 재귀를 응용한 문제풀이
def solution(n):
    if n == 1:
        return 10
    
    return solution(n-1) + 9*(10**((n-1)//2))
```


```python
solution(1)
```




    10




```python
solution(2)
```




    19




```python
solution(3)
```




    109


