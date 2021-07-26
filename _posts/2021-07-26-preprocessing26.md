---
layout: post
title: "데이터 전처리시 범주형 데이터 처리 방안"
tags: [데이터전처리]
comments: true
---

.

Data_Preprocessing_TIL(20210726)

[학습자료]

패스트캠퍼스 온라인 강의 "파이썬을 활용한 데이터 전처리 Level UP 올인원 패키지 Online." 를 공부하고 정리한 내용입니다.

URL : https://fastcampus.co.kr/data_online_preprocess

[학습내용]

- 데이터에 범주형 데이터가 존재할때 문제점

데이터에 범주형 변수가 포함될 경우 대다수의 지도학습 모델이 학습되지 않거나 비정상적으로 학습이 됨

string 타입의 범주형 변수가 포함되면 대다수의 지도학습 모델 자체가 학습이 안됨

int 혹은 float 타입의 범주형 변수 역시 모델학습이 가능하나, 비정상적으로 학습이 될 가능성이 높아서 이렇게 하면 안됨

모델학습을 위해 범주형 변수는 반드시 숫자로 변환되어야 하지만, 임의로 설정하는 것은 매우 부적절하다.

예시) 종교변수 : 기독교 = 1, 불교 = 2, 천주교 = 3

불교는 기독교의 2배라는 등의 대수관계가 실제로는 존재하지 않는데, 이런식으로 무작정 기독교 = 1, 불교 = 2, 천주교 = 3으로 변환해버리면 변수들간에 비정상적인 관계가 생겨버림

코드화된 범주형 변수도 적절한 숫자로 변환해줘야 함

- 어떤거를 범주형 변수라고 하는가

범주형 변수는 상태공간의 크기가 유한한 변수를 의미하며, 반드시 도메인이나 변수의 상태 공간을 바탕으로 판단해야 함

int 혹은 float 타입으로 정의된 변수는 반드시 연속형 변수가 아닐 수 있다는 점에 주의해야함

예를 들어서 월(month)은 숫자로 되어 있지만 범주형 변수이다

시간(hour) 역시 숫자로 둔갑된 대표적인 범주형 변수이다.

- 범주형 변수 변환방안 1. 더미화

가장 일반적인 범주형 변수를 변환하는 방법으로, 범주형 변수가 특정 값을 취하는지 여부를 나타내는 더미변수를 생성하는 방법이 있다.

아래 그림과 같이 더미변수를 만들고 불교 변수는 없어도 된다.

더미형 변수를 변환할때 변수의 종류가 많으면 컬럼 갯수도 많아지고 희소한 컬럼도 생기기 때문에 결론적으로 변수의 종류가 많은 것은 더미화를 하는게 적합하지 않다.

![1](https://user-images.githubusercontent.com/41605276/126986188-27aa78a9-571b-4f6e-a55b-bae6f2c64157.png)

- 범주형 변수 변환방안 2. 연속형 변수로 치환

범주형 변수의 상태 공간 크기가 클때, 더미화는 과하게 많은 변수를 추가해서 차원의 저주문제로 이어질 수 있다.

라벨 정보를 활용해서 범주 변수를 연속형 변수로 치환하면 기존변수가 가지는 정보가 일부 손실될 수 있고 활용이 어렵다는 단점이 있으나, 차원의 크기가 변하지 않으며 더 효율적인 변수로 변환할 수 있다는 장점이 있다.

예를 들어서 X라는 범주형 변수가 있고, Y라는 연속형 변수가 혼재되어 있는 데이터 프레임이 있으면 아래 그림과 같은 방법으로 처리할 수 있다.

이방법을 쓸때 장점은 지도학습의 경우 모델을 만들때 더미형 변수로 변환하는 것과 다르게 차원이 늘지 않는다. 그리고 범주형 변수정보를 모델에 반영할 수 있기 때문에 모델의 설명력을 높일 수 있다. 단점은 온전한 기존정보가 손실이 어느정도 일어날 것이다. 아래 그림을 예시로 하면 만약에 A가 200, B도 200이라면 A와 B가 같은 값으로 인식하게 되어 정보 왜곡이 일어날 수도 있다.

![2](https://user-images.githubusercontent.com/41605276/126986412-d87de4df-16fd-4133-a986-d9f21408fcca.PNG)

결론적으로 상태공간의 크기가 작으면 더미형 변수로 변환하고, 상태공간의 크기가 크면 연속형 변수로 치환하는 방법이 좋다.

- 범주형 변수처리를 위해 자주 사용하는 문법 : Series.unique()

Series에 포함된 유니크한 값을 변환해주는 함수로, 상태공간을 확인하는데 사용함

![3](https://user-images.githubusercontent.com/41605276/126986645-ec078c97-ba53-45ea-84e4-3b63b402044c.PNG)

- 범주형 변수처리를 위해 자주 사용하는 문법 : feature_engine.categorical_encoders.OneHotCategoricalEncoder

더미화를 하기 위한 함수로, 활용방법은 sklearn의 인스턴스의 활용방법과 유사함

주요입력

1) variables : 더미화 대상이 되는 범주형 변수의 이름 목록(주의사항 : 해당변수는 반드시 string type이어야 함)

2) drop_last : 한 범주 변수로부터 만드는 더미 변수 가운데 마지막 더미 변수를 제거할 지를 결정

3) top_categories : 한 범주 변수로부터 만드는 더미 변수 개수를 설정하며, 빈도 기준으로 자름

