---
layout: post
title: "Titanic호 생존자 예측을 위한 분류모델 구현"
tags: [Classification, 개인프로젝트]
comments: true
---

Classification 개인프로젝트 결과

```python
# 파이썬 코드작성을 위한 패키지 import 하는 명령어로 필히 실행바랍니다.

%matplotlib inline
%config InlineBackend.figure_formats = {'png', 'retina'}
from sklearn.preprocessing import LabelEncoder
from sklearn.model_selection import cross_val_score
from sklearn.ensemble import RandomForestClassifier
import numpy as np
import matplotlib as mpl
import matplotlib.pyplot as plt
import pandas as pd
from patsy import *
from sklearn.model_selection import KFold
from sklearn.datasets import make_classification
from sklearn.ensemble import ExtraTreesClassifier
from sklearn.metrics import confusion_matrix
from sklearn.metrics import classification_report
```

#  분류프로젝트 수행결과

> Titanic호 생존자 예측을 위한 분류모델 구현

## [  개 요 ]
   
#### ㅇ 컨텐츠 : Titanic: Machine Learning from Disaster
   * 컨텐츠 URL : https://www.kaggle.com/c/titanic
        
#### ㅇ 목적 : Titanic호 탑승자 명단 데이터로부터 특정 탑승자의 생존여부 예측(분류)
#### ㅇ Dataset 구성 :

    1) CSV 파일구성 : 학습을 위한 train.csv 와 모델 TEST를 위한 test.csv

    2) column : 'Pclass','Name','Sex' 등 12개
    
    3) row : 891개 (test.csv : 418개)

## [ 목 차 ]

### * 모델 구현과정에 대한 단계별 순차적인 리뷰

### 1. '데이터 탐색 및 전처리' 결과

### 2.  '타이타닉호 생존자 예측 분류모델' 구현결과

----------------------------------------------------

## [  단계별 수행결과   ]

## 1. '데이터 탐색 및  전처리' 결과

#### 1) 칼럼별 설명요약

- Survived : 생존여부(1: 생존, 0 : 사망) 
    * 예측을 요하는 종속변수


- PassengerId : 승객 번호
    * 데이터 구분을 위한 고유 ID값
    
    
- Ticket : 티켓의 고유넘버
    * 'PassengerId'와 유사한 형태로 의미있는 정보 도출불가
    

- Pclass : 승선권 클래스(1 : 1st, 2 : 2nd ,3 : 3rd)

- Name : 승객 이름

- Sex : 승객 성별

- Age : 승객 나이 

- SibSp : 동반한 형제자매, 배우자 수

- Patch : 동반한 부모, 자식 수

- Fare : 티켓요금

- Cabin : 객실 번호

- Embarked : 승선한 항구이름(C : Cherbourg, Q : Queenstown, S : Southampton)

#### 2) 데이터 형태에 따른 칼럼분류

- 실수형 칼럼 : Age, Fare
- 범주형 칼럼 : Sex, SibSp, Patch, Embarked
- 문자형 칼럼 : Name, Ticket, Cabin


