---
layout: post
title: "Python TIL (20180730)"
tags: [Python]
comments: true
---

.

#### # 컴퓨터가 다룰 수 있는 자료의 타입

1) 수 
- 정수(양수, 음수)
- 실수(실수에서 중요한 포인트는 정밀도와 표현범위이다.)

2) 문자
- 컴퓨터가 0,1 밖에 모르는데 문자를 어떻게 표현할 것인가
- 문자를 수로 변환한다. 아스키 코드나 유니코드 또는 UTF-8로..

#### # 파이썬에서 '=='  명령어는 값을 비교할때 쓰는 연산이다.

- 참고로 a is b는 a와 b가 같은 메모리를 가리키고 있는가를 말한다.
- a is b 와 a == b는 결론적으로 다른 것을 비교하는 명령어다.

#### # 파이썬에서 변수할당 방법

- 파이썬에서는 a=10이라고 하면 a라고 하는 이름이라는 메모리 공간을 만들고, 10이 저장되어 있는 별도에 메모리 공간을 만든다음 a = 10이라고 정의하는 순간 a라는 공간이 10이 저장되어 있는 공간을 가리키게 된다.

#### # mutable과 immutable

- 파이썬에서 변수든 객체든 간에
- immutable = 변경 불가한 객체 = 수, 튜플, 문자열 등
- mutable = 변경 가능한 객체 = 리스트, 딕셔너리 등
- 참고로 튜플이 리스트보다 메모리를 훨씬 더 적게 잡기 때문에 튜플이 훨씬 빠르다.

#### # Lambda

- 정의 : 익명함수로 말 그대로 이름이 없는 함수이다. 한번 쓰고 다시 사용하지 않을때, 다시말해 함수인데 한번만 쓰고 안쓸때 사용하는 함수이다.

- 특징 :

1) 익명함수

2) 자주 사용하지 않을때 편리

3) return 문이 없다.

4) body는 오직 expression(식)만 올 수 있다.

- 4) 파이썬 코드 예시


```python
# lambda 함수
f = lambda a,b : a+b

# 일반함수
def add(a,b):
    return a+b
```


```python
a = 10
b = 20
add(a, b)
```




    30




```python
f(a,b)
```




    30



5) 람다함수는 주변에 있는 변수를 가져다 쓸 수 있다.

- 5) 파이썬 코드 예시


```python
li = [1, 2, 3, 4]
f = lambda idx: li[idx] ** 2

for i in range(4): 
    print(f(i))
```

    1
    4
    9
    16
    

#### # 식(expression) vs 문(statement)

- 식 : a+b, a , 같은 것들
- 문 : a = a+b 식에서 값을 반환해서 할당할 경우
- 파이썬에서는 함수를 식으로 인식을 하고 함수내에서 리턴이 없다고 하더라도 none을 반환하게 된다. 그래서 함수를 쓸 경우에는 별도로 리턴을 하지 않아도 none을 리턴하게 된다. 하지만 람다는 얘기가 다르다.

#### # 람다에서는 식만 가능하고 문은 사용할 수 없다.

- 예를들어 lambda = a, b : a+b는 가능한데 lambda = a, b : c= a+b는 사용할 수 없다.
- 예를들어 아래와 같이

f = lambda idx, data: li[idx] = data

결과값을 출력하면 다음과 같은 애러가 발생한다. 

SyntaxError: can't assign to lambda

#### # 람다함수 사용예제

- 람다함수를 사용하지 않을때 (일반함수를 이용할때)


```python
li = [i for i in range(1, 11)]

# lambda 사용하지 않을 때(함수를 이용할때)

def pred(a):
    return a % 2 == 0

li.sort(key = pred)
##sort는 리스트를 낮음과 높음으로 정렬한다.

li
```




    [1, 3, 5, 7, 9, 2, 4, 6, 8, 10]



- 람다함수를 사용할때

-> 여러줄 코딩할 필요가 없고 한번만 사용할 함수를 대체할 수 있다.


```python
li.sort(key = lambda a: a%2==0)
li
```




    [1, 3, 5, 7, 9, 2, 4, 6, 8, 10]



#### # lazy evaluation

- 왜 쓰냐 ? lazy evaluation이라는 것 때문에 쓴다.
- lazy evaluation 이란 필요할 때만 연산을 수행하고 이에 따라 자원을 절약하고 성능을 개선하고자 할 때를 말한다.
- 대표적인 메서드로 map, filter, reduce가 있다.

#### # map

- Y = f(X)로 정의역에서 함수에 변수들을 넣으면서 결과값이 나오면 그대로 매핑해서 결과값을 출력한다고 해서 map 이다.

- 아래는 map함수 예시


```python
#일반 함수 사용시
li = [1, 2, 3, 4]

def square(x):
    return x ** 2

m = map(square, li)

for e in m:
    print(e)
```

    1
    4
    9
    16
    