참고사항 : pandas.get_dummies()는 이 함수보다 사용이 훨씬 간단하지만, 학습 데이터에 포함된 범주형 변수를 처리한 방식으로 새로 들어온 데이터에 적용이 불가능하기 때문에, 실제적으로 활용이 어려움

- 실습


```python
import os
import pandas as pd

os.chdir(r"C:/Users/user/Desktop/aa/part-4.-머신러닝을-위한-필수-전처리/Part 4. 머신러닝을 위한 필수 전처리/데이터/")

df = pd.read_csv("car-good.csv")
df
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
      <th>Buying</th>
      <th>Maint</th>
      <th>Doors</th>
      <th>Persons</th>
      <th>Lug_boot</th>
      <th>Safety</th>
      <th>Class</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>vhigh</td>
      <td>vhigh</td>
      <td>2</td>
      <td>2</td>
      <td>small</td>
      <td>low</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>1</th>
      <td>vhigh</td>
      <td>vhigh</td>
      <td>2</td>
      <td>2</td>
      <td>small</td>
      <td>med</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>2</th>
      <td>vhigh</td>
      <td>vhigh</td>
      <td>2</td>
      <td>2</td>
      <td>small</td>
      <td>high</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>3</th>
      <td>vhigh</td>
      <td>vhigh</td>
      <td>2</td>
      <td>2</td>
      <td>med</td>
      <td>low</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>4</th>
      <td>vhigh</td>
      <td>vhigh</td>
      <td>2</td>
      <td>2</td>
      <td>med</td>
      <td>med</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>859</th>
      <td>low</td>
      <td>low</td>
      <td>4</td>
      <td>4</td>
      <td>med</td>
      <td>med</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>860</th>
      <td>low</td>
      <td>low</td>
      <td>4</td>
      <td>4</td>
      <td>med</td>
      <td>high</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>861</th>
      <td>low</td>
      <td>low</td>
      <td>4</td>
      <td>4</td>
      <td>big</td>
      <td>low</td>
      <td>negative</td>
    </tr>
    <tr>
      <th>862</th>
      <td>low</td>
      <td>low</td>
      <td>4</td>
      <td>4</td>
      <td>big</td>
      <td>med</td>
      <td>positive</td>
    </tr>
    <tr>
      <th>863</th>
      <td>low</td>
      <td>low</td>
      <td>4</td>
      <td>4</td>
      <td>big</td>
      <td>high</td>
      <td>negative</td>
    </tr>
  </tbody>
</table>
<p>864 rows × 7 columns</p>
</div>




```python
# 특징과 라벨 분리
X = df.drop('Class', axis = 1)
Y = df['Class']
```


```python
# 학습 데이터와 평가 데이터 분리
from sklearn.model_selection import train_test_split
Train_X, Test_X, Train_Y, Test_Y = train_test_split(X, Y)
```


```python
Train_Y.value_counts()
```




    negative    630
    positive     18
    Name: Class, dtype: int64




```python
# 문자 라벨을 숫자로 치환 
Train_Y.replace({"negative":-1, "positive":1}, inplace = True)
Test_Y.replace({"negative":-1, "positive":1}, inplace = True)
```


```python
# Buying, Maint, Lug_boot, safety 변수가 범주형 변수로 판단됨
Train_X.head() 
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
      <th>Buying</th>
      <th>Maint</th>
      <th>Doors</th>
      <th>Persons</th>
      <th>Lug_boot</th>
      <th>Safety</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>862</th>
      <td>low</td>
      <td>low</td>
      <td>4</td>
      <td>4</td>
      <td>big</td>
      <td>med</td>
    </tr>
    <tr>
      <th>632</th>
      <td>med</td>
      <td>low</td>
      <td>4</td>
      <td>2</td>
      <td>small</td>
      <td>high</td>
    </tr>
    <tr>
      <th>254</th>
      <td>high</td>
      <td>vhigh</td>
      <td>4</td>
      <td>2</td>
      <td>small</td>
      <td>high</td>
    </tr>
    <tr>
      <th>286</th>
      <td>high</td>
      <td>high</td>
      <td>2</td>
      <td>4</td>
      <td>big</td>
      <td>med</td>
    </tr>
    <tr>
      <th>86</th>
      <td>vhigh</td>
      <td>high</td>
      <td>3</td>
      <td>4</td>
      <td>med</td>
      <td>high</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 자세한 범주형 변수 판별 => 모든 변수가 범주형임을 확인
