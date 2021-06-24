---
layout: post
title: "일일코테 - 프로그래머스 '더 맵게'"
tags: [Python, 자료구조 알고리즘]
comments: true
---

.

Coding_test_training(20210624)

[학습내용]

문제설명

매운 것을 좋아하는 Leo는 모든 음식의 스코빌 지수를 K 이상으로 만들고 싶습니다. 모든 음식의 스코빌 지수를 K 이상으로 만들기 위해 Leo는 스코빌 지수가 가장 낮은 두 개의 음식을 아래와 같이 특별한 방법으로 섞어 새로운 음식을 만듭니다.

`섞은 음식의 스코빌 지수 = 가장 맵지 않은 음식의 스코빌 지수 + (두 번째로 맵지 않은 음식의 스코빌 지수 * 2)`

Leo는 모든 음식의 스코빌 지수가 K 이상이 될 때까지 반복하여 섞습니다.
Leo가 가진 음식의 스코빌 지수를 담은 배열 scoville과 원하는 스코빌 지수 K가 주어질 때, 모든 음식의 스코빌 지수를 K 이상으로 만들기 위해 섞어야 하는 최소 횟수를 return 하도록 solution 함수를 작성해주세요.

제한 사항

1) scoville의 길이는 2 이상 1,000,000 이하입니다.


2) K는 0 이상 1,000,000,000 이하입니다.


3) scoville의 원소는 각각 0 이상 1,000,000 이하입니다.


4) 모든 음식의 스코빌 지수를 K 이상으로 만들 수 없는 경우에는 -1을 return 합니다.

입출력 예

scoville=[1, 2, 3, 9, 10, 12]

K=7

return=2

입출력 예 설명

스코빌 지수가 1인 음식과 2인 음식을 섞으면 음식의 스코빌 지수가 아래와 같이 됩니다.

새로운 음식의 스코빌 지수 = 1 + (2 * 2) = 5

가진 음식의 스코빌 지수 = [5, 3, 9, 10, 12]

스코빌 지수가 3인 음식과 5인 음식을 섞으면 음식의 스코빌 지수가 아래와 같이 됩니다.

새로운 음식의 스코빌 지수 = 3 + (5 * 2) = 13

가진 음식의 스코빌 지수 = [13, 9, 10, 12]

모든 음식의 스코빌 지수가 7 이상이 되었고 이때 섞은 횟수는 2회입니다.

- 최초 문제풀이

아래와 같이 그냥 직관적으로 풀었다. 무난하게 풀었다고 생각했는데 성능테스트 결과가 빵점이었다.

오만한 생각이었다..


```python
def solution(scoville, K):
    answer = 0
    while True:
        scoville.sort(reverse=True)
        scoville_index=scoville[-1]+(scoville[-2]*2)
        scoville.pop()
        scoville.pop()
        scoville.append(scoville_index)
        answer+=1
        if len(scoville) == 1 and min(scoville) < K:
            answer = -1
            break
        if min(scoville) >= K:
            break
    return answer

scoville=[1, 2, 3, 9, 10, 12]
K=7
solution(scoville, K)
```




    2



해결방법은 heapq를 사용해서 문제를 풀어야 성능테스트를 통과할 수 있다.

list 관련 문제가 나오면 heaq를 적극적으로 사용해야겠다.

heap 기초개념은 아래 URL 내용을 참고할 것

https://minman2115.github.io/DS_and_algorithm_02/

- 수정 후 문제풀이


```python
import heapq

def solution(scoville, K):
    answer = 0
    heapq.heapify(scoville)
    while scoville[0]<K:
        min_value=heapq.heappop(scoville)
        second_min_value=heapq.heappop(scoville)
        scoville_index=min_value+(second_min_value*2)
        heapq.heappush(scoville, scoville_index)
        answer+=1       
        if len(scoville) == 1 and scoville[0]<K:
            answer=-1
            break
        if scoville[0] >= K:
            break
    return answer

scoville=[1, 2, 3, 9, 10, 12]
K=7
solution(scoville, K)
```




    2