```python
#lambda 사용시
li = [1, 2, 3, 4]

m = map(lambda x: x**2, li)

for e in m:
    print(e)
```

    1
    4
    9
    16
    

#### # filter

- 조건에 True인 것만 골라낸다.
- 아래는 filter 사용예시


```python
li = [1, -2, 4, -6, 0, 8]

#filter 객체로 iterator이다
f = filter(lambda x: x > 0, li)	
```


```python
next(f) #next는 객체의 메서드를 호출해준다.
```




    1




```python
next(f) 
```




    4




```python
next(f) 
```




    8




```python
next(f) 
## 결과값을 모두 뽑아냈는데 또 next를 하게 되면 아래처럼 애러가 발생한다.
```


    ---------------------------------------------------------------------------

    StopIteration                             Traceback (most recent call last)

    <ipython-input-15-b7f2e1fb42f4> in <module>
    ----> 1 next(f)
    

    StopIteration: 


#### # 맵과 필터의 사용예시


```python
li=[2,6,-7,-3,3,-5]
## 원하는 함수출력 기대값은 4, 36, 9

m = map(lambda x : x**2, filter(lambda a : a>0,li))

for val in m:
    print(val)
```

    4
    36
    9
    

#### # reduce

- 핵심 기능 : sequence 자료형에서 두개씩 가져온 후 연산에 한개의 값으로 치환한다.

- sequence 자료형을 하나의 값으로 할때

- 함수는 두개의 인자를 받는다.	

- from fuctools import reduce를 입력하여 호출을 먼저 해줘야한다.

- For example 


```python
from functools import reduce

reduce(lambda x, y: x+y, [1, 2, 3, 4, 5])
# 다음과 같이 계산한다. ((((1+2)+3)+4)+5)
```




    15




```python
reduce(lambda x, y: x+y, [1, 2, 3, 4, 5],100)
# 위에 ,100은 초기값으로 100을 부여하겠다는 의미이다.
```




    115




```python
li = [9, -3, -7, 2, 4, 3, 3, -8, -6, -7]

reduce(lambda a,b : a if a>b else b ,li)
```




    9



#### # 파이썬이 거짓으로 인정하는 객체

1) {}

2) ()

3) ''

4) []

5) None

- 아래는 코드구현 예시


```python
[]  or [1,2]
# False or True
```




    [1, 2]




```python
[1,2] and []
# True and False
```




    []




```python
[3,4] or [1,2]
# True or True
```




    [3, 4]




```python
[3,4] and [1,2]
# True and True
```




    [1, 2]



#### # map, filter, reduce 응용


```python
li = ['a','b','a','c','b','a']

dic = reduce(lambda result, ch: result.update({ch : result.get(ch, 0)+1}) or result, li, {})
dic
```




    {'a': 3, 'b': 2, 'c': 1}



1) 람다가 실행되고 result는 초기값 {}(비어있는 딕셔너리)를 가져온다.

2) 그리고 result.update 함수를 실행하게 되는데 result에 아무것도 없기 때문에 (li에서 가져온 값이 없기 때문에 처음에 있는 'a'를 가져와서 {'a' : 0}이 들어가지만 뒤에 +1 이 있으므로 결론적으로 {'a' : 1}이 셋팅되게 된다. 그리고 result에  {'a' : 1}이 들어간다. 

3) 람다에서 리턴 반환시 update기능은 none이 반환되므로 뒤에 or dic을 넣어주면 {} or {'a' : 1}이 된다.

4) 그리고 {'a' : 1} 가 다음연산을 위한 result에 셋팅이 된다. 

5)  2) ~ 4) 과정이 반복되면서 {'a' :3 , 'b' : 2, 'c' :1}가 완성된다.

#### # first class function

- 정의 : 퍼스트클래스 함수란 프로그래밍 언어가 함수 (function) 를 first-class citizen으로 취급하는 것을 뜻합니다. 쉽게 설명하자면 함수 자체를 인자 (argument) 로써 다른 함수에 전달하거나 다른 함수의 결과값으로 리턴 할수도 있고, 함수를 변수에 할당하거나 데이터 구조안에 저장할 수 있는 함수를 뜻합니다.


- 사용하는 언어가 first class function를 할 수 있는 위한 세가지 조건이 있는데 파이썬은 아래 세가지 조건을 만족하므로 first class function을 지원한다


- 그 세가지 조건은 아래와 같다.


1) 함수를 인자(argument)로 전달할 수 있는가


```python
def f(a,b):
    return a+b

def g(func, a, b):
    return func(a,b)

g(f,1,2)
```




    3



2) 함수를 변수에 할당 할 수 있는가


```python
def adder(a, b):
    return a + b

#함수를 변수에 할당해 사용
func_var = adder
```

3) 함수를 리턴할 수 있는가


```python
def calculator(kind = 'add'):
    def add(a, b):
         return a + b
        
    def subtract(a, b):
        return a - b
    
    if kind == 'add':
        return add
    else:
        return subtract
```
