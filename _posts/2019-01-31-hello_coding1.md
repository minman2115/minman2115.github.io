---
layout: post
title: "너비우선탐색 기초개념"
tags: [Python, 자료구조 알고리즘]
comments: true
---

.

ㅇ 학습 시 참고한 도서정보
- 제목 : Hello Coding 그림으로 개념을 이해하는 알고리즘
- 저자&출판사 : 아디트야 바르가바 지음, 김도형 옮김 / 한빛미디어

## 너비우선탐색 구현하기

- 너비우선탐색은 그래프를 이용하여 "x로 가는 최단경로는 무엇일까"와 같은 문제에 답을 할 수 있는 알고리즘이다.
- O(간선(edge) 개수 + 노드의 개수) => O(1)
- 너비우선탐색은 A에서 B로 가는 경로가 있는지 알려준다.
- 만약 경로가 존재한다면 최단 경로도 찾아준다.
- 만약 X까지의 최단경로를 찾는 문제가 있다면 그 문제를 그래프로 모형화하고 너비우선탐색으로 문제를 풀면된다.
- 탐색목록에 추가된 순서대로 사람을 확인해야한다. 그래서 탐색목록은 큐가 되어야 한다. 그러지 않으면 최단경로는 구할 수 없다.
- 누군가를 확인한 다음에는 두번 확인하지 않도록 잘 조치해야한다. 아니면 무한루프에 빠질 수 있다.

- 망고판매상 찾기 예시


```python
graph = {}
graph["you"]  = ["alice","bob","claire"]
graph["bob"]  = ["anuj","peggy"]
graph["alice"]  = ["peggy"]
graph["claire"]  = ["thom","jonny"]
graph["anuj"] = []
graph["peggy"] = []
graph["thom"] = []
graph["jonny"] = []
print(graph)

from collections import deque

def person_is_seller(name):
    return name[-1] == 'm'

def search(name):
    search_queue = deque()
    search_queue += graph[name]
    searched = []
    
    while search_queue:
        person = search_queue.popleft()
        if not person in searched:
            if person_is_seller(person):
                return person + " is a mango seller !"
            else : 
                search_queue += graph[person]
                searched.append(person)
                
    return "no mango seller !"

search('you')
```

    {'you': ['alice', 'bob', 'claire'], 'bob': ['anuj', 'peggy'], 'alice': ['peggy'], 'claire': ['thom', 'jonny'], 'anuj': [], 'peggy': [], 'thom': [], 'jonny': []}
    




    'thom is a mango seller !'


