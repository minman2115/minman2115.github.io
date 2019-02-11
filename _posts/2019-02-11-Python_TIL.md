---
layout: post
title: "Python TIL (20180802)"
tags: [Python]
comments: true
---

.

#### # 컴퓨터는 음수를 2의 보수로 저장한다.

- CPU안에 산술연산이 ALU에서 연산이 이루어지는데


- ALU안에는 가산기 밖에 없다. 이 가산기를 이용해서 모든 계산을 수행한다. 감산기가 없다. 


- 그러므로 2의 보수를 이용해서 예를 들어 9 -4를 하는게 아니라 9 +(-4)를 실행한다.

#### # 컴퓨터는 실수를 부동소수점으로 저장한다.

- 여기서 부동은 떠다니다 floating의 의미이다.


- 이것의 의미는 뭐냐 예를 들어 1234는 123.4 * 10의 1승으로 표현된다.


- 또는 1234는 12.34 * 10의 2승으로 표현되거나 1.234 * 10의 3승으로 표현된다.


- 이런식으로 소수점이 왔다갔다 하면서 떠다닌다고 해서 부동소수점이다.


- 파이썬에서는 실수는 최대 1.79 * 10의 308승 최소 2.22 * 10의 -308승을 표현할 수 있다.


- 파이썬에서 실수표현의 최대 약점은 정밀도가 떨어진다는 것이다.


- C에서 실수를 표현할때는 통상 float(4바이트)와 double(8바이트)의 자료형을 쓴다.


- 파이썬에서는 예를 들어서 a = 4.5라고 표현하면 더블로 저장한다. 더블에서 첫번째 비트는 싸인비트라고 해서 0이면 +이고 1이면 -이다.


- 컴퓨터는 실수를 10진수 15자리까지 정확하게 표현하지만 그거 보다 1만 더 커져도 정확하게 표현하지 못한다.

예를들어 9007199254740992.0 에서 뒤에서 두번째 9까지는 정확하게 표현하는데 그 뒤에 수는 정확하지 않다. 연산을 해도 정확하게 연산이 안된다.

#### # 엡실론

- 실수로 표현할 수 있는 1.0과 the next representable number(그 다음으로 표현할 수 있는 수. 즉 다음표현 가능 수, t라고 치면 ) t-1.0이 된다.


- 1.000000000......1 * 2의 0승 소수점 52비트에서 가장 마지막 비트가 1인경우 이놈이 t다.


- t-1.0은 0.0000000...1 *2의 0승 52번째에서 1 인 이놈이 엡실론이다.

#### # 컴퓨터가 실수를 표현할때 정밀도 차이에 의한 오차를 조정할때 사용하는 방법

- 크게 세가지가 있는데 절대비교, 상대비교, 엡실론비교가 있다.


- 절대비교 : 두수차의 절대값을 구하고 이 차이가 1e-10(1의 -10승)보다 작으면 true를 반환하게하고 아니면 false를 반환하게 하면 된다.

ex)


```python
from math import fabs

a=0.1*3
b=0.3

def is_equal(a, b):
    return fabs(a-b) <= 1e-10

if is_equal(a, b):
    print('things')
```

    things
    

- 상대비교

- 알고리즘 :  만약의 절대값(fabs)을 취한 두 수의 차이가 1e-10 보다 작거나 같으면 true 아니면 false, 두수의 차이가 두 수 중에서 큰 수에 1e-8(1의 -8승)를 곱한 값보다 작거나 같으면 true 아니면 false

- relative error = fabs(a-b)(두수의 차이의 절대값) /(나누기) max(fabs(a),fabs(b)) 절대값 a와 b의 차에서 두 수중에 큰수를 하나 뽑아서 나눠준것 이렇게 하면 오차가 어느정도 나는지 나온다.

- 알고리즘과 렐러티드 에러와의 관계 : 결론적으로 렐러티드 에러가 엡실론보다 작을때는 같다고 인정해주자, 1e-10보다 작을때 같다고 인정해주자. 이런 의미이다. 상대오차의 차이일뿐.. 상대오차 차이도 프로그래머가 조절할 수 있다.

- 큰 수던 작은 수던 상대적인 오차는 같다. 예를 들어 1999 와 2000의 상대오차와 1.999 와 2.0의 오차는 같다



- epsilon comparison : 상대비교에서 상대오차값을 epsilon으로 한 것

ex)


```python
a=0.1*3
b=0.3

from math import fabs
import sys
def is_equal(a, b, w=0): 
## bool(true 또는 false를 출력하는)를 출력하는 함수임

    ep=sys.float_info.epsilon
    diff=fabs(a-b)
    
    return diff <= max(fabs(a), fabs(b))*ep*(2**w)
    ## 위에서 ep는 엡실론이고, (2**w)는 가중치를 의미한다.

if is_equal(a, b):
    print('things')
```

    things
    

#### # call by value, call by reference, call by object reference(또는 assignment)

1) call by value

스텍프레임이 아예 다른 공간이기 때문에 변수 자체를 전달하는 것이 아니라 값에 의해 복사해서 전달하는 경우

