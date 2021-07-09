---
layout: post
title: "Python class 기초개념"
tags: [Python]
comments: true
---

.

[학습자료]

"점프 투 파이썬" 클래스 자료를 읽고 공부한 내용입니다. 

URL : https://wikidocs.net/28

[학습내용]


- 클래스는 왜 쓰는걸까

계산기를 예를 들어보자 계산기에 숫자 3을 입력하고 + 기호를 입력한 후 4를 입력하면 결괏값으로 7을 보여 준다. 다시 한 번 + 기호를 입력한 후 3을 입력하면 기존 결괏값 7에 3을 더해 10을 보여 준다. 다시말해서 계산기는 이전에 계산한 결괏값을 항상 메모리 어딘가에 저장하고 있어야 한다.

예를 들어서 계산기의 "더하기" 기능을 구현한 파이썬 코드는 다음과 같다.

이전에 계산한 결과값을 유지하기 위해서 result라는 전역변수를 사용했다.


```python
result = 0

def add(num):
    global result
    result += num
    return result

print(add(3))
print(add(4))
```

    3
    7
    

그런데 만일 한 프로그램에서 2대의 계산기가 필요한 상황이 발생하면 어떻게 해야 할까? 각 계산기는 각각의 결괏값을 유지해야 하기 때문에 위와 같이 add 함수 하나만으로는 결과값을 유지할 수 없다. 그래서 다음과 같이 함수를 각각 따로 만들어야 한다.

코드가 매우 지저분해진다.


```python
result1 = 0
result2 = 0

def add1(num):
    global result1
    result1 += num
    return result1

def add2(num):
    global result2
    result2 += num
    return result2

print(add1(3))
print(add1(4))
print(add2(3))
print(add2(7))
```

    3
    7
    3
    10
    

위와 같은 경우에 클래스를 사용하면 다음과 같이 간단하게 해결할 수 있다.


```python
class Calculator:
    def __init__(self):
        self.result = 0

    def add(self, num):
        self.result += num
        return self.result

# cal_01, cal_02를 파이썬에서는 객체라고 부른다.
cal_01 = Calculator()
cal_02 = Calculator()

print(cal_01.add(3))
print(cal_01.add(4))
print(cal_02.add(3))
print(cal_02.add(7))
```

    3
    7
    3
    10
    

- 객체와 인스턴스의 차이

클래스로 만든 객체를 인스턴스라고도 한다. 그렇다면 객체와 인스턴스의 차이는 무엇일까. 

예를 들어서 a = Cookie() 이렇게 만든 a는 객체이다. 그리고 a 객체는 Cookie의 인스턴스이다. 즉 인스턴스라는 말은 특정 객체(a)가 어떤 클래스(Cookie)의 객체인지를 관계 위주로 설명할 때 사용한다. "a는 인스턴스"보다는 "a는 객체"라는 표현이 어울리며 "a는 Cookie의 객체"보다는 "a는 Cookie의 인스턴스"라는 표현이 자연스럽다.

- 사칙연산 클래스 만들기

step 1) 객체에 숫자 지정할 수 있게 만들기

더하기, 나누기, 곱하기, 빼기등의 기능을 하는 객체를 만들어야 한다. 그런데 이러한 기능을 갖춘 객체를 만들려면 우선 test_cal 객체에 사칙연산을 할 때 사용할 2개의 숫자를 먼저 알려주어야 한다. 다음과 같이 연산을 수행할 대상(4, 2)을 객체에 지정할 수 있게 만들어 보자.


```python
class FourCal:
    def setdata(self, first, second):
        self.first = first
        self.second = second
        
test_cal = FourCal()
test_cal.setdata(4, 2)
print(test_cal.first)
print(test_cal.second)
```

    4
    2
    

객체를 통해 클래스의 메서드를 호출하려면 `test_cal.setdata(4, 2)`와 같이 도트(.) 연산자를 사용해야 한다. setdata 메서드에는 self, first, second 총 3개의 매개변수가 필요한데 실제로는 `test_cal.setdata(4, 2)`처럼 2개 값만 전달했다. 그 이유는 `test_cal.setdata(4, 2)`처럼 호출하면 setdata 메서드의 첫 번째 매개변수 self에는 setdata메서드를 호출한 객체 `test_cal`가 자동으로 전달되기 때문이다.

파이썬 메서드의 첫 번째 매개변수 이름은 관례적으로 self를 사용한다. 객체를 호출할 때 호출한 객체 자신이 전달되기 때문에 self를 사용한 것이다. 물론 self말고 다른 이름을 사용해도 상관없다.

step 2) 더하기 기능 만들기


