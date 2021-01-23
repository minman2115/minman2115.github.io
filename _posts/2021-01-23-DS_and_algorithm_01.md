---
layout: post
title: "Hash Collision 해결을 위한 알고리즘 기초개념"
tags: [Python, 자료구조 알고리즘]
comments: true
---

.

[학습시 참고자료]

패스트캠퍼스 '알고리즘 / 기술면접 완전 정복 올인원 패키지 Online' 를 공부하고 정리한 학습노트입니다.

URL : https://www.fastcampus.co.kr/dev_online_algo

[학습내용]

해쉬 테이블의 가장 큰 문제는 복수개의 데이터가 동일한 어드레스에 저장될 경우인데 이를 충돌(Collision) 또는 해쉬 충돌(Hash Collision)이라고 부른다.

그러면 이를 해결하기 위해 어떤 알고리즘 기법이 있을까. 크게 두가지 기법이 있다. 

하나는 오픈해싱이라고 해서 해시테이블 안에서 충돌이 발생하는 경우의 데이터만 해시테이블 밖에 추가적인 데이터 공간을 확보해서 거기에 저장을 하고 해결하는 방법이 있다.

다른 하나는 '아니 그러면 충돌할때마다 공간을 확보한다는게 데이터 공간에 제약이 있으면 어떻게 할거냐 공간을 아껴야 한다.'라는 얘기가 나올 수 있는데 그러면 해시테이블 안에서 빈공간을 찾아서 거기에 충돌하는 데이터를 저장하자는 기법인 클로즈 해싱이라는 기법이 있다.

#### 1. Chaining 기법
- **개방 해슁 또는 Open Hashing 기법** 중 하나: 해쉬 테이블 저장공간 외의 공간을 활용하는 기법
- 충돌이 일어나면, 링크드 리스트라는 자료 구조를 사용해서, 링크드 리스트로 데이터를 추가로 뒤에 연결시켜서 저장하는 기법

<div class="alert alert-block alert-warning">
<strong><font color="blue" size="3em">연습문제 : 해쉬 테이블 코드에 Chaining 기법으로 충돌해결 코드를 추가해보기</font></strong><br>
1. 해쉬 함수: key % 8<br>
2. 해쉬 키 생성: hash(data)
</div>


