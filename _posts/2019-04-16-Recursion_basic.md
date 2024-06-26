﻿---
layout: post
title: "Recursive call 기본개념"
tags: [Python, 자료구조 알고리즘]
comments: true
---

.

[학습 참고자료]

아래에 두개의 학습 참고자료로 공부한 것을 정리한 내용입니다.

1) 패스트캠퍼스 양태환 강사님 강의자료 : https://github.com/ythwork

2) 패스트캠퍼스 "알고리즘 / 기술면접 완전 정복 올인원 패키지 Online." 강의내용

** URL : https://fastcampus.co.kr/dev_online_algo

[학습내용]

- recursive call(재귀용법) 이란

(1) 함수 안에서 동일한 함수를 호출하는 형태를 말한다.

(2) 재귀를 사용할때 매개변수를 줄여주는 조건을 걸어줘야 무한루프가 돌지 않는다.

(3) 정렬 알고리즘 등 여러 알고리즘에 사용되는 용법이기 때문에 익숙해져야 한다.


- recursive call 구현양상

일반적인 구현양상 1.

```python
def function(입력값):
    if 입력값 > 일정값: # 입력값이 일정 값 이상이면
        return function(입력값 - 1) # 입력값보다 작은 값
    else:
        return 일정값, 입력값, 또는 특정값 # 재귀 호출 종료
```

일반적인 구현양상 2.

```python
def function(입력값):
    if 입력값 <= 일정값:              # 입력값이 일정 값보다 작으면
        return 일정값, 입력값, 또는 특정값              # 재귀 호출 종료
    function(입력값보다 작은 값)
    return 결과값
```

- 실제 코드구현 예시

(1) 전형적인 예시 : factorial

```python
def factorial(num):
    if num <= 1:
        return num
    
    return num * factorial(num - 1)

for num in range(4):
    print (factorial(num))
```

```text
0
1
2
6
```

(2) recursion의 대표적인 예시2


ex) 

3! = 3 * 2 * 1

3! = 3 * 2!


이런 성질때문에 recursion을 쓸 수 있다.

종료조건은 n이 1일때

- factorial 파이썬 코드 구현

손으로 스텍프레임을 그려보면 좀더 쉽게 이해할 수 있다.


```python
def factorial(n):
    
    if n <= 1:
        return 1
    
    return n * factorial(n-1)

for i in range(1,6):
    print(factorial(i), end='  ')
```

    1  2  6  24  120  

- 재귀함수 구현할때 알아야 할 것

1) 점화식을 구현

2) base case 설계(언제 멈출거냐)

- 재귀 호출은 메모리 stack 활용의 전형적인 예시라고 할 수 있음