for col in Train_X.columns:
    print(col, len(Train_X[col].unique()))
```

    Buying 4
    Maint 4
    Doors 3
    Persons 2
    Lug_boot 3
    Safety 3
    

더미화를 이용한 범주 변수 처리


```python
# 모든 변수가 범주이므로, 더미화를 위해 전부 string 타입으로 변환
Train_X = Train_X.astype(str) 
```


```python
from feature_engine.encoding import OneHotEncoder as OHE

dummy_model = OHE(variables = Train_X.columns.tolist(),drop_last = True)

dummy_model.fit(Train_X)

d_Train_X = dummy_model.transform(Train_X)
d_Test_X = dummy_model.transform(Test_X)
```


```python
# 더미화를 한 뒤의 모델 테스트
from sklearn.neighbors import KNeighborsClassifier as KNN
model = KNN().fit(d_Train_X, Train_Y)
pred_Y = model.predict(d_Test_X)

from sklearn.metrics import f1_score
f1_score(Test_Y, pred_Y)
```




    0.0



연속형 변수로 치환하여 처리


```python
Train_df = pd.concat([Train_X, Train_Y], axis = 1)
Train_df
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
      <th>Buying</th>
      <th>Maint</th>
      <th>Doors</th>
      <th>Persons</th>
      <th>Lug_boot</th>
      <th>Safety</th>
      <th>Class</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>862</th>
      <td>low</td>
      <td>low</td>
      <td>4</td>
      <td>4</td>
      <td>big</td>
      <td>med</td>
      <td>1</td>
    </tr>
    <tr>
      <th>632</th>
      <td>med</td>
      <td>low</td>
      <td>4</td>
      <td>2</td>
      <td>small</td>
      <td>high</td>
      <td>-1</td>
    </tr>
    <tr>
      <th>254</th>
      <td>high</td>
      <td>vhigh</td>
      <td>4</td>
      <td>2</td>
      <td>small</td>
      <td>high</td>
      <td>-1</td>
    </tr>
    <tr>
      <th>286</th>
      <td>high</td>
      <td>high</td>
      <td>2</td>
      <td>4</td>
      <td>big</td>
      <td>med</td>
      <td>-1</td>
    </tr>
    <tr>
      <th>86</th>
      <td>vhigh</td>
      <td>high</td>
      <td>3</td>
      <td>4</td>
      <td>med</td>
      <td>high</td>
      <td>-1</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>256</th>
      <td>high</td>
      <td>vhigh</td>
      <td>4</td>
      <td>2</td>
      <td>med</td>
      <td>med</td>
      <td>-1</td>
    </tr>
    <tr>
      <th>499</th>
      <td>med</td>
      <td>high</td>
      <td>2</td>
      <td>4</td>
      <td>med</td>
      <td>med</td>
      <td>-1</td>
    </tr>
    <tr>
      <th>853</th>
      <td>low</td>
      <td>low</td>
      <td>4</td>
      <td>2</td>
      <td>big</td>
      <td>med</td>
      <td>-1</td>
    </tr>
    <tr>
      <th>107</th>
      <td>vhigh</td>
      <td>high</td>
      <td>4</td>
      <td>4</td>
      <td>big</td>
      <td>high</td>
      <td>-1</td>
    </tr>
    <tr>
      <th>185</th>
      <td>vhigh</td>
      <td>low</td>
      <td>3</td>
      <td>2</td>
      <td>med</td>
      <td>high</td>
      <td>-1</td>
    </tr>
  </tbody>