```python
## train 데이터 로드
train = pd.read_csv('train.csv')
test = pd.read_csv('test.csv')

train.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>PassengerId</th>
      <th>Survived</th>
      <th>Pclass</th>
      <th>Name</th>
      <th>Sex</th>
      <th>Age</th>
      <th>SibSp</th>
      <th>Parch</th>
      <th>Ticket</th>
      <th>Fare</th>
      <th>Cabin</th>
      <th>Embarked</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>0</td>
      <td>3</td>
      <td>Braund, Mr. Owen Harris</td>
      <td>male</td>
      <td>22.0</td>
      <td>1</td>
      <td>0</td>
      <td>A/5 21171</td>
      <td>7.2500</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>1</td>
      <td>1</td>
      <td>Cumings, Mrs. John Bradley (Florence Briggs Th...</td>
      <td>female</td>
      <td>38.0</td>
      <td>1</td>
      <td>0</td>
      <td>PC 17599</td>
      <td>71.2833</td>
      <td>C85</td>
      <td>C</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>1</td>
      <td>3</td>
      <td>Heikkinen, Miss. Laina</td>
      <td>female</td>
      <td>26.0</td>
      <td>0</td>
      <td>0</td>
      <td>STON/O2. 3101282</td>
      <td>7.9250</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>1</td>
      <td>1</td>
      <td>Futrelle, Mrs. Jacques Heath (Lily May Peel)</td>
      <td>female</td>
      <td>35.0</td>
      <td>1</td>
      <td>0</td>
      <td>113803</td>
      <td>53.1000</td>
      <td>C123</td>
      <td>S</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>0</td>
      <td>3</td>
      <td>Allen, Mr. William Henry</td>
      <td>male</td>
      <td>35.0</td>
      <td>0</td>
      <td>0</td>
      <td>373450</td>
      <td>8.0500</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
  </tbody>
</table>
</div>



#### 3) 데이터 전처리 수행 간 이슈

#### point 1) 결측값은 어떻게 처리할 것인가

- 결측값 변수 변황 : 'Age','Cabin','Embarked'


```python
missing_df = train.isnull().sum().reset_index()
missing_df.columns = ['column', 'count']
missing_df['ratio'] = missing_df['count'] / train.shape[0]
missing_df.loc[missing_df['ratio'] != 0]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>column</th>
      <th>count</th>
      <th>ratio</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>5</th>
      <td>Age</td>
      <td>177</td>
      <td>0.198653</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Cabin</td>
      <td>687</td>
      <td>0.771044</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Embarked</td>
      <td>2</td>
      <td>0.002245</td>
    </tr>
  </tbody>
</table>
</div>



#### -  결측 컬럼별 처리방안

1) Age : 나이에 따른 생존 여부와 상관관계가 있을 것으로 예상되고, 결측값의 비중이 절대적으로 크지 않다고 판단하여 결측값을 '나이미상'이라는 데이터값으로 대체

2) Cabin : 77% 이상의 다수결측, 추가적으로 유추해낼 수 있는 정보가 없다고 판단, 해당 변수는 모델구성에 미반영

3) Embarked : 2건의 결측값에 대해 최빈값으로 대체

- 'Age' 결측값 처리

결측값에 대해 '-1'로 대체처리 -> 추후 '나이미상'이라는 라벨링 지정


```python
train = train.fillna({'Age': -1})
test = test.fillna({'Age': -1})
```

- 'Embarked' 결측값 처리

최빈값 'Southampton(S)'로 대체


```python
train = train.fillna({'Embarked': 'S'})
```


```python
## 테스트 데이터에 대한 결측값 대체 동시수행
## Fare 칼럼 평균값으로 대체
test = test.fillna({'Fare': 10})
```

#### point 2) 데이터 전처리를 어떻게 수행할 것인가

- 'Age'

이슈 : 데이터 중 소수점 단위의 나이데이터 식별

예시)

