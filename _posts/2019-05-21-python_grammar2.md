---
layout: post
title: "iterator와 generator 기초개념"
tags: [Python]
comments: true
---

.

#### 그림, 실습코드 등 학습자료 출처 : https://gitlab.com/radajin

iterator&generator를 위해서는 먼저 iterable을 알아야 한다.

#### 1. iterable 개요

iterable은 순서가 있는 집합을 의미한다.


```python
x = [1,2,3]
it = iter(x)

## x에 있는 데이터를 하나씩 꺼내는 것이 목적인게 iter이다.
```


```python
print(next(it))
```

    1
    


```python
print(next(it))
```

    2
    


```python
print(next(it))
```

    3
    


```python
print(x)
```

    [1, 2, 3]
    

- iterator란 next 함수를 호출하면 다음 값을 생성하는 생성기이다.


- next를 호출하면 객체상태가 변경된다.


#### 2. iterator를 이용한 피보나치 수열 구현


```python
# 피보나치 수열을 구현한 이터레이터
# next를 호출하면 객체의 상태를 변경한다.
# 호출에 대한 결과값을 return 한다.

class fib:
    def __init__(self):
        self.prev = 0
        self.current = 1
        
    def __iter__(self):
        return self ##오브젝트 자체를 리턴한다.
    
    def __next__(self):
        value = self.current
        self.current += self.prev
        self.prev = value
        return value
    
f = fib()

for count in range(10):
    print(next(f), end = ' ')
```

    1 1 2 3 5 8 13 21 34 55 

#### 3. generator 개요

- 이터레이터를 간편하게 만든것이 제네레이터이다.


- 특별한 종류의 이터레이터라고 생각하면 된다.


- 이터레이터를 간결하게 만든것이다.


- iter()와 next() 메서드로 클래스를 작성하는 수고를 없애줬다.


- 사용하는 이유 : 프로그램의 성능향상 때문이다. 왜냐하면 메모리를 소비하지 않기 때문이다. 또한 속도도 빨라진다. lazy evaluation이 가능하기 때문이다.	


- 특정한 종류의 함수이다.


- next로 실행한다.


- yield를 순차적으로 실행한다. 예를 들어 내가 yield 1을 반환했다면 그다음에는 yield 2를 반환한다. yield 1을 반환하는게 아니라..

#### 4. generator를 이용한 피보나치수열 구현


```python
def fib():
    prev = 0
    current = 1
    ## 아래처럼해도 가능
    ## prev, current = 0,1
    while True:
        yield current
        ## yield는 yield 명령을 만나면 값을 바깥으로 전달한다.
        ## 그리고 현재 실행하던 함수를 잠시 중단하고 바깥 코드를 실행한 후에 다음 next를 만나면 다시 돌아와서 나머지 코드를 실행한다.
        prev, current = current, prev + current
        
f = fib()

for count in range(10):
    print(next(f), end = ' ')
```

    1 1 2 3 5 8 13 21 34 55 

yield 테스트


```python
def test():
    yield 1
    yield 2
    yield 3
    yield 4
    yield 5
    
t = test()

for count in range(5):
    print(next(t))

## 실행코드에 넥스트에 넣어줬을때 순서대로 리턴해줌
```

    1
    2
    3
    4
    5
    

- yield, yield from 

1) yield를 만나면 값을 바깥으로 전달

2) 현재 함수를 잠시 중단하고 바깥 코드를 실행 시킴


```python
def test():
    ls = [1, 2, 3]
    yield from ls
    
t = test()

for _ in range(3):
    print(next(t))
```

    1
    2
    3
    

#### 5. list Comprehention

- 컴프리헨션도 제너레이터의 한 종류이다.


```python
# list comprehention
numbers = [1, 2, 3, 4, 5, 6]
[x * x for x in numbers]
```




    [1, 4, 9, 16, 25, 36]




```python
# set comprehention
numbers = [1, 2, 3, 4, 5, 6]
{x * x for x in numbers}
```




    {1, 4, 9, 16, 25, 36}




```python
# dictionary comprehention
numbers = [1, 2, 3, 4, 5, 6]
{x: x * x for x in numbers}
```




    {1: 1, 2: 4, 3: 9, 4: 16, 5: 25, 6: 36}



- 제너레이터 표현식


```python
numbers = [1, 2, 3, 4, 5, 6]
gen_numbers = (x * x for x in numbers)
print(gen_numbers)
```

    <generator object <genexpr> at 0x10d272468>
    


```python
next(gen_numbers)
```




    1




```python
list(gen_numbers)
```




    [4, 9, 16, 25, 36]


