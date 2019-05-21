---
layout: post
title: "파이썬 기초문법을 이용한 각종 도형그리기 연습"
tags: [Python]
comments: true
---

.

#### 그림, 실습코드 등 학습자료 출처 : https://gitlab.com/radajin

for문과 class 문법을 이용한 각종 도형 그리기 연습


```python
class starmaker():
    
    def __init__(self,number):
        self.number = number
    
    def setData(self, number):
        self.number = number
        
    def type_1(self):
        for star in range(1, self.number + 1):
            print("*" * star)
    
    def type_2(self):
        space = self.number - 1
        for star in range(1, self.number + 1):
            print(" " * space + "*" * star)
            space -= 1
    
    def type_3(self):
        space = self.number // 2
        for star in range(1, self.number+1, 2):
            print(" " * space + "*" * star)
            space -= 1
    
    def type_4(self):
        space = 0
        for star in range(self.number, 0 , -2):
            print(" " * space + "*" * star)
            space += 1
            
    def type_5(self):
        space = self.number // 2
        for star in range(1, self.number+1, 2):
            print(" " * space + "*" * star)
            space -= 1
    
        space = 1
        for star in range(self.number-2, 0 , -2):
            print(" " * space + "*" * star)
            space += 1
            
making = starmaker(5)

making.type_1()
print()
making.type_2()
print()
making.type_3()
print()
making.type_4()
print()
making.type_5()
```

    *
    **
    ***
    ****
    *****
    
        *
       **
      ***
     ****
    *****
    
      *
     ***
    *****
    
    *****
     ***
      *
    
      *
     ***
    *****
     ***
      *
    
