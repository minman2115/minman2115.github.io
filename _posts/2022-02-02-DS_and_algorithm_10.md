---
layout: post
title: "Bubble sort 기본개념"
tags: [Python, 자료구조 알고리즘]
comments: true
---

.

Coding_test_training(20220202)

[학습자료]

패스트 캠퍼스 "알고리즘 / 기술면접 완전 정복 올인원 패키지 Online." 강의를 공부하고 정리한 내용입니다.

** URL : https://fastcampus.co.kr/dev_online_algo

[학습내용]

### (1) 데이터가 네 개 일때 sorting 예시
  - 예: data_list = [1, 9, 3, 2]
    - 1차 로직 적용
      - 1 와 9 비교, 자리바꿈없음 [1, 9, 3, 2]
      - 9 와 3 비교, 자리바꿈 [1, 3, 9, 2]
      - 9 와 2 비교, 자리바꿈 [1, 3, 2, 9]
    - 2차 로직 적용
      - 1 와 3 비교, 자리바꿈없음 [1, 3, 2, 9]
      - 3 과 2 비교, 자리바꿈 [1, 2, 3, 9]
      - 3 와 9 비교, 자리바꿈없음 [1, 2, 3, 9]
    - 3차 로직 적용
      - 1 과 2 비교, 자리바꿈없음 [1, 2, 3, 9]
      - 2 과 3 비교, 자리바꿈없음 [1, 2, 3, 9]
      - 3 과 9 비교, 자리바꿈없음 [1, 2, 3, 9]

### (2) 손코딩

```text
for index in range(데이터길이-1):     <-- 데이터 대소비교 횟수 : 데이터길이 -1
    swap = False
    for index2 in range(데이터길이-index-1):    <-- 데이터 대소비교 반복 횟수 : 데이터길이 - index -1
        if 앞데이터 > 뒤데이터:                   <-- 제일 큰 데이터가 가장 오른쪽으로 가기 때문에 대소 비교 반복횟수는 1씩 감소함
            swap(앞데이터,뒤데이터)
            swap = True
        
    if swap == False:       <-- 정렬할게 없으면 sorting 작업을 끝내버린다.
       break
```

### (3) 실제 구현코드

```python
import random

def bubblesort(data):
    for index in range(len(data) - 1):
        swap = False
        print("index:",index)
        for index2 in range(len(data) - index - 1):
            print("index2:",index2)
            if data[index2] > data[index2 + 1]:
                data[index2], data[index2 + 1] = data[index2 + 1], data[index2]
                swap = True
            print(data)
            print("********************")
        
        if swap == False:
            break
    return data

data_list = random.sample(range(20), 4)
print("origin_data : ", data_list)
print("********************")
print("sorted_data : ", bubblesort(data_list))
```

실행결과

```text
origin_data :  [7, 1, 9, 5]
********************
index: 0
index2: 0
[1, 7, 9, 5]
********************
index2: 1
[1, 7, 9, 5]
********************
index2: 2
[1, 7, 5, 9]
********************
index: 1
index2: 0
[1, 7, 5, 9]
********************
index2: 1
[1, 5, 7, 9]
********************
index: 2
index2: 0
[1, 5, 7, 9]
********************
sorted_data :  [1, 5, 7, 9]
```

```text
origin_data :  [17, 4, 10, 15]
********************
index: 0
index2: 0
[4, 17, 10, 15]
********************
index2: 1
[4, 10, 17, 15]
********************
index2: 2
[4, 10, 15, 17]
********************
index: 1
index2: 0
[4, 10, 15, 17]
********************
index2: 1
[4, 10, 15, 17]
********************
sorted_data :  [4, 10, 15, 17]
```

### (4) 빅오

반복문이 두개 이므로 n 제곱임

데이터의 길이(n) X 데이터의 길이(n) 만큼 돌기 때문이다.

완전 정렬이 되어 있는 상태라면 최선은 n임