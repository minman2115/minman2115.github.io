---
layout: post
title: "해시테이블 기본개념"
tags: [Python, 자료구조 알고리즘]
comments: true
---

.

Coding_test_training(20220309)

[학습자료]

패스트 캠퍼스 "알고리즘 / 기술면접 완전 정복 올인원 패키지 Online." 강의를 공부하고 정리한 내용입니다.

** URL : https://fastcampus.co.kr/dev_online_algo

[학습내용]

- 아주 간단한 해시테이블 만들기

```python
hash_table = [0 for i in range(10)]
hash_table
```

```text
[0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
```


- 아주 간단한 해시함수 만들기

해시함수는 다양한 방법이 있으며, 가장 간단한 방법이 나누기를 통한 나머지 값을 사용하는 방법이다.


```python
def hash_func(key):
    return key%7
```

- 해시테이블에 데이터 저장해보기

```python
data1 = "minman"
data2 = "lee"
data3 = "hong"

# ord() : 특정문자의 아스키 코드를 리턴함, 일종의 인코딩 방식.
# 아스키 코드는 영어와 일부 특수문자 정도만 코드화 할 수 있음. 한국어 인코딩 불가함. 그래서 나온게 유니코드.
# 유니코드는 한국어 영어 등 문자들을 문자에 해당하는 특정 숫자값으로 인코딩하는 방식임.
print(ord(data1[0]), ord(data1[1]), ord(data1[2]))
# ord로 문자열을 인코딩해서 키값을 아래와 같이 생성하게 된다.
```

```text
109 105 110
```


```python
print(ord(data1[0]), hash_func(ord(data1[1])))
# 아래에 결과값으로 나온 0을 해쉬 주소라고 할수 있음.
```

```text
109 0
```

- 해쉬 주소를 갖고 데이터를 저장하는 함수를 만들어보자.


```python
hash_table = [0 for i in range(10)]

def hash_func(key):
    return key%7

def hash_storage(data,value):
    key = ord(data[0])
    hash_address = hash_func(key)
    hash_table[hash_address] = value
    
hash_storage('minman','data engineer')
hash_storage('lee','public official')
hash_storage('jeon','security guard')
```

- 해시에 저장한 데이터를 가져와보자


```python
def get_data(data):
    key = ord(data[0])
    hash_address = hash_func(key)    
    return hash_table[hash_address]

get_data("minman")
```



```text
    'data engineer'
```



```python
get_data("lee")
```



```text
    'public official'
```



```python
get_data("jeon")
```



```text
    'security guard'
```


정리하면 데이터를 받아서(minman, lee, jeon) 첫문자를 ord 함수로 인코딩해서 키값을 추출하고, 그런 다음에 키값을 해시함수로 변환해서 나온 해시값(슬롯번호)에 해당하는 슬롯에 데이터를 저장하는 방식이다.

- 연습문제

파이썬 리스트를 이용해 해시테이블 구현하기

조건 1. 해시함수 : key % 5

조건 2. 해시키를 생성하는 함수 : hash(data)


```python
hash_tb = [0 for i in range(10)]

def get_key(data):
    return hash(data)

def hash_func(key):
    return key % 5

def store_data_in_hash(data,value):
    hash_add = hash_func(get_key(data))
    hash_tb[hash_add] = value
    
def read_data_from_hash(data):
    hash_add = hash_func(get_key(data))
    return hash_tb[hash_add]

store_data_in_hash('minman','data engineer')
store_data_in_hash('lee','public official')
store_data_in_hash('kim','security guard')
```


```python
read_data_from_hash('minman')
```



```text
    'data engineer'
```



```python
read_data_from_hash('kim')
```



```text
    'security guard'
```



```python
read_data_from_hash('lee')
```



```text
    'public official'
```


- 해시테이블의 가장 큰 문제는 해시충돌의 경우이다. 이를 해결하는 방법은 두가지가 있다.


방법 1. Chaining 기법

해쉬 테이블 저장공간 외의 공간을 활용하는 기법

결론적으로 충돌이 일어나면, 링크드 리스트라는 자료 구조를 사용해서, 링크드 리스트로 데이터를 추가로 뒤에 연결시켜서 저장하는 기법


```python
# 아래의 경우는 링크드 리스트 대신에 파이썬에 그냥 리스트를 사용해서 구현한 것임

hash_tb = [0 for i in range(10)]

def get_key(data):
    return hash(data)

def hash_func(key):
    return key % 5

def store_data_in_hash(data,value):
    index_key = get_key(data)    
    hash_add = hash_func(index_key)
    
    # 해시테이블 슬롯에 이미 데이터가 존재하고 있는 경우
    if hash_tb[hash_add] != 0:
        for index in range(len(hash_tb[hash_add])):
            print(index,hash_tb[hash_add])
            if hash_tb[hash_add][index][0] == index_key:
                hash_tb[hash_add][index][1] = value
                return
            
        hash_tb[hash_add].append([index_key,value])
    
def read_data_from_hash(data):
    index_key = get_key(data)    
    hash_add = hash_func(index_key)
    
    if hash_tb[hash_add] != 0:
        for index in range(len(hash_tb[hash_add])):
            if hash_tb[hash_add][index][0] == index_key:
                return hash_tb[hash_add][index][1]
            
        return None
                        
    else:
        return None

store_data_in_hash('minman','data engineer')
store_data_in_hash('lee','public official')
store_data_in_hash('jeon','security guard')
```


