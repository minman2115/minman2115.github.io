---
layout: post
title: "args 문법 기초개념"
tags: [Python]
comments: true
---

.

#### 그림, 실습코드 등 학습자료 출처 : https://gitlab.com/radajin

#### 1. arg앞에 ' * ' 의 유무에 따른 차이 이해


```python
def test(*args):
    print(args)
    
data = [1, 2, 3] 

test(data) 
# ([1, 2, 3]) -> ([1, 2, 3],)
test(*data) 
# (1, 2, 3) -> (1, 2, 3)
```

    ([1, 2, 3],)
    (1, 2, 3)
    

#### 2. map function을 이용한 arg 문법활용 예제 1


```python
ls1 = [1,2,3,4]
ls2 = [5,6,7]
ls3 = [9,10,11,12]

def map_func(func, *args):
    return [func(*datas) for datas in zip(args)]

result = map_func(lambda args : sum(args), ls1, ls2, ls3)

result
```




    [10, 18, 42]



#### 2. map function을 이용한 arg 문법활용 예제 2


```python
ls1 = [1,2,3,4]
ls2 = [5,6,7]
ls3 = [9,10,11,12]

def map_func(func, *args):
    
    results = []
    print("zip(args) : ", list(zip(args)))
    print("zip(*args) : ", list(zip(*args)))
    
    for datas in zip(args):
        
        # ([1, 2, 3, 4],) - ([5, 6, 7],) - ([9, 10, 11, 12],)
        print("datas:", datas, end=" ")            
        
        # [1, 2, 3, 4] - [5, 6, 7] - [9, 10, 11, 12]
        print("*datas:", *datas, end=" ")           
        
        # 아래와 같이 3번 func 함수를 호출한다.
        # func([1, 2, 3, 4]) - func([5, 6, 7]) - func([9, 10, 11, 12]) 
        # lambda args : sum(args) 함수에서 
        # lambda 함수의 args 는 [1, 2, 3, 4] - [5, 6, 7] - [9, 10, 11, 12] 를 받아서 sum 함수를 실행한다.
        result = func(*datas)  
        print("result:", result, end=" ")           
        
        # 결과 데이터 저장
        results.append(result)
        print()
        
    return results

#     return [func(*datas) for datas in zip(args)]

result = map_func(lambda args : sum(args), ls1, ls2, ls3)

result
```

    zip(args) :  [([1, 2, 3, 4],), ([5, 6, 7],), ([9, 10, 11, 12],)]
    zip(*args) :  [(1, 5, 9), (2, 6, 10), (3, 7, 11)]
    datas: ([1, 2, 3, 4],) *datas: [1, 2, 3, 4] result: 10 
    datas: ([5, 6, 7],) *datas: [5, 6, 7] result: 18 
    datas: ([9, 10, 11, 12],) *datas: [9, 10, 11, 12] result: 42 
    




    [10, 18, 42]



#### 3. map function을 이용한 arg 문법활용 예제 3


```python
ls1 = [1,2,3,4]
ls2 = [5,6,7]
ls3 = [9,10,11,12]

def map_func(func, *args):
    print("zip(*args) : ", list(zip(*args)))
    return [func(*datas) for datas in zip(*args)]

result = map_func(lambda *args : sum(args), ls1, ls2, ls3)

result
```

    zip(*args) :  [(1, 5, 9), (2, 6, 10), (3, 7, 11)]
    




    [15, 18, 21]


