---
layout: post
title: "삽입정렬 기본개념"
tags: [Python, 자료구조 알고리즘]
comments: true
---

.

Coding_test_training(20220205)

[학습자료]

패스트 캠퍼스 "알고리즘 / 기술면접 완전 정복 올인원 패키지 Online." 강의를 공부하고 정리한 내용입니다.

** URL : https://fastcampus.co.kr/dev_online_algo

[학습내용]

- 삽입정렬을 이용해서 예를들어서 아래와 같은 리스트를 정렬을 해본다고 가정하자.

```text
[5,3,2,4]
```

맨앞에 5는 그냥두고 두번째 데이터인 3부터 시작을 하는데 먼저 5와 비교를 한다. 3이 5보다 작기 때문에 3이랑 5랑 바꿔야 하는데 바로 바꾸지는 않고 5가 맨앞의 인덱스인지 파악을 한다. 만약에 5가 맨 앞의 인덱스가 아니면 그 앞의 인덱스의 수와 또 비교를 하게 된다. 그런데 5가 가장 맨 앞의 인덱스이기 때문에 3과 5를 바꿔주게 된다. 그래서 아래와 같이 정렬이 된다.

```text
STEP 1)
[3,5,2,4]
```

그다음에 세번째 인덱스인 2가 정렬을 하게 되는데 바로 앞에 숫자인 5와 비교를 한다. 당연히 5가 더 크기 때문에 바꿔줘야 하는데 바로 2와 5를 바꾸는게 아니라 5의 앞에 인덱스인 3과 또 비교를 한다. 당연히 3이 더 크기 때문에 바꿔줘야 한다. 3이 가장 맨 앞의 인덱스이기 때문에 이제서야 바꿔주게 된다. 먼저 2와 5랑 바꿔주고 그런 다음에 2랑 3을 또 바꿔주게 된다.

```text
STEP 2)
[3,2,5,4]

STEP 3)
[2,3,5,4]
```

그 다음에 마지막 인덱스 숫자인 4가 앞에 인덱스 숫자들과 비교를 하는데 이번에는 5앞에 인덱스로는 4가 더 크기 때문에 4와 5랑만 바꿔주게 된다.

```text
STEP 4)
[2,3,4,5]
```

- 삽입정렬이란

(1) 삽입 정렬은 두 번째 인덱스부터 시작

(2) 해당 인덱스(key 값) 앞에 있는 데이터(B)부터 비교해서 key 값이 더 작으면, B값을 뒤 인덱스로 복사

(3) 이를 key 값이 더 큰 데이터를 만날때까지 반복, 그리고 큰 데이터를 만난 위치 바로 뒤에 key 값을 이동

- 손코딩

```text
for index in range(데이터길이-1):     <-- 데이터 대소비교 횟수 : 데이터길이
    swap = False
    for index2 in range(index번호+1,0,-1):    <-- 인덱스번호 +1 부터 시작을 해서 맨끝인덱스까지 한칸씩 뒤로가는 for문
        if data[index2] > data[index2 - 1]:
            swap(data[index2], data[index2-1])
        else:
            break
```

** 맨끝인덱스까지 한칸씩 뒤로가는 for문

```python
for index in range(10, 0, -1):
    print (index)
```

```text
10
9
8
7
6
5
4
3
2
1
```

- 실제 코드구현

```python
import random

def insertion_sort(data):
    for index in range(len(data) - 1):
        print("index : ",index)
        for index2 in range(index + 1, 0, -1):
            print("index2 : ",index2)
            if data[index2] < data[index2 - 1]:
                data[index2], data[index2 - 1] = data[index2 - 1], data[index2]
                print(data)
                print("********************")
            else:
                print("********************")
                break
    return data

data_list = random.sample(range(20), 4)
print("origin_data : ", data_list)
print("********************")
print("sorted_data : ", insertion_sort(data_list))
```

```text
origin_data :  [7, 8, 0, 3]
********************
index :  0
index2 :  1
********************
index :  1
index2 :  2
[7, 0, 8, 3]
********************
index2 :  1
[0, 7, 8, 3]
********************
index :  2
index2 :  3
[0, 7, 3, 8]
********************
index2 :  2
[0, 3, 7, 8]
********************
index2 :  1
********************
sorted_data :  [0, 3, 7, 8]
```

- 빅오

반복문이 두개 이므로 n 제곱임

데이터의 길이(n) X 데이터의 길이(n) 만큼 돌기 때문이다.

완전 정렬이 되어 있는 상태라면 최선은 n임