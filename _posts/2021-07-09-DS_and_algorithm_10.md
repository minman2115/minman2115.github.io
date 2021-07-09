---
layout: post
title: "일일코테 - 프로그래머스 '문자열 압축'"
tags: [Python, 자료구조 알고리즘]
comments: true
---

.

Coding_test_training(20210709)

#### 문제 설명

데이터 처리 전문가가 되고 싶은 "어피치"는 문자열을 압축하는 방법에 대해 공부를 하고 있습니다. 최근에 대량의 데이터 처리를 위한 간단한 비손실 압축 방법에 대해 공부를 하고 있는데, 문자열에서 같은 값이 연속해서 나타나는 것을 그 문자의 개수와 반복되는 값으로 표현하여 더 짧은 문자열로 줄여서 표현하는 알고리즘을 공부하고 있습니다.
간단한 예로 "aabbaccc"의 경우 "2a2ba3c"(문자가 반복되지 않아 한번만 나타난 경우 1은 생략함)와 같이 표현할 수 있는데, 이러한 방식은 반복되는 문자가 적은 경우 압축률이 낮다는 단점이 있습니다. 예를 들면, "abcabcdede"와 같은 문자열은 전혀 압축되지 않습니다. "어피치"는 이러한 단점을 해결하기 위해 문자열을 1개 이상의 단위로 잘라서 압축하여 더 짧은 문자열로 표현할 수 있는지 방법을 찾아보려고 합니다.

예를 들어, "ababcdcdababcdcd"의 경우 문자를 1개 단위로 자르면 전혀 압축되지 않지만, 2개 단위로 잘라서 압축한다면 "2ab2cd2ab2cd"로 표현할 수 있습니다. 다른 방법으로 8개 단위로 잘라서 압축한다면 "2ababcdcd"로 표현할 수 있으며, 이때가 가장 짧게 압축하여 표현할 수 있는 방법입니다.

다른 예로, "abcabcdede"와 같은 경우, 문자를 2개 단위로 잘라서 압축하면 "abcabc2de"가 되지만, 3개 단위로 자른다면 "2abcdede"가 되어 3개 단위가 가장 짧은 압축 방법이 됩니다. 이때 3개 단위로 자르고 마지막에 남는 문자열은 그대로 붙여주면 됩니다.

압축할 문자열 s가 매개변수로 주어질 때, 위에 설명한 방법으로 1개 이상 단위로 문자열을 잘라 압축하여 표현한 문자열 중 가장 짧은 것의 길이를 return 하도록 solution 함수를 완성해주세요.

#### 제한사항

- s의 길이는 1 이상 1,000 이하입니다.


- s는 알파벳 소문자로만 이루어져 있습니다.

#### 입출력 예

s result

"aabbaccc" 7

"ababcdcdababcdcd" 9

"abcabcdede" 8

"abcabcabcabcdededededede" 14

"xababcdcdababcdcd" 17

#### 입출력 예에 대한 설명

입출력 예 #1

문자열을 1개 단위로 잘라 압축했을 때 가장 짧습니다.

입출력 예 #2

문자열을 8개 단위로 잘라 압축했을 때 가장 짧습니다.

입출력 예 #3

문자열을 3개 단위로 잘라 압축했을 때 가장 짧습니다.

입출력 예 #4

문자열을 2개 단위로 자르면 "abcabcabcabc6de" 가 됩니다.

문자열을 3개 단위로 자르면 "4abcdededededede" 가 됩니다.

문자열을 4개 단위로 자르면 "abcabcabcabc3dede" 가 됩니다.

문자열을 6개 단위로 자를 경우 "2abcabc2dedede"가 되며, 이때의 길이가 14로 가장 짧습니다.

입출력 예 #5

문자열은 제일 앞부터 정해진 길이만큼 잘라야 합니다.

따라서 주어진 문자열을 x / ababcdcd / ababcdcd 로 자르는 것은 불가능 합니다.

이 경우 어떻게 문자열을 잘라도 압축되지 않으므로 가장 짧은 길이는 17이 됩니다.

#### 문제풀이

** 참고자료 : 

https://github.com/tjdud0123/daily_algorithm/blob/master/%EC%B9%B4%EC%B9%B4%EC%98%A4%EB%B8%94%EB%9D%BC%EC%9D%B8%EB%93%9C2020/1%EB%B2%88_%EB%AC%B8%EC%9E%90%EC%97%B4%EC%95%95%EC%B6%95.py

솔루션 1) stack으로 풀이


```python
def solution(s):
    length=len(s)
    string_position=0
    count_position=1
    answer_list=[length]
    
    for size in range(1, length):
        compressed=''
        splited_list = [s[i:i+size] for i in range(0,length,size)]
        my_stack=[[splited_list[0],1]]
        
        for elem in splited_list[1:]:
            if my_stack[-1][string_position] != elem :
                my_stack.append([elem,1])
            else:
                my_stack[-1][count_position] += 1
        
        print(my_stack)
        print([str(cnt)+string if cnt > 1 else string for string, cnt in my_stack])
        
        compressed+= "".join([str(cnt)+string if cnt > 1 else string for string, cnt in my_stack])
        
        answer_list.append(len(compressed))
        
    return min(answer_list)

solution('aabbaccc')
```

    [['a', 2], ['b', 2], ['a', 1], ['c', 3]]
    ['2a', '2b', 'a', '3c']
    [['aa', 1], ['bb', 1], ['ac', 1], ['cc', 1]]
    ['aa', 'bb', 'ac', 'cc']
    [['aab', 1], ['bac', 1], ['cc', 1]]
    ['aab', 'bac', 'cc']
    [['aabb', 1], ['accc', 1]]
    ['aabb', 'accc']
    [['aabba', 1], ['ccc', 1]]
    ['aabba', 'ccc']
    [['aabbac', 1], ['cc', 1]]
    ['aabbac', 'cc']
    [['aabbacc', 1], ['c', 1]]
    ['aabbacc', 'c']
    




    7



솔루션 2) count를 이용해서 푸는 방법


```python
def solution(s):
    length=len(s)
    answer_list=[length]
    
    for size in range(1,length):
        compressed=''
        splited_list = [s[i:i+size] for i in range(0,length,size)]
        count = 1
        
        for j in range(1,len(splited_list)):
            prev=splited_list[j-1]
            cur=splited_list[j]
            
            if prev==cur:
                count+=1
            else:
                compressed+=(str(count)+prev) if count > 1 else prev
                count=1
                  
        compressed += (str(count)+splited_list[-1]) if count > 1 else splited_list[-1]
        answer_list.append(len(compressed))
        
    return min(answer_list)
        
solution('aabbaccc')
```

    2a2ba3c
    aabbaccc
    aabbaccc
    aabbaccc
    aabbaccc
    aabbaccc
    aabbaccc
    




    7


