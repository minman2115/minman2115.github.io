---
layout: post
title: "memorization"
tags: [Python]
comments: true
---

예시를 통한 memorization 개념학습

## memorization

- 개념요약 : 반복적으로 사용되는 계산결과를 임시 저장공간에 캐싱하여 활용함으로써 반복적인 연산을 줄이는 기법


- 아래 피보나치 함수와 같이 두단계로 나누어지는 피보나치 수열 예시로 구현


```python
def fib(n):
    if n < 2:
        return n
    
    return fib(n-1) + fib(n-2)
```

- 피보나치 수열의 연산시간은 크지 않은 수가 입력됨에도 불구하고 상당히 오래걸린다.


```python
%%time
fib(40)
```

    Wall time: 1min 38s
    




    102334155



- 아래 구현한 'make_memo' 함수의 핵심 : 

-> 불필요한 연산감소

-> 중복된 계산을 'me' 딕셔너리에 임시 캐싱하여 차후 반복적인 계산 시 캐싱된 데이터를 대신 활용하겠다


```python
def make_memo(func):
    me = {}
    
    def memo(n): # memo(400) # me, func=fib
        print("실제 요청 받은 숫자 n:", n)
        
        if me.get(n):
            print("캐쉬된 값 :", n)
            return me[n]
        
        else:
            print("캐쉬되지 않아서 계산해야하는 값 :", n)
            result = func(n)
            me[n] = result
            return result
        
    return memo
```

- 'make_memo' 데코레이터를 씌우는 순간 'make_memo' 함수 안에 'memo'함수가 작동되는 원리이다.


```python
@make_memo
def fib(n): 
    ## memo에 인자를 400넣는거랑 동일하다고 할 수 있다. 
    # me, func = fib
    if n < 2:
        return n
    
    return fib(n-1) + fib(n-2)
```

- memorization 기법 적용 시 상당한 시간단축을 할 수 있다.


```python
%%time
fib(5)
```

    실제 요청 받은 숫자 n: 5
    캐쉬되지 않아서 계산해야하는 값 : 5
    실제 요청 받은 숫자 n: 4
    캐쉬되지 않아서 계산해야하는 값 : 4
    실제 요청 받은 숫자 n: 3
    캐쉬되지 않아서 계산해야하는 값 : 3
    실제 요청 받은 숫자 n: 2
    캐쉬되지 않아서 계산해야하는 값 : 2
    실제 요청 받은 숫자 n: 1
    캐쉬되지 않아서 계산해야하는 값 : 1
    실제 요청 받은 숫자 n: 0
    캐쉬되지 않아서 계산해야하는 값 : 0
    실제 요청 받은 숫자 n: 1
    캐쉬된 값 : 1
    실제 요청 받은 숫자 n: 2
    캐쉬된 값 : 2
    실제 요청 받은 숫자 n: 3
    캐쉬된 값 : 3
    Wall time: 4.02 ms
    




    5




```python
%%time
fib(5)
```

- 파이썬 라이브러리에도 memorization을 구현할 수 있는 내부함수가 있다.


```python
from functools import lru_cache
```


```python
@lru_cache(maxsize = 512)
def fib(n):
    if n < 2:
        return n
    
    return fib(n-1) + fib(n-2)
```


```python
%%time
fib(400)
```

    Wall time: 4.01 ms
    




    176023680645013966468226945392411250770384383304492191886725992896575345044216019675


