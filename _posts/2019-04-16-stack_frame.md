---
layout: post
title: "스텍프레임 기초개념"
tags: [Python]
comments: true
---

.

#### 그림, 실습코드 등 학습자료 출처 : https://github.com/ythwork

예를 들어 아래와 같은 파이썬 코드가 있다고 하자.


```python
a=10

def func(b,c):
    d=b+c
    e=b-c
    return d, e

func(20, 30)
```




    (50, -10)



- 우리가 함수를 실행하는 순간 저장해야할 공간이 필요하다. a,b,c,d를 저장하는 공간이 필요하다. 메모리에서는 아래와 같이 스택프레임이라는 저장공간을 확보한다. 다시말해 함수가 실행할때 메모리에서 연산을 위해 변수만큼 저장공간(스텍프레임)을 만든다. 함수가 종료될 때 메모리 할당을 해제한다. 한마디로 함수를 실행될때 메모리 내부에서 함수 실행에 필요한 변수를 저장해두는 공간을 스텍프레임이라고 한다.


- 관련개념 학습 URL : https://minman2115.github.io/Python_TIL2


- 프레임 : 함수를 실행하기 위해 변수들을 모아놓은 공간


- 스텍 : 급식판

step1) 최초에 글로벌 프레임이 만들어져서 메모리 가장 밑에 쌓이게 되고 거기에는 a=10과 func 함수에 대한 내용이 저장된다.

![1](https://user-images.githubusercontent.com/41605276/56191817-1499cd80-6068-11e9-81d8-d9cee2c777e2.png)

step2) func 함수를 호출하게 되는 순간(func(20,30)이 실행되는 순간) func에 대한 스텍프레임을 만들어져서 글로벌 프레임 위에 쌓이게 된다.

step3) func 스텍프레임 내에는 그 함수에 있는 변수(c,b,d,e 총 4개) 만큼 공간이 생긴다.

![2](https://user-images.githubusercontent.com/41605276/56191841-1bc0db80-6068-11e9-9983-f1b6c453240b.png)

step4) 그리고 func 실행시 입력값(20,30)에 따라 각각의 변수에 수들이 저장이 된다.

![3](https://user-images.githubusercontent.com/41605276/56191856-224f5300-6068-11e9-9124-33e2569a0835.png)

step5) 일들이 일어나는 동안 메모리에 프레임들이 쌓여있다가. func 함수가 실행이 완료되면서 return 값이 반환되는 순간 func 스텍프레임은 메모리 공간에서 사라지게 된다.

step6) 프로그램이 전부 실행이 완료되면 글로벌 프레임까지 메모리 공간에서 사라지게 된다.

![4](https://user-images.githubusercontent.com/41605276/56191874-2aa78e00-6068-11e9-87f7-f5782636dd38.png)

![5](https://user-images.githubusercontent.com/41605276/56191884-309d6f00-6068-11e9-8523-ade006057944.png)
