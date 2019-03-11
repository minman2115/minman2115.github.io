---
layout: post
title: "Python TIL (20180807)"
tags: [Python]
comments: true
---

.

#### # 클래스메서드

- 형식은 classmethod(클래스명, 인자) 로 되어있다. 

- ex)


```python
class Person:
    
    @classmethod
    def init_from_string(cls, string):
        name,age = string.split('_')
        return name, int(age)
```

- 여기서는 예를 들어 cls에는 Person클래스 자체가 인자로 들어오게 되고 string은 예를들어 li=['john_36','minsu_28','kim_25'] 를 이름과 나이 넣고 싶을때


```python
li=['john_36','minsu_28','kim_25'] 

ojt_list=[]
for string in li:
    ojt_list.append(Person.init_from_string(string))
    
ojt_list
```




    [('john', 36), ('minsu', 28), ('kim', 25)]



- 위와같이 입력하여 init_from_string함수를 활용한다.

#### # Property 예시


```python
class Account:    
    def __init__(self, money):
        self._balance=money
        ## 만약에 self.balance=money이면 balance setter 함수를 호출한다.
        
    @property
    def balance(self):
        print('balance_getter')
        return self._balance
    
    @balance.setter
    def balance(self,new_bal):
        print('balance_setter')
        if new_bal < 0 :
            return
        self._balance=new_bal
```


```python
my_acnt=Account(5000)

my_acnt.balance
```

    balance_getter
    




    5000



위의 실행결과를 확인하고 아래와 같이 명령어를 넣고 실행하면 아래과 같은 실행결과가 나온다.


```python
my_acnt.balance = 3000
```

    balance_setter
    

 property는 my_acnt.balance 이런식으로 명령어를 실행하면 바로 벨런스라는
 멤버에 직접 접근하는 것처럼 보이지만 def balance(self): 함수를 호출한다
 통상 my_acnt.balance 이걸 실행하면

@property

def balance(self):

print('balance_getter')

return self._balance

이놈이 실행되고

만약에 my_acnt.balance = 3000 이렇게 실행하면 

@balance.setter

def balance(self,new_bal):

print('balance_setter')

if new_bal < 0 :

return self._balance=new_bal

함수가 실행된다.

def __init__(self, money):

self._balance=money

이 함수에서 만약에 self.balance=money이면 머니라는
숫자값이 self.balance로 들어가기 때문에 balance setter 함수를 호출한다.

#### # 클래스 간의 관계

- 프로그래밍의 복잡성이 커질 수록 클래스간의 관계형성이 중요하다. 

1) IS-A 관계

- ex) A lap-top is a computer
     
- X(클래스)는 Y(클래스)의 '일종'이다. 그래서 is a 가 나온것이다.
     
- inheritence(상속) 기법을 쓴다. 

상속받는 입장이 반드시 논리적으로 상위 클래스의 모든 멤버변수와 모든 기능을 가지는 상태가 되야한다.	

2) HAS-A관계

- ex) A policeman has a gun

- X(클래스)는 Y(클래스)를 가지고 있다(또는 포함한다).

- composition(합성), aggregation(통합) 을 쓴다. 이 둘의 용어 구분이 감이 안잡히는데 그것은 중요한 것은 아니고 그냥 has a 관계구나 라고 생각하면 된다.

- composition 특성 

(1) 클래스간에 has a 상황 기반에서 같은 life cycle이고 강하게 연관되어 있다.

(2) 멤버가 클래스의 통해 만들어진 객체를 갖는다. 

- aggregation 특성 : 클래스간 약한 연관


3) 이즈어나 해즈어 는 결국 논리적으로 잘 판단해야하며, 둘을 구분하기 애매한 경우 헤즈어를 쓰는경우가 많다고 한다. 


#### # 다형성

- OOP를 사용하는 궁극적인 목적 : 다형성

- 다형성은 상속을 해야하는 상황에서 발생한다.

- 다형성의 정의 : 상속을 할때, 같은 이름의 메서드를 호출할때 그 메서드를 호출하는 객체가 서로 다르기 때문에 그 결과 값이 서로 다른 것

- 다형성은 method overriding으로 구현한다.