</table>
<p>648 rows × 7 columns</p>
</div>




```python
# 보통은 범주 변수만 순회
for col in Train_X.columns: 
    
    # col에 따른 Class의 평균을 나타내는 사전 (replace를 쓰기 위해, 사전으로 만듦)
    temp_dict = Train_df.groupby(col)['Class'].mean().to_dict()
    print(temp_dict)
    
    print('\n','*'*100,'\n')
    
    # 변수 치환
    Train_df[col] = Train_df[col].replace(temp_dict)
    print(Train_df[col])
    
    print('\n','*'*100,'\n')
    
    # 테스트 데이터도 같이 치환해줘야 함 (나중에 활용하기 위해서는 저장도 필요)
    Test_X[col] = Test_X[col].astype(str).replace(temp_dict)
    print(Test_X[col])
    
    print('\n','*'*100,'\n')
```

    {'high': -1.0, 'low': -0.8509316770186336, 'med': -0.9254658385093167, 'vhigh': -1.0}
    
     **************************************************************************************************** 
    
    862   -0.850932
    632   -0.925466
    254   -1.000000
    286   -1.000000
    86    -1.000000
             ...   
    256   -1.000000
    499   -0.925466
    853   -0.850932
    107   -1.000000
    185   -1.000000
    Name: Buying, Length: 648, dtype: float64
    
     **************************************************************************************************** 
    
    212   -1.000000
    676   -0.850932
    10    -1.000000
    780   -0.850932
    168   -1.000000
             ...   
    716   -0.850932
    377   -1.000000
    531   -0.925466
    811   -0.850932
    329   -1.000000
    Name: Buying, Length: 216, dtype: float64
    
     **************************************************************************************************** 
    
    {'high': -1.0, 'low': -0.8658536585365854, 'med': -0.9090909090909091, 'vhigh': -1.0}
    
     **************************************************************************************************** 
    
    862   -0.865854
    632   -0.865854
    254   -1.000000
    286   -1.000000
    86    -1.000000
             ...   
    256   -1.000000
    499   -1.000000
    853   -0.865854
    107   -1.000000
    185   -0.865854
    Name: Maint, Length: 648, dtype: float64
    
     **************************************************************************************************** 
    
    212   -0.865854
    676   -1.000000
    10    -1.000000
    780   -0.909091
    168   -0.865854
             ...   
    716   -1.000000
    377   -0.909091
    531   -1.000000
    811   -0.865854
    329   -0.909091
    Name: Maint, Length: 216, dtype: float64
    
     **************************************************************************************************** 
    
    {'2': -0.9420289855072463, '3': -0.9282511210762332, '4': -0.963302752293578}
    
     **************************************************************************************************** 
    
    862   -0.963303
    632   -0.963303
    254   -0.963303
    286   -0.942029
    86    -0.928251
             ...   
    256   -0.963303
    499   -0.942029
    853   -0.963303
    107   -0.963303
    185   -0.928251
    Name: Doors, Length: 648, dtype: float64
    
     **************************************************************************************************** 
    
    212   -0.963303
    676   -0.928251
    10    -0.942029
    780   -0.928251
    168   -0.942029
             ...   
    716   -0.942029
    377   -0.963303
    531   -0.963303
    811   -0.942029
    329   -0.942029
    Name: Doors, Length: 216, dtype: float64
    
     **************************************************************************************************** 
    
    {'2': -1.0, '4': -0.8867924528301887}
    
     **************************************************************************************************** 
    
    862   -0.886792
    632   -1.000000
    254   -1.000000
    286   -0.886792
    86    -0.886792
             ...   
    256   -1.000000
    499   -0.886792
    853   -1.000000
    107   -0.886792
    185   -1.000000
    Name: Persons, Length: 648, dtype: float64
    
     **************************************************************************************************** 
    
    212   -0.886792
    676   -0.886792
    10    -0.886792
    780   -1.000000
    168   -1.000000
             ...   
    716   -0.886792
    377   -0.886792
    531   -0.886792
    811   -1.000000
    329   -1.000000
    Name: Persons, Length: 216, dtype: float64
    
     **************************************************************************************************** 
    
    {'big': -0.9444444444444444, 'med': -0.9351851851851852, 'small': -0.9537037037037037}
    
     **************************************************************************************************** 
    
    862   -0.944444
    632   -0.953704
    254   -0.953704
    286   -0.944444
    86    -0.935185
             ...   
    256   -0.935185
    499   -0.935185
    853   -0.944444
    107   -0.944444
    185   -0.935185
    Name: Lug_boot, Length: 648, dtype: float64
    
     **************************************************************************************************** 
    
    212   -0.935185
    676   -0.953704
    10    -0.953704
    780   -0.944444
    168   -0.944444
             ...   
    716   -0.935185
    377   -0.944444
    531   -0.953704
    811   -0.953704
    329   -0.935185
    Name: Lug_boot, Length: 216, dtype: float64
    
     **************************************************************************************************** 
    
    {'high': -0.8957345971563981, 'low': -1.0, 'med': -0.9383259911894273}
    
     **************************************************************************************************** 
    
    862   -0.938326
    632   -0.895735
    254   -0.895735
    286   -0.938326
    86    -0.895735
             ...   
    256   -0.938326
    499   -0.938326
    853   -0.938326
    107   -0.895735
    185   -0.895735
    Name: Safety, Length: 648, dtype: float64
    
     **************************************************************************************************** 
    
    212   -0.895735
    676   -0.938326
    10    -0.938326
    780   -1.000000
    168   -1.000000
             ...   
    716   -0.895735
    377   -0.895735
    531   -1.000000
    811   -0.938326
    329   -0.895735
    Name: Safety, Length: 216, dtype: float64
    
     **************************************************************************************************** 
    
    

    <ipython-input-12-cb132fae3c4b>:17: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      Test_X[col] = Test_X[col].astype(str).replace(temp_dict)
    