![recursivecall](https://user-images.githubusercontent.com/41605276/152670394-1f3f4014-17d4-4a24-b91d-7f227f476b1c.png)

** 실제 라이브코드 예시 - [코드분석]( http://pythontutor.com/live.html#code=%23%20factorial%20%ED%95%A8%EC%88%98%20%EC%95%88%EC%97%90%EC%84%9C%20factorial%20%ED%95%A8%EC%88%98%EB%A5%BC%20%ED%98%B8%EC%B6%9C%0Adef%20factorial%28num%29%3A%0A%20%20%20%20if%20num%20%3E%201%3A%0A%20%20%20%20%20%20%20%20return%20num%20*%20factorial%28num%20-%201%29%0A%20%20%20%20else%3A%0A%20%20%20%20%20%20%20%20return%20num%0A%0Afactorial%285%29&cumulative=false&curInstr=22&heapPrimitives=false&mode=display&origin=opt-live.js&py=3&rawInputLstJSON=%5B%5D&textReferences=false)

반드시 알아할 것은 파이썬에서 재귀 함수는 한번 호출했을때 recursive하게 콜할 수 있는 깊이가 1000회까지로 고정되어 있다. 1000회를 넘어서 콜을 하게 되면 에러가 발생한다.

- 재귀 기념을 이용한 각종응용 사례

(1) fibonacci 수열

점화식 : 이번 피보나치 수 = 전전 피보나치 수 + 이전 피보나치 수 

fibo(n) = fibo(n-2) + fibo(n-1)

base case

첫 번째 피보나치 수 : 0

두 번째 피보나치 수 : 1

피보나치 수열 파이썬 코드로 구현

```python
def fibonacci(n): ## 인자 n을 넣게되면 n번째 피포나치 수열을 출력하게 한다. 
   
    # base case
    # n이 1 이면 0
    
    if n == 1:
        return 0
        
    # n이 2 이면 1
    if n == 2:
        return 1
    
    return fibonacci(n-2) + fibonacci(n-1)

n=10
for i in range(1, n+1):
    print(fibonacci(i), end= '  ')
```

    0  1  1  2  3  5  8  13  21  34  

피보나치 수열 문제점

점화식 실행 시 스텍프레임을 불필요하게 많이 쌓게 되므로(이말은 메모리 공간에 계속 데이터가 저장이 된 얘기) 성능상의 이슈가 발생한다. 

그래서 가능하다면 반복문으로 대체하는 것이 빠르다.

이런 피보나치 수열의 문제점을 해결하기 위해 나온 개념이 다이나믹 프로그래밍이라는 개념이다.
recursion을 쓰되 한번 호출한 스텍은 기억해두었다가 리턴해주자 라는 메모리제이션
아니면 특정 메모리에 저장해두었다가 리턴해주자 라는 바텀 업 이라는 개념이 있다.

반복문은 메모리 공간에 하나의 데이터가 저장이 된다.

피보나치 수열 반복문으로 대체해보기


```python
def fibo_iter(n):
    
    if n == 1:
        return 0
        
    if n == 2:
        return 1
    
    last = 0
    temp = 0
    current = 1
    for number in range(n-2):
        temp = current
        current = last + current
        last = temp
        
#   또는 아래와 같은 코드로도 구현이 가능하다.        
#   last, current = current, last + current
        
    return current

for i in range(1, 11):
    print(fibo_iter(i), end = "  ")
```

    0  1  1  2  3  5  8  13  21  34  

(2) 하노이타워

하노이타워 목적 : from에 있는 원반을 그대로 to로 옮기는 것이다.

![1](https://user-images.githubusercontent.com/41605276/56191133-be785a80-6066-11e9-8240-0dcd74074a8f.png)

규칙 위반 예시 : from의 원반순서를 어긋나면 안된다. 아래 그림처럼 초록색원반이 파란색 원반 위에 있어야지 파란색 원반 아래에 있으면 안된다. 

![2](https://user-images.githubusercontent.com/41605276/56191153-ca641c80-6066-11e9-80ab-754908e4df77.png)

원반이 하나일때 예시 : 바로 from에서 to로 옮기면 된다.

![3](https://user-images.githubusercontent.com/41605276/56191169-d0f29400-6066-11e9-9f73-e942c78d2248.png)

원반이 두개일때 예시 아래 그림을 보면 쉽게 이해할 수 있다.

![4](https://user-images.githubusercontent.com/41605276/56191172-d5b74800-6066-11e9-80f9-6a1f252644fe.png)

![5](https://user-images.githubusercontent.com/41605276/56191177-db149280-6066-11e9-8f7e-ddc8ae9e5419.png)

![6](https://user-images.githubusercontent.com/41605276/56191187-e071dd00-6066-11e9-9985-0760839d8a08.png)

만약에 원반이 3개일때 구현과정

step1) 'top_tray'를 from에서 to로 옮긴다.

step2) 'middle_tray'를 from에서 by로 옮긴다.

step3) 'top_tray'를 to에서 by로 옮긴다.

step4) 'bottom_tray'를 from에서 to로 옮긴다.

step5) 'top_tray'를 by에서 from으로 옮긴다.

step6) 'middle_tray'를 by에서 to로 옮긴다.

step7) 'top_tray'를 from에서 to로 옮긴다.

원반이 1개 일때가 base case이다.

하노이타워 파이썬 코드 구현

```python
def hanoi(num_tray, _from, _by, _to):
    if num_tray==1:
        print('{}번째 원반을 {}에서 {}로 이동'.format(num_tray, _from, _to))
        return
        
    hanoi(num_tray-1, _from, _to, _by)
    print('{}번째 원반을 {}에서 {}로 이동'.format(num_tray, _from, _to))
    
    hanoi(num_tray-1, _by, _from, _to)
    
while 1:
    num_tray=int(input("원반의 개수를 입력하세요(종료 : 0) :"))
    if num_tray==0:
        break
    hanoi(num_tray, 'From', 'By', 'To')
```

```text
원반의 개수를 입력하세요(종료 : 0) :3
1번째 원반을 From에서 To로 이동
2번째 원반을 From에서 By로 이동
1번째 원반을 To에서 By로 이동
3번째 원반을 From에서 To로 이동
1번째 원반을 By에서 From로 이동
2번째 원반을 By에서 To로 이동
1번째 원반을 From에서 To로 이동
원반의 개수를 입력하세요(종료 : 0) :0
```