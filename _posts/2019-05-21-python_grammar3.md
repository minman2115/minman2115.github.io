---
layout: post
title: "파이썬 기초문법 연습문제 풀이"
tags: [Python]
comments: true
---

.

#### 그림, 실습코드 등 학습자료 출처 : https://gitlab.com/radajin

#### 각종 툴 사전 SETUP


```python
import random
from functools import reduce
```

#### 1. 삼항연산을 이용하여 값을 입력받아 짝수이면 even, 홀수이면 odd를 출력하는 코드를 작성하세요. 


```python
_input = int(input("숫자를 입력하시오 : "))

print("even" if _input % 2 == 0 else "odd")
```

    숫자를 입력하시오 : 5
    odd
    

#### 2. 구구단을 4단까지 세로로 출력하는 코드를 작성하세요. 

```
2단
2*1=2
2*2=4
2*3=6
2*4=8
2*5=10
2*6=12
2*7=14
2*8=16
2*9=18

3단
3*1=3
3*2=6
3*3=9
3*4=12
3*5=15
3*6=18
3*7=21
3*8=24
3*9=27

4단
4*1=4
4*2=8
4*3=12
4*4=16
4*5=20
4*6=24
4*7=28
4*8=32
4*9=36
```

#### 3. 구구단을 9단까지 가로로 출력하는 코드를 작성하세요. 


```python
for num1 in range(2,10):
    print("{}단".format(num1))
    for num2 in range(1,10):
        print("{} * {} = {}".format(num1,num2,num1*num2),end ='   ')
    print()
    print()
```

    2단
    2 * 1 = 2   2 * 2 = 4   2 * 3 = 6   2 * 4 = 8   2 * 5 = 10   2 * 6 = 12   2 * 7 = 14   2 * 8 = 16   2 * 9 = 18   
    
    3단
    3 * 1 = 3   3 * 2 = 6   3 * 3 = 9   3 * 4 = 12   3 * 5 = 15   3 * 6 = 18   3 * 7 = 21   3 * 8 = 24   3 * 9 = 27   
    
    4단
    4 * 1 = 4   4 * 2 = 8   4 * 3 = 12   4 * 4 = 16   4 * 5 = 20   4 * 6 = 24   4 * 7 = 28   4 * 8 = 32   4 * 9 = 36   
    
    5단
    5 * 1 = 5   5 * 2 = 10   5 * 3 = 15   5 * 4 = 20   5 * 5 = 25   5 * 6 = 30   5 * 7 = 35   5 * 8 = 40   5 * 9 = 45   
    
    6단
    6 * 1 = 6   6 * 2 = 12   6 * 3 = 18   6 * 4 = 24   6 * 5 = 30   6 * 6 = 36   6 * 7 = 42   6 * 8 = 48   6 * 9 = 54   
    
    7단
    7 * 1 = 7   7 * 2 = 14   7 * 3 = 21   7 * 4 = 28   7 * 5 = 35   7 * 6 = 42   7 * 7 = 49   7 * 8 = 56   7 * 9 = 63   
    
    8단
    8 * 1 = 8   8 * 2 = 16   8 * 3 = 24   8 * 4 = 32   8 * 5 = 40   8 * 6 = 48   8 * 7 = 56   8 * 8 = 64   8 * 9 = 72   
    
    9단
    9 * 1 = 9   9 * 2 = 18   9 * 3 = 27   9 * 4 = 36   9 * 5 = 45   9 * 6 = 54   9 * 7 = 63   9 * 8 = 72   9 * 9 = 81   
    
    

#### 4. 로또번호를 출력하는 코드를 작성하시오

주의사항 : 1 ~ 45 사이의 숫자를 랜덤으로 뽑되 숫자가 중복불가


```
출력 예시

[3, 8, 15, 22, 27, 29]
```


```python
lotto = []

while True:
    
    num = random.randint(1,45)
    
    if num not in lotto:
        lotto.append(num)
    
    if len(lotto) >= 6:
        break

print(lotto)
```

    [3, 7, 19, 8, 16, 22]
    

#### 5. 5를 입력했을때 아래와 같이 별이 출력되도록 코드를 작성하세요.

```
*
**
***
****
*****
```


```python
_input = int(input("숫자를 입력하시오 : "))

star = 1

for i in range(0,_input):
    print("{}".format("*" * star))
    star +=1
```

    숫자를 입력하시오 : 5
    *
    **
    ***
    ****
    *****
    

#### 6. kim 씨 성을 가진 사람은 데이터에서 제거하고, lee 씨 성을  가진 사람은 성을 삭제하는 코드를 작성하시오