```python
Train_df.head()
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
      <th>Buying</th>
      <th>Maint</th>
      <th>Doors</th>
      <th>Persons</th>
      <th>Lug_boot</th>
      <th>Safety</th>
      <th>Class</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>862</th>
      <td>-0.850932</td>
      <td>-0.865854</td>
      <td>-0.963303</td>
      <td>-0.886792</td>
      <td>-0.944444</td>
      <td>-0.938326</td>
      <td>1</td>
    </tr>
    <tr>
      <th>632</th>
      <td>-0.925466</td>
      <td>-0.865854</td>
      <td>-0.963303</td>
      <td>-1.000000</td>
      <td>-0.953704</td>
      <td>-0.895735</td>
      <td>-1</td>
    </tr>
    <tr>
      <th>254</th>
      <td>-1.000000</td>
      <td>-1.000000</td>
      <td>-0.963303</td>
      <td>-1.000000</td>
      <td>-0.953704</td>
      <td>-0.895735</td>
      <td>-1</td>
    </tr>
    <tr>
      <th>286</th>
      <td>-1.000000</td>
      <td>-1.000000</td>
      <td>-0.942029</td>
      <td>-0.886792</td>
      <td>-0.944444</td>
      <td>-0.938326</td>
      <td>-1</td>
    </tr>
    <tr>
      <th>86</th>
      <td>-1.000000</td>
      <td>-1.000000</td>
      <td>-0.928251</td>
      <td>-0.886792</td>
      <td>-0.935185</td>
      <td>-0.895735</td>
      <td>-1</td>
    </tr>
  </tbody>
</table>
</div>




```python
Train_X = Train_df.drop('Class', axis = 1)
Train_Y = Train_df['Class']
```


```python
# 치환한 뒤의 모델 테스트
model = KNN().fit(Train_X, Train_Y)
pred_Y = model.predict(Test_X)

f1_score(Test_Y, pred_Y)

# 라벨을 고려한 전처리이므로 더미화보다 좋은 결과가 나왔음 => 차원도 줄고 성능 상에 이점이 있으나, 
```




    0.4444444444444444