```python
class FourCal:
    def setdata(self, first, second):
        self.first = first
        self.second = second
        
    def add(self):
        result = self.first + self.second
        return result
    
test_cal = FourCal()
test_cal.setdata(4, 2)
print(test_cal.add())
```

    6
    

step 3) 곱하기, 빼기, 나누기 기능 만들기


```python
class FourCal:
    def setdata(self, first, second):
        self.first = first
        self.second = second
    
    def add(self):
        result = self.first + self.second
        return result
    
    def mul(self):
        result = self.first * self.second
        return result

    def sub(self):
        result = self.first - self.second
        return result
    
    def div(self):
        result = self.first / self.second
        return result
    
test_cal = FourCal()
test_cal.setdata(4, 2)
print(test_cal.sub())
```

    2
    

step 4) 생성자 (Constructor)를 이용해서 초기 파라미터값을 받도록 해보자

이번에는 우리가 만든 FourCal 클래스를 다음과 같이 사용해 보자.


```python
test_cal = FourCal()
test_cal.add()
```


    ---------------------------------------------------------------------------

    AttributeError                            Traceback (most recent call last)

    <ipython-input-9-b60843a5976c> in <module>
          1 test_cal = FourCal()
    ----> 2 test_cal.add()
    

    <ipython-input-8-d7431545e28a> in add(self)
          5 
          6     def add(self):
    ----> 7         result = self.first + self.second
          8         return result
          9 
    

    AttributeError: 'FourCal' object has no attribute 'first'


FourCal 클래스의 인스턴스 a에 setdata 메서드를 수행하지 않고 add 메서드를 수행하면 "AttributeError: 'FourCal' object has no attribute 'first'" 에러가 발생한다. setdata 메서드를 수행해야 객체 test_cal의 객체변수 first와 second가 생성되기 때문이다.

이렇게 객체에 초깃값을 설정해야 할 필요가 있을 때는 setdata와 같은 메서드를 호출하여 초깃값을 설정하기보다는 생성자를 구현하는 것이 안전한 방법이다. 생성자(Constructor)란 객체가 생성될 때 자동으로 호출되는 메서드를 의미한다.

파이썬 메서드 이름으로 `__init__`를 사용하면 이 메서드는 생성자가 된다. 다음과 같이 FourCal 클래스에 생성자를 추가해 보자.

`__init__` 메서드의 init 앞뒤로 붙은 `__는 언더스코어(_)` 두 개를 붙여 쓴 것이다.


```python
class FourCal:
    def __init__(self, first, second):
        self.first = first
        self.second = second
        
    # __init__ 메서드는 setdata 메서드와 이름만 다르고 모든 게 동일하다. 
    # 단 메서드 이름을 __init__으로 했기 때문에 생성자로 인식되어 객체가 생성되는 시점에 자동으로 호출되는 차이가 있다.
    
    def add(self):
        result = self.first + self.second
        return result
    
    def mul(self):
        result = self.first * self.second
        return result

    def sub(self):
        result = self.first - self.second
        return result
    
    def div(self):
        result = self.first / self.second
        return result
```

이제 다음처럼 예제를 수행해 보자.


```python
test_cal = FourCal()
```


    ---------------------------------------------------------------------------

    TypeError                                 Traceback (most recent call last)

    <ipython-input-12-3b6e66e91537> in <module>
    ----> 1 test_cal = FourCal()
    

    TypeError: __init__() missing 2 required positional arguments: 'first' and 'second'


`test_cal = FourCal()`을 수행할 때 생성자 `__init__`이 호출되어 위와 같은 오류가 발생했다. 오류가 발생한 이유는 생성자의 매개변수 first와 second에 해당하는 값이 전달되지 않았기 때문이다.

위 에러를 해결하려면 다음처럼 first와 second에 해당되는 값을 전달하여 객체를 생성해야 한다.

위와 같이 수행하면 `__init__` 메서드의 매개변수에는 각각 오른쪽과 같은 값이 대입된다.

`__init__` 메서드도 다른 메서드와 마찬가지로 첫 번째 매개변수 self에 생성되는 객체가 자동으로 전달된다는 점을 유념해야한다

따라서 `__init__` 메서드가 호출되면 setdata 메서드를 호출했을 때와 마찬가지로 first와 second라는 객체변수가 생성될 것이다.


```python
test_cal = FourCal(4, 2)
# self : 생성되는 객체
# first : 4
# second : 2
```

다음과 같이 객체변수의 값을 확인해 보자.


```python
test_cal = FourCal(4, 2)
print(test_cal.first)
print(test_cal.second)
```

    4
    2
    

- 클래스의 상속

어떤 클래스를 만들 때 다른 클래스의 기능을 물려받을 수 있게 만들 수 있다. 상속 개념을 사용하여 우리가 만든 FourCal 클래스에 a의 b제곱을 구할 수 있는 기능을 추가해 보자.

