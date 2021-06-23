---
layout: post
title: "일일코테 - 프로그래머스 '입국심사'"
tags: [Python, 자료구조 알고리즘]
comments: true
---

.

Coding_test_training(20210623)

[학습내용]

- 문제설명

n명이 입국심사를 위해 줄을 서서 기다리고 있습니다. 각 입국심사대에 있는 심사관마다 심사하는데 걸리는 시간은 다릅니다.

처음에 모든 심사대는 비어있습니다. 한 심사대에서는 동시에 한 명만 심사를 할 수 있습니다. 가장 앞에 서 있는 사람은 비어 있는 심사대로 가서 심사를 받을 수 있습니다. 하지만 더 빨리 끝나는 심사대가 있으면 기다렸다가 그곳으로 가서 심사를 받을 수도 있습니다.

모든 사람이 심사를 받는데 걸리는 시간을 최소로 하고 싶습니다.

입국심사를 기다리는 사람 수 n, 각 심사관이 한 명을 심사하는데 걸리는 시간이 담긴 배열 times가 매개변수로 주어질 때, 모든 사람이 심사를 받는데 걸리는 시간의 최솟값을 return 하도록 solution 함수를 작성해주세요.

- 제한사항

1) 입국심사를 기다리는 사람은 1명 이상 1,000,000,000명 이하입니다.

2) 각 심사관이 한 명을 심사하는데 걸리는 시간은 1분 이상 1,000,000,000분 이하입니다.

3) 심사관은 1명 이상 100,000명 이하입니다.

- 입출력 예시

n,times,return

6,`[7, 10]`,28

- 문제풀이

결론적으로 문제를 못풀었다. binary search를 실제 어떻게 활용하는지 배울 수 있었다.

문제풀이 프로세스

step 1) 최상의 시나리오 시간과 최악의 시나리오 시간을 산정한다.

start_time = 0

end_time = max(times) * n  

step 2) start_time ~ end_time 전구간을 이진탐색하는데 승객들을 전부 커버가능한 범위에서 최소의 time을 구하도록 구현한다.


```python
def solution(n, times):
    answer = 0
    start_time = 0
    end_time = max(times) * n  
    while start_time <= end_time:
        mid_time = (start_time+end_time)//2
        print("mid_time:",mid_time)
        passenger=0       
        for inspector in times:
            passenger+=(mid_time//inspector)
            if passenger >=n:
                break
        if passenger >=n:
            end_time = mid_time-1
            print("end_time:",end_time)
            answer = mid_time
            print("answer:",answer)
        else:
            start_time=mid_time+1
            print("start_time:",start_time)
    return answer

n=6
times=[7, 10]
solution(n, times)
```

    mid_time: 30
    end_time: 29
    answer: 30
    mid_time: 14
    start_time: 15
    mid_time: 22
    start_time: 23
    mid_time: 26
    start_time: 27
    mid_time: 28
    end_time: 27
    answer: 28
    mid_time: 27
    start_time: 28
    




    28


