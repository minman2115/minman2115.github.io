---
layout: post
title: "기타 파이썬 프로그래밍 기초개념"
tags: [Python]
comments: true
---

.

#### # 학습시 참고 URL : https://github.com/ythwork

1) 오버로딩

오버로딩은 사실 파이썬에서 지원하지 않는다.

상속에서 나오는 개념이다.

같은 레벨에서 같은 이름의 함수가 두가지 있을때

가장 최근에 정의했던 함수를 실행하게 된다.

기존에 있던 함수를 그냥 덮어버린다.

일부 프로그래밍 언어에서는 이름이 같지만 다른 함수로 인정하는 경우가 있다.

예를 들어서 인자가 다를때, 데이터 타입이 다를때 이럴때 인정을 해주는데 이런 개념을 오버로딩이라고 한다.

2) 오버라이딩

오버라이딩도 파이썬에서 지원하지 않는다.

기본클래스가 있고 상속받은 파생클래스가 있다고 할때

부모클래스 내에 있는 이미 존재하는 함수를 자식클래스에서 다시 정의하는 것을 말하고 이것을 매서드 오버라이딩이라고 한다.

3) 다형성의 특수한 예시


```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y
```


```python
p1 = Point(2,3)
```

- 아래의 코드가 수행했으면 하는게


- p1에 각 x와 y에 1을 더해서 p2로 반환하고 싶다. 그러나 결과는 애러가 난다.


```python
p2 = p1 + 1
```


    ---------------------------------------------------------------------------

    TypeError                                 Traceback (most recent call last)

    <ipython-input-3-35ae83714e86> in <module>
    ----> 1 p2 = p1 + 1
    

    TypeError: unsupported operand type(s) for +: 'Point' and 'int'


- 연산자는 같은 타입의 데이터를 연산해주는 것인데 어쨌든 위에 의도한거 처럼 하고 싶다. 이럴때는 아래와 같이 구현해주면 된다.


```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y
        
    def __add__(self, num):
        x = self.x + num
        y = self.y + num
        return Point(x,y)
    ## 그냥 x와 y로 받아주는 이유는 기존에 값을 변경하지 않기 위해서이다.
    ## 만약에 self.x 와 self.y로 받아주게 되면 기존에 있던 값들을 덮어씌우면서
    ## 기존에 값이 바뀌게 된다.
    
    def __str__(self):
        return '({}, {})'.format(self.x, self.y)
    
p1 = Point(2,3)

p2 = p1 + 1
print(p2)
```

    (3, 4)
    

- p2 = 1 + p1 를 실행하면 이때는 오류가 난다. 순서가 바뀌었기 때문에 그러는데 이것도 받아주기 위해 아래 구현한 코드와 같이 radd를 추가하면 하면 된다.


```python
p2 = 1 + p1
```


    ---------------------------------------------------------------------------

    TypeError                                 Traceback (most recent call last)

    <ipython-input-12-243447a8ecec> in <module>
    ----> 1 p2 = 1 + p1
    

    TypeError: unsupported operand type(s) for +: 'int' and 'Point'



```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y
        
    def __add__(self, num):
        x = self.x + num
        y = self.y + num
        return Point(x,y)

    def __radd__(self, num):
        x = self.x + num
        y = self.y + num
        return Point(x,y)
    
    def __str__(self):
        return '({}, {})'.format(self.x, self.y)

## __str__은 print(어떤값) 이라고 하면 어떤값을 
## 스트링으로 변환해서 출력하게 하는 함수이다.
```


```python
p1 = Point(2,3)

p2 = 1 + p1
print(p2)
```

    (3, 4)
    

4) expression(식) vs statement(문)

- expression(식)

반드시 어떤값이 있어야 한다.

ex) 3 + 5 ==> 식이다. 다항식

ex) 3 ==> 식이다. 단항식

ex) 'kim' ==> 식이다. 단항식

왜냐하면 값이 있기 때문에


- statement(문)

ex) a = 1 + 5


파이썬에서 모든함수는 식이다.

따라서 모든함수는 값을 반환해야 한다.

그런데 우리가 리턴을 명시를 안해줬다고 치자.

이럴 경우에는 파이썬에서는 그 함수를 None 값으로 반환시킨다.

- *args 예시


```python
li = [1,2,3,4]

def func(*args):
    for elem in args:
        print(elem)
        
func(li)
```

    [1, 2, 3, 4]
    


```python
func(*li)
```

    1
    2
    3
    4
    
