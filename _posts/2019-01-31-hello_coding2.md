---
layout: post
title: "다익스트라 알고리즘 기초개념"
tags: [Python, 자료구조 알고리즘]
comments: true
---

다익스트라 알고리즘 기초개념 요약 및 Python 프로그래밍 구현연습

ㅇ 'Hello Coding 그림으로 개념을 이해하는 알고리즘'을 읽고 공부한 내용을 정리한 것으로 일부 주관적이며 오류가 있을 수 있습니다.

ㅇ 참고 도서정보
- 제목 : Hello Coding 그림으로 개념을 이해하는 알고리즘
- 저자&출판사 : 아디트야 바르가바 지음, 김도형 옮김 / 한빛미디어


## 다익스트라 알고리즘

- 다익스트라 알고리즘은 방향성 비순환 그래프 또는 사이클을 가진 경우에는 가중치가 양수일때만 적용된다.


- 다익스트라 알고리즘에서 각 간선을 가중치라고 한다.


- 다익스트라 알고리즘의 4단계

1단계 = 가장 가격이 싼 정점, 즉 도달하는데 시간이 가장 적게 걸리는 정점을 찾는다.

2단계 = 이 정점의 이웃 정점에 대해 현재의 가격보다 더 싼 경로가 존재하는지 확인한다. 만약에 존재한다면 가격을 수정한다.

3단계 = 그래프 상의 모든 정점에 대해 이러한 일을 반복한다.

4단계 = 최종경로를 계산한다.

- 알고리즘 구현

![1](https://user-images.githubusercontent.com/41605276/52030195-099bca80-255a-11e9-866a-665c3296584d.jpg)

![2](https://user-images.githubusercontent.com/41605276/52030202-13bdc900-255a-11e9-912b-53423093b07a.jpg)


```python
from collections import deque

## 그래프를 나타내는 해시테이블을 만드는 코드
graph['출발점'] = {}
graph['출발점']['A'] = 6
graph['출발점']['B'] = 2
# print(graph['출발점'].keys())

graph['A'] = {}
graph['A']['도착점'] = 1

graph['B'] = {}
graph['B']['A'] = 3
graph['B']['도착점'] = 5

graph['도착점'] = {}



## 가격을 나타내는 해시테이블을 만드는 코드
infinity = float("inf")
costs = {}
costs['A'] = 6
costs['B'] = 2
costs['도착점'] = infinity



## 부모를 나타내는 해시테이블을 만드는 코드
parents = {}
parents['A'] = "출발점"
parents['B'] = "출발점"
parents['도착점'] = None

## 각 정점은 한번씩만 처리해야 하므로 처리한 정점을 추적하기 위한 배열
processed = []

## find_lowest_cost_node 함수 구현

def find_lowest_cost_node(costs):
    lowest_cost = float("inf")
    lowest_cost_node = None
    
    ## 모든 정점을 확인
    for node in costs:
        ## 여기서 node는 A나 B처럼 키값이 나오게 된다.
        cost = costs[node]
        
        if cost < lowest_cost and node not in processed:
            lowest_cost = cost
            lowest_cost_node = node
    
    return lowest_cost_node

##아직 처리하지 않은 가장 싼 정점을 찾는다.
node = find_lowest_cost_node(costs)

## 모든 정점을 처리하면 반복문이 종료된다.
while node is not None:
    cost = costs[node]
    neighbors = graph[node]
    
    ## 모든 이웃에 대해 반복한다.
    for n in neighbors.keys():
        new_cost = cost + neighbors[n]
        
        ##만약 이 정점을 지나는 것이 가격이 더 싸다면
        if costs[n] > new_cost:
            ## 정점의 가격을 갱신하고
            costs[n] = new_cost
            ## 부모를 이 정점으로 새로 설정한다.
            parents[n] = node
    
    ## 정점을 처리한 사실을 기록한다.
    processed.append(node)
    ## 다음으로 처리할 정점을 찾아 반복한다.
    node = find_lowest_cost_node(costs)
```


```python
print("Cost from the start to each node:")
print(costs)
```

    Cost from the start to each node:
    {'A': 5, 'B': 2, '도착점': 6}
    