2) call by reference

메모리의 주소값을 전달하는 경우 ex) c에서 포인터


3) call by object reference(또는 assignment)

- immutable의 경우

메모리를 새로 할당해주는 방법으로 변경해줘야 한다.

- immutable 변수 : 변경불가능

예를 들어 수, 문자열, 튜플

예시1) call by object reference(또는 assignment)로 mutable이 안되는 경우 이렇기 때문에 예시 2)의 방법으로 mutable을 바꿀 수 있다.(결론적으로 함수로부터 리턴 값을 받아 새로운 변수에 저장하면된다.)


예시1)


```python
tu = (1,2,3)
def change_value_tu(tu, *args):
    tu=args
    print(str(tu)+'in change_value')

change_value_tu(tu, 4,5,6)
print(str(tu)+'in main')
```

    (4, 5, 6)in change_value
    (1, 2, 3)in main
    

예시2)


```python
tu = (1,2,3)
def change_value_tu(tu, *args):
    tu=args
    print(str(tu)+'in change_value')
    return tu

tu = change_value_tu(tu, 4,5,6)
print(str(tu)+'in main')
```

    (4, 5, 6)in change_value
    (4, 5, 6)in main
    

- mutable의 경우

변수 내 데이터를 변경할 수 있다.

- mutable 변수 : 변경가능

예를들어 리스트, 딕셔너리	

예시1)


```python
li=[1,2,3] 

def change_val_li(li, idx, val):
    li[idx]=val
    print(str(li)+'in change_val_li')
    
change_val_li(li,0,55)
print(str(li)+'in main')
```

    [55, 2, 3]in change_val_li
    [55, 2, 3]in main
    

step1) 메인 스텍프레임이 메모리의 최하단에 만들어져 쌓이게 되고 그 안에는 리스트와 change_val_li의 내용이 저장된다. 스텍프레임 안에 저장된 리스트는 li = [li[0]인데 *(1이 저장되어 있는 주소를 가리키는 포인터), li[1]인데 *(2가 저장되어 있는 주소를 가리키는 포인터), li[2]인데 *(3이 저장되어 있는 주소를 가리키는 포인터)]로 되어있다.

step2) 그리고 change_val_li(li,0,55)가 실행되면 메인스텍 위에 change_val_li 함수 스텍이 쌓이게 된다.

step3) 그리고 그 함수 스텍안에 아래부터 순서대로 val, idx, li가 저장되어 있고 이 각각의 공간은 55가 저장되어 있는 주소를 가리킴, 0이 저장되어 있는 주소를 가리킴, li 리스트의 주소를 가리킴

step4) li[idx]=val이 실행이 되면 li[0]=55가 되기 때문에 li[0]가 즉 1이 저장되어 있는 주소를 가리키는 포인터가 55가 저장되어 있는 주소를 가리키는 포인터로 바뀌게 된다.

step5) 그리고 change_val_li 함수가 종료되게 되면 change_val_li 함수 스텍이 사라지게되고 메인 스텍 만 남게 되는데 li=[li[0]인데 *(55가 저장되어 있는 주소를 가리키는 포인터),li[1]인데 *(2가 저장되어 있는 주소를 가리키는 포인터),li[3]인데 *(3이 저장되어 있는 주소를 가리키는 포인터)]로 남게 된다.

예시2) 

- 예시1)과의 차이점을 비교해보자


```python
li=[1,2,3] 

def change_value(li, new_li):
    li=new_li
    print(str(li)+'in change_value')
    
change_value(li,[5,2,3])
print(str(li)+'in main')
```

    [5, 2, 3]in change_value
    [1, 2, 3]in main
    

step1) 메인 스텍프레임이 메모리의 최하단에 만들어져 쌓이게 되고 그 안에는 리스트와 change_value의 내용이 저장된다. 스텍프레임 안에 저장된 리스트는 li = [li[0]인데 *(1이 저장되어 있는 주소를 가리키는 포인터), li[1]인데 *(2가 저장되어 있는 주소를 가리키는 포인터), li[2]인데 *(3이 저장되어 있는 주소를 가리키는 포인터)]로 되어있다.

step2) 그리고 change_value(li,[5,2,3])가 실행되면 메인스텍 위에 change_value 함수 스텍이 쌓이게 된다.

step3) 그리고 그 함수 스텍안에 아래부터 순서대로 new_li, li가 저장되어 있고 이 각각의 공간 new_li = [5,2,3] 이라는 리스트 주소를 가리키고 있고 li는 li=[1,2,3]의 리스트 주소를 가리키고 있다.

step4) li = new_li이 실행이 되면 li는 new_li = [5,2,3] 이라는 리스트 주소를 가리키게 된다.

step5) 그리고 change_value 함수가 종료되게 되면 change_value 함수 스텍이 사라지게 되고 li는 new_li = [5,2,3] 이라는 리스트 주소를 가리키는 내용도 메모리에서 사라지게 된다.

step5) 결론적으로 메인함수에 있는 li는 li=[1,2,3]의 리스트 주소를 가리키고 있다.