![1](https://user-images.githubusercontent.com/41605276/51813701-00f98900-22fb-11e9-9e7a-ad6446521fc7.png)

방안 : 나이대별 구간지정 및 라벨링 조치


```python
## age_section : 나이구간 지정 
## -2 ~ 0 : 나이미상, 0 ~ 9 : 유아, 10 ~ 19 : 청소년, 20 ~ 39 : 청년
## 40 ~ 59 : 장년, 60이상 : 노인
age_section = [-2, 0, 9, 19, 39, 59, np.inf]

## age_label : 나이구간별 라벨링
## 0 : 나이미상, 1 : 유아, 2 : 청소년, 3 : 청년, 4 : 장년, 5 : 노인
age_label = [0, 1, 2, 3, 4, 5]
train['Age']=pd.cut(train['Age'], age_section, labels=age_label)
test['Age']=pd.cut(test['Age'], age_section, labels=age_label)
```

- 'Sex'

이슈 : 데이터가 문자형으로 모델에 그대로 반영 시 오류가 발생함

방안 : 문자형 데이터 -> 숫자 라벨링


```python
## Sex 데이터 라벨링 지정
## male : 1, female : 0

def make_bin(data):
    if data == 'male':
        return 1
    else:
        return 0

train['Sex'] = train['Sex'].apply(make_bin)
test['Sex'] = test['Sex'].apply(make_bin)
```

- 'Name'

이슈 : 데이터로부터 의미있는 정보를 추출해야함

방안 : 이름 앞의 수식어를 추출하여 직업, 성별, 귀족여부 등의 정보획득


```python
## 'Name'데이터의 가장 앞의 수식어를 추출

train['Name'] = train['Name'].map(lambda name: name.split(',')[1].split('.')[0].strip())
test['Name'] = test['Name'].map(lambda name: name.split(',')[1].split('.')[0].strip())

titles = train['Name'].unique()
titles
```




    array(['Mr', 'Mrs', 'Miss', 'Master', 'Don', 'Rev', 'Dr', 'Mme', 'Ms',
           'Major', 'Lady', 'Sir', 'Mlle', 'Col', 'Capt', 'the Countess',
           'Jonkheer'], dtype=object)




```python
## 추출한 데이터를 바탕으로 직업, 성별, 귀족여부 분리 및 숫자 라벨링

def make_cate2(name):
    
    royal_list = ['Sir','Major','lady','the Countess','Jonkheer']
    man = ['Mr', 'Don']
    female = ['Mrs','Miss','Mme','Ms']
    
    if name in royal_list:
        return 1
    elif name in man:
        return 2
    elif name in female:
        return 3
    elif name == 'Master':
        return 4
    elif name == 'Rev':
        return 5
    elif name == 'Dr':
        return 6
    elif name == 'Mlle':
        return 7
    elif name == 'Col':
        return 8
    elif name == 'Capt':
        return 9
    else :
        return 10
    

train['Name'] = train['Name'].apply(make_cate2)
test['Name'] = test['Name'].apply(make_cate2)
```

- 'Embarked'

이슈 : 데이터가 문자형으로 모델에 그대로 반영 시 오류가 발생함

방안 : 문자형 데이터 -> 숫자 라벨링


```python
## Embarked 데이터 라벨링 지정
## Southampton : 1, Cherbourg : 2 , Queenstown : 3

def make_cate(data):
    if data == 'S':
        return 1
    elif data == 'C':
        return 2
    else:
        return 3

train['Embarked'] = train['Embarked'].apply(make_cate)
test['Embarked'] = test['Embarked'].apply(make_cate)
```

### V 소결론 : 모델의 신뢰성과 성능 보장을 위한 사전작업 수행

## 2. '타이타닉 생존자 예측 분류모델' 구현결과

### [ 모델구현 과정 간 이슈 ]

#### point 1) 어떤 모델을 선택할 것인가
- 분류의 세분화가 가능하고 안정성이 높은 'Random Forest' 선정

#### point 2) 최상의 예측 Perfomance 구현을 위해 어떻게 할 것인가
- 'random forest' 인자인 'max_depth' 및 'n_estimators' 조절로 성능 극대화 모색
- '독립변수 구성 재검토 <-> 모델구성' 반복시행으로 모델의 performance 향상시도
- 'Kfold' 검증수행 및 kaggle contest summit으로 실질적인 모델의 performance 확인

### [ 모델 구현과정 ] 
-> 종속변수 survived(생존여부)에 대하여 'Random Forest' 모델 구현

#### 1) 모델구성 :  'Pclass' 등 8개의 독립변수 조합

- 모델구성에 제외한 변수 : 'survived', 'PassengerId', 'Cabin', 'Ticket'


```python
train_target = train['Survived']
train_data = train[['Pclass','Sex','Age','Embarked','Fare','Parch','SibSp','Name']]

model = RandomForestClassifier(max_depth=7, n_estimators=140,\
                               random_state=0).fit(train_data, train_target)
```

#### 2) 모델의 성능 확인 및 '예측 Performance' 극대화 모색

#### [ 트레이닝 데이터에 대한 prediction 성능확인 ]


```python
prediction = model.predict(train_data)

print("[ confusion maxtrix ] \n")
print(confusion_matrix(train_target, prediction))
print("\n ******************************************* \n")
print('\t \t   [ classification report ] \n')
print(classification_report(train_target, prediction))
```

    [ confusion maxtrix ] 
    
    [[520  29]
     [ 81 261]]
    
     ******************************************* 
    
    	 	   [ classification report ] 
    
                  precision    recall  f1-score   support
    
               0       0.87      0.95      0.90       549
               1       0.90      0.76      0.83       342
    
       micro avg       0.88      0.88      0.88       891
       macro avg       0.88      0.86      0.87       891
    weighted avg       0.88      0.88      0.87       891
    
    

#### [ K-fold 수행 및 kaggle contest summit으로 실질적인 prediction 성능 확인 ]

- k_fold 수행(n_splits = 10)


```python
k_fold = KFold(n_splits = 10, shuffle = True, random_state=0)

model = RandomForestClassifier(max_depth=7, n_estimators=140, random_state=0).fit(train_data, train_target)
score = cross_val_score(model, train_data, train_target, cv=k_fold, n_jobs=1, scoring = 'accuracy')
round(np.mean(score*100),2)
```




    83.5




```python
score
```




    array([0.85555556, 0.82022472, 0.83146067, 0.84269663, 0.85393258,
           0.83146067, 0.84269663, 0.84269663, 0.78651685, 0.84269663])



- kaggle contest summit score

-> 10562rank 중 3524 rank 기록

![2](https://user-images.githubusercontent.com/41605276/51813712-12429580-22fb-11e9-8bea-dff95a829fcf.png)

#### [ 모형 인자인 'max_depth' 및 'n_estimators' 조절로 모델의 성능 극대화 모색 ]

-> 각각의 인자가 변경됨으로써 얻어지는 'kfold score'를 비교

결론 : max_depth는 7, n_estimators는 140에서 모델성능이 극대화


- max_depth 조절


```python
k_fold = KFold(n_splits = 10, shuffle = True, random_state=0)
score_list = []

for trying in range(1, 15+1):
    model = RandomForestClassifier(max_depth=trying, n_estimators=140, random_state=0).fit(train_data, train_target)
    score = cross_val_score(model, train_data, train_target, cv=k_fold, n_jobs=1, scoring = 'accuracy')
    score_list.append(round(np.mean(score*100),2))
```


```python
plt.plot(np.arange(1,15+1), score_list)
plt.ylabel('mean_score')
plt.xlabel('max_depth')
plt.show()
```


<img width="389" alt="classification_personal_44_0" src="https://user-images.githubusercontent.com/41605276/51813734-2d150a00-22fb-11e9-8d15-aefe6c13ca67.png">


- n_estimators 조절
    - 파이썬 코드 실행 시간이 상당히 소요되므로 결과값만 전시, 실제 코드실행 생략

![3](https://user-images.githubusercontent.com/41605276/51813738-37370880-22fb-11e9-8567-30ad2595a72a.png)

#### [ 특성중요도 확인 ]

-> 노드별 information gain평균 산출 및 비교

결론 : 'name','sex','fare' 순으로 모델에 대한 영향력을 갖는것으로 확인


```python
forest = RandomForestClassifier(max_depth=7, n_estimators=140, random_state=0).fit(train_data, train_target)

importances = forest.feature_importances_

std = np.std([tree.feature_importances_ for tree in forest.estimators_], axis=0)
indices = np.argsort(importances)[::-1]

plt.bar(range(train_data.shape[1]), importances[indices],
        color="r", yerr=std[indices], align="center")
plt.xticks(range(train_data.shape[1]), train_data.columns[indices])
plt.xlim([-1, train_data.shape[1]])
plt.ylabel('average of infomation gain')
plt.xlabel('category')
plt.show()
```


<img width="392" alt="classification_personal_48_0" src="https://user-images.githubusercontent.com/41605276/51813746-41f19d80-22fb-11e9-9d17-3977500ac195.png">


### V 결론 : 생존여부를 확률높게 예측할 수 있는 분류모델 구현