![1](https://user-images.githubusercontent.com/41605276/105577297-fe33b080-5dbb-11eb-86bb-06bf7f932880.png)

위에 그림과 같이 해시 키값이 같은 경우의 데이터는 링크드 리스트로 엮은 다음에 데이터에 인덱스키(아래 index_key라는 변수를 만들어서 이용하였음)를 추가해줘서 어떤 키의 value인지 구분을 해주는 방식이다.


```python
hash_table = list([0 for i in range(8)])

def get_key(data):
    return hash(data)

def hash_function(key):
    return key % 8

def save_data(data, value):
    index_key = get_key(data)
    hash_address = hash_function(index_key)
    
    # 위에 hash_table 변수에 정의한 것 처럼 value 값이 없는 경우를 0으로 하고
    # 데이터가 0이 아니면 데이터가 존재하는 것을 가정하였다.
    # 따라서 아래와 같이 hash_table[hash_address] != 0 라는 말은 데이터가 들어가 있다는 의미이다.
    if hash_table[hash_address] != 0:       
        # python의 list를 링크드 리스트라고 가정했다.
        for index in range(len(hash_table[hash_address])):
            if hash_table[hash_address][index][0] == index_key:
                hash_table[hash_address][index][1] = value
                return
        hash_table[hash_address].append([index_key, value])
    else:
        hash_table[hash_address] = [[index_key, value]]
    
def read_data(data):
    index_key = get_key(data)
    hash_address = hash_function(index_key)

    if hash_table[hash_address] != 0:
        for index in range(len(hash_table[hash_address])):
            if hash_table[hash_address][index][0] == index_key:
                return hash_table[hash_address][index][1]
        return None
    else:
        return None
    
print (hash('Dave') % 8)
print (hash('Dd') % 8)
print (hash('Data') % 8)

save_data('Dd', '1201023010')
save_data('Data', '3301023010')
print(read_data('Dd'))

print(hash_table)
```

    4
    7
    7
    1201023010
    [0, 0, 0, 0, 0, 0, 0, [[-608492703038075529, '1201023010'], [3496885993367740167, '3301023010']]]
    

#### 2. Linear Probing 기법
- **폐쇄 해슁 또는 Close Hashing 기법** 중 하나: 해쉬 테이블 저장공간 안에서 충돌 문제를 해결하는 기법
- 충돌이 일어나면, 해당 hash address의 다음 address부터 맨 처음 나오는 빈공간에 저장하는 기법
  - 저장공간 활용도를 높이기 위한 기법
  
<div class="alert alert-block alert-warning">
<strong><font color="blue" size="3em">연습문제 : 해쉬 테이블 코드에 Linear Probling 기법으로 충돌해결 코드를 추가해보기</font></strong><br>
1. 해쉬 함수: key % 8<br>
2. 해쉬 키 생성: hash(data)
</div>

![2](https://user-images.githubusercontent.com/41605276/105577670-66839180-5dbe-11eb-96bb-1aa366d01d8a.PNG)

아래 코드에 index_key와 같이 변수를 따로 잡은 이유는 위에 그림과 같이 다른 칸에 충돌된 데이터를 저장할 경우 특정 주소를 해시 어드레스로 얻었을때 해당 칸에 있는 데이터가 내가 진짜 원하는 데이터 인지 아니면 다른 칸에서 충돌이 되어서 옮겨온 데이터 인것인지 알 수 없기 때문에 키랑 같이 저장해서 데이터를 읽거나 저장할때 해당주소에 저장되어 있는 데이터가 내가 원하는 데이터인지 식별을 하는 것이다.


```python
hash_table = list([0 for i in range(8)])

def get_key(data):
    return hash(data)

def hash_function(key):
    return key % 8

def save_data(data, value):
    index_key = get_key(data)
    hash_address = hash_function(index_key)
    if hash_table[hash_address] != 0:
        # 이미 해시 어드레스에 데이터가 저장이 되어 있다는 의미
        # 그래서 거기에 저장된 데이터가 내가 원하는 데이터 인지 확인하는 과정이다.
        for index in range(hash_address, len(hash_table)):
            if hash_table[index] == 0:
                hash_table[index] = [index_key, value]
                return
            elif hash_table[index][0] == index_key: # 기존의 값을 업데이트
                hash_table[index][1] = value
                return
    else:
        hash_table[hash_address] = [index_key, value]

def read_data(data):
    index_key = get_key(data)
    hash_address = hash_function(index_key)
    
    if hash_table[hash_address] != 0:
        for index in range(hash_address, len(hash_table)):
            if hash_table[index] == 0:
                return None
            elif hash_table[index][0] == index_key:
                return hash_table[index][1]
    else:
        return None

    
print (hash('db') % 8)
print (hash('da') % 8)
print (hash('dc') % 8)

save_data('db', '01200123123')
save_data('da', '3333333333')
save_data('dc', '1238123991')
print(read_data('db'))
print(read_data('dc'))
```

    4
    1
    4
    01200123123
    1238123991
    

#### 3. 빈번한 충돌을 개선하는 기법

해시테이블 구현의 핵심은 어떻게 하면 충돌을 최소화 할 수 있을까이다.

그러면 이런 우려를 어떻게 하면 해결할 수 있을까

--> 해쉬 함수을 재정의하거나 해쉬 테이블의 공간을 데이터의 두배 이상으로 저장공간을 확대한다.


- 예:내가 가지고 있는 데이터가 8개라고 하면 아래와 같이 개선을 할 수 있다.

```python
hash_table = list([None for i in range(16)])

def hash_function(key):
    return key % 16
```

#### 참고: 해쉬 함수와 키 생성 함수
- 파이썬의 hash() 함수는 실행할 때마다, 값이 달라질 수 있기 때문에 실제 현업에 적용하기에는 적합하지 않다.
- 유명한 해쉬 함수를 사용하면 된다. 예를 들어서 SHA(Secure Hash Algorithm, 안전한 해시 알고리즘)
  - 어떤 데이터도 유일한 고정된 크기의 고정값을 리턴해주므로, 해쉬 함수로 유용하게 활용 가능
  
#### SHA-1


```python
import hashlib

hash_object = hashlib.sha1()
hash_object.update(b'test')
hex_dig = hash_object.hexdigest()
print (hex_dig)
```

    a94a8fe5ccb19ba61c4c0873d391e987982fbbd3
    

또는 아래와 같이 구현하면 된다.


```python
import hashlib

data = 'test'.encode()
hash_object = hashlib.sha1()
hash_object.update(data)
hex_dig = hash_object.hexdigest()
print (hex_dig)
```

    a94a8fe5ccb19ba61c4c0873d391e987982fbbd3
    

#### SHA-256


```python
import hashlib

data = 'test'.encode()
hash_object = hashlib.sha256()
hash_object.update(data)
hex_dig = hash_object.hexdigest()
print (hex_dig)
```

    9f86d081884c7d659a2feaa0c55ad015a3bf4f1b2b0b822cd15d6c15b0f00a08
    

<div class="alert alert-block alert-warning">
<strong><font color="blue" size="3em">연습문제 : Chaining 기법을 적용한 해쉬 테이블 코드에 키 생성 함수를 sha256 해쉬 알고리즘을 사용하도록 변경해보기</font></strong><br>
1. 해쉬 함수: key % 8<br>
2. 해쉬 키 생성: hash(data)
</div>


```python
import hashlib

hash_table = list([0 for i in range(8)])

def get_key(data):
    hash_object = hashlib.sha256()
    hash_object.update(data.encode())
    hex_dig = hash_object.hexdigest()
    return int(hex_dig, 16) 
    # 숫자로 표현하기 위해 16진수의 문자열을 10진수 정수로 만들기 위해 위와 같이 작성함

def hash_function(key):
    return key % 8

def save_data(data, value):
    index_key = get_key(data)
    hash_address = hash_function(index_key)
    if hash_table[hash_address] != 0:
        for index in range(hash_address, len(hash_table)):
            if hash_table[index] == 0:
                hash_table[index] = [index_key, value]
                return
            elif hash_table[index][0] == index_key:
                hash_table[index][1] = value
                return
    else:
        hash_table[hash_address] = [index_key, value]

def read_data(data):
    index_key = get_key(data)
    hash_address = hash_function(index_key)
    
    if hash_table[hash_address] != 0:
        for index in range(hash_address, len(hash_table)):
            if hash_table[index] == 0:
                return None
            elif hash_table[index][0] == index_key:
                return hash_table[index][1]
    else:
        return None

print (get_key('db') % 8)
print (get_key('da') % 8)
print (get_key('dh') % 8)

save_data('da', '01200123123')
save_data('dh', '3333333333')
print(read_data('da'))
print(read_data('dh'))
```

    1
    2
    2
    01200123123
    3333333333
    

#### 해시의 시간 복잡도
- 일반적인 경우(Collision이 없는 경우)는 O(1)
- 최악의 경우(Collision이 모두 발생하는 경우)는 O(n) --> 모든 데이터의 어드레스가 충돌나서 모든 데이터가 링크드 리스트로 체이닝 걸려있을 경우

> 해쉬 테이블의 경우, 일반적인 경우를 기대하고 만들기 때문에, 시간 복잡도는 O(1) 이라고 말할 수 있음

#### 검색에서 해쉬 테이블의 사용 예
- 16개의 배열에 데이터를 저장하고, 검색할 때 O(n)
- 16개의 데이터 저장공간을 가진 위의 해쉬 테이블에 데이터를 저장하고, 검색할 때 O(1)
