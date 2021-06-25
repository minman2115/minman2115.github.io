---
layout: post
title: "일일코테 - 프로그래머스 '짝지어 제거하기'"
tags: [Python, 자료구조 알고리즘]
comments: true
---

.

Coding_test_training(20210625)

[학습내용]

문제 설명

짝지어 제거하기는, 알파벳 소문자로 이루어진 문자열을 가지고 시작합니다. 먼저 문자열에서 같은 알파벳이 2개 붙어 있는 짝을 찾습니다. 그다음, 그 둘을 제거한 뒤, 앞뒤로 문자열을 이어 붙입니다. 이 과정을 반복해서 문자열을 모두 제거한다면 짝지어 제거하기가 종료됩니다. 문자열 S가 주어졌을 때, 짝지어 제거하기를 성공적으로 수행할 수 있는지 반환하는 함수를 완성해 주세요. 성공적으로 수행할 수 있으면 1을, 아닐 경우 0을 리턴해주면 됩니다.

예를 들어, 문자열 S = baabaa 라면

b aa baa --> bb aa --> aa -->

의 순서로 문자열을 모두 제거할 수 있으므로 1을 반환합니다.

제한사항

1) 문자열의 길이 : 1,000,000이하의 자연수

2) 문자열은 모두 소문자로 이루어져 있습니다

입출력 예

`s='baabaa',result = 1`

`s='cdcd',result=0`

입출력 예 설명

입출력 예 #1

위의 예시와 같습니다.

입출력 예 #2

문자열이 남아있지만 짝지어 제거할 수 있는 문자열이 더 이상 존재하지 않기 때문에 0을 반환합니다.

- 최초 문제풀이

직관적으로 풀었는데 시간초과가 났다. 성능테스트 빵점이었다.

생각없이 풀지말고 문제의도를 잘 파악해서 풀어야겠다는 생각이 들었다.


```python
def solution(s):
    answer = -1
    order = 0
    while True:
        if s[order] == s[order+1]:
            s=s[0:order]+s[order+2:]
            order = max(0,order-1)
        else:
            order+=1
        if order >= len(s)-1:
            break
            
    if len(s)==0:
        answer = 1
        
    else:
        answer = 0
        
    return answer

s='baabaa'
solution(s)
```




    1



- 수정후 문제풀이

결국에는 스텍 자료구조를 이용해서 문제를 해결하는게 목적인 문제였다.


```python
def solution(s):
    answer=-1
    my_stack = []
    for elem in s:
        if len(my_stack) == 0: 
            my_stack.append(elem)
        elif my_stack[-1] == elem: 
            my_stack.pop()
        else: 
            my_stack.append(elem)
    if len(my_stack) == 0: 
        answer = 1
    else: 
        answer = 0
    return answer

s='baabaa'
solution(s)
```




    1