주의사항 : list comprehension을 사용하여 코드를 작성

```

예시

(입력) => ["kim test", "park python", "lee data", "jung science", "lee school"] 
(결과) => ["park python", "data", "jung science", "school"] 
```


```python
names = ["kim test", "park python", "lee data", "jung science", "lee school"] 

result = [ 
    name.split(" ")[1] if name.split(" ")[0] == 'lee' else name
    for name in names
    if name.split(" ")[0] != 'kim'
]
result
```




    ['park python', 'data', 'jung science', 'school']



#### 7. map을 이용하여 아래 리스트에 있는 성의 가장 앞 문자를 대문자로 수정하고, filter를 사용하여 사람 이름에서 성이 Lee인 사람의 이름만 결과로 출력하는 코드 작성하시오

name_list = ["kim kildong", "lee jeawoo", "park sangjin", "kim kilsoo", "lee sungsim"]


```python
name_list = ["kim kildong", "lee jeawoo", "park sangjin", "kim kilsoo", "lee sungsim"]
family_name = "Lee"

name_list = list(map(lambda name : name[0].upper()+name[1:],name_list))
                 
result = list(filter(lambda name : name.split(" ")[0] == family_name,name_list))

result
```




    ['Lee jeawoo', 'Lee sungsim']



#### 8. reduce를 사용하여 사람의 이름 길이가 가장 긴 사람 1명을 출력하시오

```
name_list = ["Kim kildong", "Lee jeawoo", "Park sangjin", "Kim kilsoo", "Lee sungsim"]
```


```python
name_list = ["Kim kildong", "Lee jeawoo", "Park sangjin", "Kim kilsoo", "Lee sungsim"]

result = reduce(lambda name1, name2 : name1 if len(name1) > len(name2) else name2, name_list)

print(result)
```

    Park sangjin
    

#### 9. map 함수를 map_func 함수의 이름으로 구현하시오

- 리스트의 데이터를 아래의 예시와 같이 더하는 함수를 만드는데 따로 함수를 선언해서 사용하지 말고 map의 첫번째 파라미터에 lambda 함수로 구현하시오

```
ls1 = [1,2,3,4]
ls2 = [5,6,7]
ls3 = [9,10,11,12]

result = [15, 18, 21]
```


```python
ls1 = [1,2,3,4]
ls2 = [5,6,7]
ls3 = [9,10,11,12]

def map_filter(func, *datas):
    return [func(*data) for data in zip(*datas)]

result = list(map(lambda *args : sum(args),ls1,ls2,ls3))

result
```




    [15, 18, 21]



#### 10. 아래에 있는 데이터를 class와 object로 나타내세요.

- 클래스에서는 타율(안타/타석) 정보를 볼수 있는 기능(avg)을 클래스의 함수로 추가하세요.

```
김선빈(ksb) - 타석:476, 안타:176
박건우(pgw) - 타석:483, 안타:177
박민우(pmw) - 타석:388, 안타:141
```


```python
# TODO
class player:
    
    def __init__(self, bb, hit):
        self.bb = bb
        self.hit = hit
    
    def avg(self):
        return round(self.hit / self.bb,3)
        
ksb = player(476, 176)
pgw = player(483, 177)
pmw = player(388, 141)

print("ksb : {}".format(ksb.avg()))
print("pgw : {}".format(pgw.avg()))
print("pmw : {}".format(pmw.avg()))
```

    ksb : 0.37
    pgw : 0.366
    pmw : 0.363
    

#### 11. `input`을 이용하여 숫자를 입력받는 코드를 작성하시오.

- 문자가 입력되면 에러 메시지를 보내고 다시 숫자를 입력받도록 만들어 주세요.


```python
while True:
    try:
        number = int(input("insert number : "))
        break
    except Exception as e:
        print(e)
print(type(number), number)
```

    insert number : asd
    invalid literal for int() with base 10: 'asd'
    insert number : 1
    <class 'int'> 1
    

#### 12. 다음 두 Nested List를 더하는 코드를 List comprehension 을 이용해서 작성하세요.

힌트 : 행렬의 덧셈을 이용


```python
X = [[12,7,3],
     [4 ,5,6],
     [7,8,9]]

Y = [[5,8,1],
     [6,7,3],
     [4,5,9]]

# TODO

result = [list(sum(data) for data in zip(X[number],Y[number])) for number in range(0,2+1)]

result

print(' X + Y = ')

for r in result:
    print(r)
```

     X + Y = 
    [17, 15, 4]
    [10, 12, 9]
    [11, 13, 18]
    
