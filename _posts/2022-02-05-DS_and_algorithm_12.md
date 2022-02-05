---
layout: post
title: "선택정렬 기본개념"
tags: [Python, 자료구조 알고리즘]
comments: true
---

.

Coding_test_training(20220205)

[학습자료]

패스트 캠퍼스 "알고리즘 / 기술면접 완전 정복 올인원 패키지 Online." 강의를 공부하고 정리한 내용입니다.

** URL : https://fastcampus.co.kr/dev_online_algo

[학습내용]

- 선택정렬 과정

STEP 1) 주어진 데이터 중, 최소값을 찾음

STEP 2) 해당 최소값을 데이터 맨 앞에 위치한 값과 교체함

STEP 3) 맨 앞의 위치를 뺀 나머지 데이터를 동일한 방법으로 반복함

- 선택정렬 예제 시나리오

아래에 리스트 데이터를 선택정렬하는 시나리오를 가정해보자.

```text
[5,4,3,1]
```

먼저 가장 좌측의 인덱스인 5를 기준으로 정렬을 할 것이다. 5와 그 뒤에 있는 모든 인덱스의 숫자들 4,3,1과 비교를 할 것이다. 비교 기준값인 5를 포함해서 4,3,1 중 가장 작은 숫자가 1이다. 그러므로 1과 5를 바꿔준다.

그러면 아래와 같이 정렬될 것이다.

전체 리스트 중에 최소값을 가장 좌측을 이동시켰기 때문에 사실상 1은 언터쳐블이고 오른쪽에 있는 4부터 생각을 하면 된다.

```text
[1,4,3,5]
```

이번에는 4를 기준으로해서 나머지 값들과 비교를 해보면 4,3,5 중에 최소값은 3이다. 그러므로 아래와 같이 3을 이 세개의 숫자중에 가장 좌측으로 빼준다.

```text
[1,3,4,5]
```

그리고 마지막 5는 기준으로 잡고 비교를 하지 않아도 이미 정렬이 전부 이루어지게 된다.

(이말은 전체 데이터 길이의 -1 값까지만 기준점으로 잡고 비교하면 된다는 것이다. 굳이 가장 뒤에 있는 값까지 기준으로 잡고 비교를 안해도 된다는 말이다.)

- 손코딩

```text
for stand in range(데이터길이-1):     <-- 데이터길이 만큼 비교 기준값을 잡는다
    lowest = stand
    for index in range(stand+1, 데이터길이):
        if data[lowest] > data[index]:
            lowest = index
    swap(lowest,stand)
```

for index in range(stand+1, 데이터길이) 헷갈리는 이부분 코드 실행 예시

```python
for index in range(1,4):
    print(index)
```

```text
1
2
3
```

- 실제 코드구현

```python
import random

def selection_sort(data):
    for stand in range(len(data) - 1):
        lowest = stand
        for index in range(stand + 1, len(data)):
            if data[lowest] > data[index]:
                lowest = index
        data[lowest], data[stand] = data[stand], data[lowest]
        print("data:",data)
        print("********************")
    return data

data_list = random.sample(range(20), 4)
print("origin_data : ", data_list)
print("********************")
print("sorted_data : ", selection_sort(data_list))
```

```text
origin_data :  [19, 7, 2, 16]
********************
data: [2, 7, 19, 16]
********************
data: [2, 7, 19, 16]
********************
data: [2, 7, 16, 19]
********************
sorted_data :  [2, 7, 16, 19]
```

- 빅오

반복문이 두개 이므로 n 제곱임