```python
hash('jeon')
```



```text
    774756220597252233
```



```python
hash('jeon') % 5
```



```text
    3
```



```python
hash('minman')
```



```text
    5652876852841394078
```



```python
hash('minman') % 5
```



```text
    3
```



```python
import pprint

pprint.pprint(hash_tb)
```

    [0,
     [[1323991744489545081, 'public official']],
     0,
     [[5652876852841394078, 'data engineer'],
      [774756220597252233, 'security guard']],
     0,
     0,
     0,
     0,
     0,
     0]



```python
read_data_from_hash('minman')
```



```text
    'data engineer'
```



```python
read_data_from_hash('jeon')
```



```text
    'security guard'
```



```python
read_data_from_hash('lee')
```



```text
    'public official'
```


방법 2. Linear Probing 기법

해쉬 테이블 저장공간 안에서 충돌 문제를 해결하는 기법

충돌이 일어나면, 해당 hash address의 다음 address부터 맨 처음 나오는 빈공간에 저장하는 기법

저장공간의 활용도를 높일 수 있는 기법임


```python
# 아래의 경우는 링크드 리스트 대신에 파이썬에 그냥 리스트를 사용해서 구현한 것임

hash_tb = [0 for i in range(10)]

def get_key(data):
    return hash(data)

def hash_func(key):
    return key % 5

def store_data_in_hash(data,value):
    index_key = get_key(data)    
    hash_add = hash_func(index_key)
    
    # 해시테이블 슬롯에 이미 데이터가 존재하고 있는 경우
    if hash_tb[hash_add] != 0:
        for index in range(hash_add,len(hash_tb)):
            if hash_tb[index] == 0:
                hash_tb[index] = [index_key,value]
                return
            
            elif hash_tb[index] == index_key:
                hash_tb[index][1] = value
                return
            
    else:
        hash_tb[hash_add] = [index_key,value]

def read_data_from_hash(data):
    index_key = get_key(data)    
    hash_add = hash_func(index_key)
    
    if hash_tb[hash_add] != 0:
        for index in range(hash_add,len(hash_tb)):
            if hash_tb[index] == 0:
                return None
            
            elif hash_tb[index][0] == index_key:
                return hash_tb[index][1]

    else:
        return None

store_data_in_hash('minman','data engineer')
store_data_in_hash('lee','public official')
store_data_in_hash('jeon','security guard')
```


```python
hash('jeon')
```



```text
    774756220597252233
```



```python
hash('jeon') % 5
```



```text
    3
```



```python
hash('minman')
```



```text
    5652876852841394078
```



```python
hash('minman') % 5
```



```text
    3
```



```python
import pprint

pprint.pprint(hash_tb)
```

```text
    [0,
     [1323991744489545081, 'public official'],
     0,
     [5652876852841394078, 'data engineer'],
     [774756220597252233, 'security guard'],
     0,
     0,
     0,
     0,
     0]
```


```python
read_data_from_hash('minman')
```



```text
    'data engineer'
```



```python
read_data_from_hash('jeon')
```



```text
    'security guard'
```



```python
read_data_from_hash('lee')
```



```text
    'public official'
```


- 그러면 빈번한 충돌을 개선하는 방법은 따로 없을까

--> 해시함수을 재정의하고 테이블 저장공간을 확대하는게 좋다.

예를들어서 저장할 데이터가 4개이고 슬롯이 8개라면 16개까지 슬롯을 늘려주는게 일반적이다.


```python
ex) 

hash_table = [None for i in range(16)]

def hash_func(key):
    return key % 16
```

- 참고사항 : 해쉬 함수와 키 생성 함수

파이썬의 hash() 함수는 실행할 때마다, 값이 달라질 수 있음

따라서 유명한 해쉬 함수들이 있고 이들을 사용하는게 좋음. 예를 들어서 SHA(Secure Hash Algorithm) 라는게 있음.

어떤 데이터도 유일한 고정된 크기의 고정값을 리턴해주므로, 해쉬 함수로 유용하게 활용 가능함.

(1) SHA-1


```python
import hashlib

data = 'test'.encode()
hash_object = hashlib.sha1()
hash_object.update(data)
hex_dig = hash_object.hexdigest()
print (hex_dig)
```

```text
    a94a8fe5ccb19ba61c4c0873d391e987982fbbd3
```

(2) SHA-256


```python
import hashlib

data = 'test'.encode()
hash_object = hashlib.sha256()
hash_object.update(data)
hex_dig = hash_object.hexdigest()
print (hex_dig)
```

```text
    9f86d081884c7d659a2feaa0c55ad015a3bf4f1b2b0b822cd15d6c15b0f00a08
```

- 해시 테이블 코드에 키 생성 함수를 sha256 해쉬 알고리즘을 사용하도록 변경해보기