왜 상속을 해야 할까?

보통 상속은 기존 클래스를 변경하지 않고 기능을 추가하거나 기존 기능을 변경하려고 할 때 사용한다.

"클래스에 기능을 추가하고 싶으면 기존 클래스를 수정하면 되는데 왜 굳이 상속을 받아서 처리해야 하지?" 라는 의문이 들 수도 있다. 하지만 기존 클래스가 라이브러리 형태로 제공되거나 수정이 허용되지 않는 상황이라면 상속을 사용해야 한다.

상속은 MoreFourCal 클래스처럼 기존 클래스(FourCal)는 그대로 놔둔 채 클래스의 기능을 확장시킬 때 주로 사용한다.


```python
class MoreFourCal(FourCal):
    def pow(self):
        result = self.first ** self.second
        return result
    
test_cal = MoreFourCal(4, 2)
test_cal.pow()
```




    16



- 메서드 오버라이딩

이번에는 FourCal 클래스를 다음과 같이 실행해 보자.


```python
test_cal = FourCal(4, 0)
test_cal.div()
```


    ---------------------------------------------------------------------------

    ZeroDivisionError                         Traceback (most recent call last)

    <ipython-input-18-89ec85952f5e> in <module>
          1 test_cal = FourCal(4, 0)
    ----> 2 test_cal.div()
    

    <ipython-input-11-161d856d33f9> in div(self)
         20 
         21     def div(self):
    ---> 22         result = self.first / self.second
         23         return result
    

    ZeroDivisionError: division by zero


FourCal 클래스의 객체 a에 4와 0 값을 설정하고 div 메서드를 호출하면 4를 0으로 나누려고 하기 때문에 위와 같은 ZeroDivisionError 오류가 발생한다. 하지만 0으로 나눌 때 오류가 아닌 0을 돌려주도록 만들고 싶다면 어떻게 해야 할까?

다음과 같이 FourCal 클래스를 상속하는 SafeFourCal 클래스를 만들어 보자.


```python
class SafeFourCal(FourCal):
    def div(self):
        if self.second == 0:  # 나누는 값이 0인 경우 0을 리턴하도록 수정
            return 0
        else:
            return self.first / self.second
        
test_cal = SafeFourCal(4, 0)
test_cal.div()
```




    0



SafeFourCal 클래스는 FourCal 클래스에 있는 div 메서드를 동일한 이름으로 다시 작성하였다. 이렇게 부모 클래스(상속한 클래스)에 있는 메서드를 동일한 이름으로 다시 만드는 것을 메서드 오버라이딩이라고 한다. 이렇게 메서드를 오버라이딩하면 부모클래스의 메서드 대신 오버라이딩한 메서드가 호출된다.

- 클래스 변수

객체변수는 다른 객체들에 영향받지 않고 독립적으로 그 값을 유지한다는 점을 이미 알아보았다. 이번에는 객체변수와는 성격이 다른 클래스 변수에 대해 알아보자.

다음 클래스를 작성해 보자.


```python
class Family:
    lastname = "kim"
    
print(Family.lastname)
```

    kim
    

Family 클래스에 선언한 lastname이 바로 클래스 변수이다. 클래스 변수는 클래스 안에 함수를 선언하는 것과 마찬가지로 클래스 안에 변수를 선언하여 생성한다.

클래스 변수는 위 예와 같이 `클래스이름.클래스변수`로 사용할 수 있다.

또는 다음과 같이 Family 클래스로 만든 객체를 통해서도 클래스 변수를 사용할 수 있다.


```python
Family.lastname = "lee"

print(Family.lastname)
```

    lee
    

클래스 변수 값을 변경했더니 클래스로 만든 객체의 lastname 값도 모두 변경된다는 것을 확인할 수 있다. 즉 클래스 변수는 클래스로 만든 모든 객체에 공유된다는 특징이 있다.

id 함수를 사용하면 클래스 변수가 공유된다는 사실을 증명할 수 있다.


```python
print(id(Family.lastname))

a = Family()
b = Family()

print(id(a.lastname))
print(id(b.lastname))
```

    1768394882032
    1768394882032
    1768394882032
    

id 값이 모두 같으므로 `Family.lastname`, `test_cal.lastname`, `test_cal.lastname`은 모두 같은 메모리를 가리키고 있다.

클래스 변수를 가장 늦게 설명하는 이유는 클래스에서 클래스 변수보다는 객체변수가 훨씬 중요하기 때문이다. 실무 프로그래밍을 할 때도 클래스 변수보다는 객체변수를 사용하는 비율이 훨씬 높다.
