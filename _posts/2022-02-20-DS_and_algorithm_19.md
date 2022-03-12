---
layout: post
title: "병합정렬 기본개념"
tags: [Python, 자료구조 알고리즘]
comments: true
---

.

Coding_test_training(20220220)

[학습자료]

패스트 캠퍼스 "알고리즘 / 기술면접 완전 정복 올인원 패키지 Online." 강의를 공부하고 정리한 내용입니다.

** URL : https://fastcampus.co.kr/dev_online_algo

[학습내용]

- 병합정렬(merge sort) 개요

문제를 잘개 쪼개는 분할정복 알고리즘을 사용하는 정렬 알고리즘이라고 할 수 있다. memorization을 사용하지는 않는다 따라서 동적계획법을 사용한 알고리즘은 아니다. 

- 병합정렬 예시 프로세스

아래와 같은 리스트의 데이터를 정렬한다고 가정하자

```text
test_data = [2,8,5,4]
```

STEP 1) 먼저 [2,8],[5,4]로 짜른다.

STEP 2) [2,8]을 다시 [2],[8]로 짜른다.

STEP 3) [2]와 [8]을 정렬해서 합친다. 그러면 [2,8]이 된다.

STEP 4) [5,4]를 [5],[4]로 짜른다.

STEP 5) [5]와 [4]을 정렬해서 합친다. 그러면 [4,5]가 된다.

STEP 6) [2,8]과 [4,5]를 합친다.

```text
step 6-1) 2보다 4가 크기 때문에 [2]

step 6-2) 8이 4보다 크기 때문에 [2,4]

step 6-3) 8이 5보다 크기 때문에 [2,4,5]

step 6-4) 남은거는 8밖에 없으니 [2,4,5,8]
```

- 실제 코드로 구현해보기

병합정렬은 함수 두개로 구현을 한다.

함수 1. 메인함수 겸 재귀용법으로 데이터를 쪼개는 함수

함수 2. 나누어진 데이터를 병합하는 함수

```python
# 함수 1. 메인함수 겸 재귀용법으로 데이터를 쪼개는 함수
def merge_sort(data):
    if len(data) <= 1:
        return data
    medium = int(len(data) / 2)
    left = merge_sort(data[:medium])
    right = merge_sort(data[medium:])
    return merge(left, right)

#함수 2. 나누어진 데이터를 병합하는 함수
def merge(left, right):
    merged_result = list()
    left_index, right_index = 0, 0
    
    # case1 : left/right 둘다 있을때
    while (len(left) > left_index) and (len(right) > right_index):
        if left[left_index] > right[right_index]:
            merged_result.append(right[right_index])
            right_index += 1
        else:
            merged_result.append(left[left_index])
            left_index += 1

    # case2 : left 데이터만 남아있을때
    while len(left) > left_index:
        merged_result.append(left[left_index])
        left_index += 1
        
    # case3 : right 데이터만 남아있을때
    while len(right) > right_index:
        merged_result.append(right[right_index])
        right_index += 1
    
    return merged_result

test_data = [2,8,5,4]
merge_sort(test_data)
```

```text
[2,4,5,8]
```

- 시간복잡도

가지치기한 각 단계별로는 각각 O(n)인데 이게 log2의 n개 만큼 만들어지기 때문에 결론적으로는 O(n log n) 

삽입정렬, 선택정렬보다는 빠른 시간복잡도를 보인다.