(1) Chaining 기법으로 구현


```python
# 아래의 경우는 링크드 리스트 대신에 파이썬에 그냥 리스트를 사용해서 구현한 것임

import hashlib

hash_tb = [0 for i in range(8)]

def get_key(data):
    hash_object = hashlib.sha256()
    hash_object.update(data.encode())
    hex_dig = hash_object.hexdigest()
    
    # 16진수의 문자열을 10진수의 정수로 변환해서 리턴
    return int(hex_dig, 16)

def hash_func(key):
    return key % 8

def store_data_in_hash(data,value):
    index_key = get_key(data)
    hash_add = hash_func(index_key)
    print(hash_add)
    
    # 해시테이블 슬롯에 이미 데이터가 존재하고 있는 경우
    if hash_tb[hash_add] != 0:
        for index in range(len(hash_tb[hash_add])):
            if hash_tb[hash_add][index][0] == index_key:
                hash_tb[hash_add][index][1] = value
                return
            
        hash_tb[hash_add].append([index_key,value])
    
    # 해시테이블 슬롯에 데이터가 존재하지 않는 경우
    else:
        hash_tb[hash_add] = [[index_key,value]]
    
def read_data_from_hash(data):
    index_key = get_key(data)    
    hash_add = hash_func(index_key)
    
    if hash_tb[hash_add] != 0:
        for index in range(len(hash_tb[hash_add])):
            if hash_tb[hash_add][index][0] == index_key:
                return hash_tb[hash_add][index][1]
            
        return None
                        
    else:
        return None
    
store_data_in_hash('minman','data engineer')
store_data_in_hash('lee','public official')
store_data_in_hash('jeon','security guard')
```

```text
    7
    4
    4
```


```python
get_key('jeon')
```



```text
    45346129150026477640700068094382684880115347822051191015517170001848761462876
```



```python
hash_func(get_key('jeon'))
```



```text
    4
```



```python
get_key('lee')
```



```text
    9513964800018998613193793952269983956000544315811060080871941187466581436596
```



```python
hash_func(get_key('lee'))
```



```text
    4
```



```python
import pprint

pprint.pprint(hash_tb)
```

```text
    [0,
     0,
     0,
     0,
     [[9513964800018998613193793952269983956000544315811060080871941187466581436596,
       'public official'],
      [45346129150026477640700068094382684880115347822051191015517170001848761462876,
       'security guard']],
     0,
     0,
     [[102551494643304287630723721189220063742289318403062479390579687622742222748175,
       'data engineer']]]
```


```python
read_data_from_hash('minman')
```



```text
    'data engineer'
```



```python
read_data_from_hash('jeon')
```



```text
    'security guard'
```



```python
read_data_from_hash('lee')
```



```text
    'public official'
```


(2) Linear Probing 기법


```python
import hashlib

hash_tb = list([0 for i in range(8)])

def get_key(data):
    hash_object = hashlib.sha256()
    hash_object.update(data.encode())
    hex_dig = hash_object.hexdigest()
    return int(hex_dig, 16)

def hash_func(key):
    return key % 8

def store_data_in_hash(data, value):
    index_key = get_key(data)
    hash_add = hash_func(index_key)
    if hash_tb[hash_add] != 0:
        for index in range(hash_add, len(hash_tb)):
            if hash_tb[index] == 0:
                hash_tb[index] = [index_key, value]
                return
            elif hash_tb[index][0] == index_key:
                hash_tb[index][1] = value
                return
    else:
        hash_tb[hash_add] = [index_key, value]

def read_data_from_hash(data):
    index_key = get_key(data)
    hash_add = hash_func(index_key)
    
    if hash_tb[hash_add] != 0:
        for index in range(hash_add, len(hash_tb)):
            if hash_tb[index] == 0:
                return None
            elif hash_tb[index][0] == index_key:
                return hash_tb[index][1]
    else:
        return None
    
store_data_in_hash('minman','data engineer')
store_data_in_hash('lee','public official')
store_data_in_hash('jeon','security guard')
```


```python
get_key('jeon')
```



```text
    45346129150026477640700068094382684880115347822051191015517170001848761462876
```



```python
hash_func(get_key('jeon'))
```



```text
    4
```



```python
get_key('lee')
```



```text
    9513964800018998613193793952269983956000544315811060080871941187466581436596
```



```python
hash_func(get_key('lee'))
```



```text
    4
```



```python
import pprint

pprint.pprint(hash_tb)
```

```text
    [0,
     0,
     0,
     0,
     [9513964800018998613193793952269983956000544315811060080871941187466581436596,
      'public official'],
     [45346129150026477640700068094382684880115347822051191015517170001848761462876,
      'security guard'],
     0,
     [102551494643304287630723721189220063742289318403062479390579687622742222748175,
      'data engineer']]
```


```python
read_data_from_hash('minman')
```



```text
    'data engineer'
```



```python
read_data_from_hash('jeon')
```



```text
    'security guard'
```



```python
read_data_from_hash('lee')
```



```text
    'public official'
```

