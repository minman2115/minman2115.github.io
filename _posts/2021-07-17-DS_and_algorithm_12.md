---
layout: post
title: "일일코테 - 프로그래머스 '멀쩡한 사각형'"
tags: [Python, 자료구조 알고리즘]
comments: true
---

.

Coding_test_training(20210717)

#### 문제 설명

가로 길이가 Wcm, 세로 길이가 Hcm인 직사각형 종이가 있습니다. 종이에는 가로, 세로 방향과 평행하게 격자 형태로 선이 그어져 있으며, 모든 격자칸은 1cm x 1cm 크기입니다. 이 종이를 격자 선을 따라 1cm × 1cm의 정사각형으로 잘라 사용할 예정이었는데, 누군가가 이 종이를 대각선 꼭지점 2개를 잇는 방향으로 잘라 놓았습니다. 그러므로 현재 직사각형 종이는 크기가 같은 직각삼각형 2개로 나누어진 상태입니다. 새로운 종이를 구할 수 없는 상태이기 때문에, 이 종이에서 원래 종이의 가로, 세로 방향과 평행하게 1cm × 1cm로 잘라 사용할 수 있는 만큼만 사용하기로 하였습니다.

가로의 길이 W와 세로의 길이 H가 주어질 때, 사용할 수 있는 정사각형의 개수를 구하는 solution 함수를 완성해 주세요.

#### 제한사항 

W, H : 1억 이하의 자연수

#### 입출력 예

W = 8

H = 12 

result = 80

#### 입출력 예 설명

입출력 예 #1

가로가 8, 세로가 12인 직사각형을 대각선 방향으로 자르면 총 16개 정사각형을 사용할 수 없게 됩니다. 원래 직사각형에서는 96개의 정사각형을 만들 수 있었으므로, 96 - 16 = 80 을 반환합니다.

![1](https://user-images.githubusercontent.com/41605276/126027958-0afd64de-04fc-4097-8ca8-20b983658b11.png)

#### 풀이

참고자료 : https://ddouo.tistory.com/9

step 1) W와 H가 8,12 면 4의 배수 비율로 2:3 크기로 꼭지점이 맞아 떨어진다.

step 2) 비율로 나누어떨어지지 않은 W:H의 경우, 꼭지점 까지 가로질러 가는 선의 개수가 가로선 W개, 세로선 H개이고 꼭지점으로 중복되는 것을 제외하면 결국 잘린 사각형의 갯수가 W+H-1이 된다.

2:3으로 예를 들면 아래 그림과 같이 2+3-1=4 개가 날아가게 된다.

![2](https://user-images.githubusercontent.com/41605276/126028670-6d0d8c89-0882-4342-b3e4-cce8f66c0dba.PNG)


4:6, 6:9, 8:12의 경우 모두 2:3의 경우가 각각 2, 3, 4번씩 반복되는데, 이는 두 비의 최대 공약수이고 최대 공약수 만큼 꼭지점에서 만나게 된다는것을 알 수 있다. 

step 3) 자연스럽게 잘린 사각형의 개수는 (X, Y의 수 - 꼭지점에서 만나게 되는 횟수)가 되고 정리하면 (X + Y - 두수의 최대 공약수) 라는 공식을 잡을 수 있다.

step 4) 그래서 함수를 짠다면 아래와 같은 프로세스로 처리하도록 하면 된다.

가로와 세로의 공약수를 구한다 ---> 가로 * 세로 - (가로 + 세로 - 두수의 공약수)를 리턴한다.


```python
def solution(w,h):
    # step 1) 가로, 세로의 약수를 저장할 리스트
    w_list=[]
    h_list=[]

    # step 2) 약수 저장 
    for i in range(1,w+1):
        if w%i==0:
            w_list.append(i)

    for i in range(1,h+1):
        if h%i==0:
            h_list.append(i)
    
    #step 3) 두 리스트의 중복값을 리스트에 저장
    hw_list = set(w_list).intersection(h_list)
    
    #step 4) 최대공약수 산출
    max_gong= max(hw_list)
    
    return w*h-(w+h-max_gong)
```

아래와 같이 코드수를 줄여볼 수도 있다.


```python
def solution(w,h):
    w_list=[]
    h_list=[]
    
    search_value=max(w,h)
    for i in range(1,search_value+1):
        if i <= h:
            if h%i==0:
                h_list.append(i)               
        if i <= w:
            if w%i==0:
                w_list.append(i)
    
    return w*h-(w+h-max(set(w_list).intersection(h_list)))
```

또는 아래와 같이 math 라이브러리의 gcd라는 함수를 이용해서 최대공약수를 구하는 풀이를 할 수도 있다.


```python
from math import gcd

def solution(w,h):
    return w*h-(w+h-gcd(w,h))
```
