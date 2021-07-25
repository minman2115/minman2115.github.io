---
layout: post
title: "데이터 결측문제 - 결측치 예측모델을 활용해서 해결하는 경우"
tags: [데이터전처리]
comments: true
---

.

Data_Preprocessing_TIL(20210725)

[학습자료]

패스트캠퍼스 온라인 강의 "파이썬을 활용한 데이터 전처리 Level UP 올인원 패키지 Online." 를 공부하고 정리한 내용입니다.

URL : https://fastcampus.co.kr/data_online_preprocess

[학습내용]


- 결측치 예측모델이란

결측이 발생하지 않은 컬럼을 학습해서 결측치를 예측하는 모델을 말한다.

![1](https://user-images.githubusercontent.com/41605276/126889922-7b2db72a-1ec0-4314-b5e5-62f09c73eef3.png)

- 결측치 예측모델은 어떻게 활용하면 되는가

결측치 예측모델은 어떤 상황이던지 무난하게 사용이 가능하나 사용조건과 단점을 반드시 숙지하고 있어야 한다.



사용조건은 아래와 같다.

사용조건 1. 결측이 소수 컬럼에 쏠리면 안된다.

소수 컬럼의 결측 비율이 60프로 이상이면 쓰기 어렵다. 왜냐하면 라벨값 자체가 부족하기 때문에 일반화된 모델을 만들기 어렵기 때문이다.

그런데 또 이런 경우에는 사용이 가능하다. 소수컬럼에 결측이 쏠리더라도, 어떤 컬럼에 결측률이 90프로라고 하더라도 남은 10프로가 충분히 많은 경우에는 쓸 수 있다. 예측하고자 하는 컬럼에 포함된 결측치를 제거한 레코드의 갯수가 충분히 많으면 사용이 가능한 것이다.

사용조건 2. 피쳐간에 상관관계가 있어야 한다. 

단점은 다른 결측치 처리방법과 비교했을때 시간이 오래걸린다.


- 많이 쓰이는 함수 : sklearn.impute.KNNimputer

결측이 아닌값만 사용하여 이웃을 구한뒤, 이웃들의 값의 대표값으로 결측을 대체하는 결측치 예측모델

주요입력 

n_neighbors : 이웃수 (너무적으면 결측 대체가 정상적으로 이루어지지 않을 수 있으므로 5정도가 적절하다)

여기서 a,b,c의 이웃은 어떻게 구한거냐면 V2 컬럼값을 기준으로 거리행렬을 계산한 것이다.

![2](https://user-images.githubusercontent.com/41605276/126890021-7094aa1d-de93-40e7-af87-5e2e72a93277.PNG)

- 실습

결측치 예측모델을 활용해서 결측치를 채워보자


```python
import os
import pandas as pd

os.chdir(r"C:/Users/user/Desktop/aa/part-4.-머신러닝을-위한-필수-전처리/Part 4. 머신러닝을 위한 필수 전처리/데이터/")

df = pd.read_csv("mammographic.csv")
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
      <th>BI-RADS</th>
      <th>Age</th>
      <th>Shape</th>
      <th>Margin</th>
      <th>Density</th>
      <th>Output</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>5.0</td>
      <td>67.0</td>
      <td>3.0</td>
      <td>5.0</td>
      <td>3.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>4.0</td>
      <td>43.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>5.0</td>
      <td>58.0</td>
      <td>4.0</td>
      <td>5.0</td>
      <td>3.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4.0</td>
      <td>28.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>3.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5.0</td>
      <td>74.0</td>
      <td>1.0</td>
      <td>5.0</td>
      <td>NaN</td>
      <td>1</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>956</th>
      <td>4.0</td>
      <td>47.0</td>
      <td>2.0</td>
      <td>1.0</td>
      <td>3.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>957</th>
      <td>4.0</td>
      <td>56.0</td>
      <td>4.0</td>
      <td>5.0</td>
      <td>3.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>958</th>
      <td>4.0</td>
      <td>64.0</td>
      <td>4.0</td>
      <td>5.0</td>
      <td>3.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>959</th>
      <td>5.0</td>
      <td>66.0</td>
      <td>4.0</td>
      <td>5.0</td>
      <td>3.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>960</th>
      <td>4.0</td>
      <td>62.0</td>
      <td>3.0</td>
      <td>3.0</td>
      <td>3.0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>961 rows × 6 columns</p>
</div>



특징과 라벨 분리


```python
X = df.drop('Output', axis = 1)
Y = df['Output']
```

학습 데이터와 평가 데이터 분리


```python
from sklearn.model_selection import train_test_split
Train_X, Test_X, Train_Y, Test_Y = train_test_split(X, Y)
```

열별 결측치 비율 확인 했더니 아래와 같이 그리 높지 않음을 확인했다


```python
Train_X.isnull().sum() / len(Train_X)
```




    BI-RADS    0.001389
    Age        0.005556
    Shape      0.033333
    Margin     0.044444
    Density    0.080556
    dtype: float64




```python
Train_X.corr().sum() / len(Train_X.columns)
```




    BI-RADS    0.447521
    Age        0.416607
    Shape      0.516235
    Margin     0.537773
    Density    0.256620
    dtype: float64



특징 간 상관 계수 확인 => 평균적으로 40 ~ 50%로 매우 높음을 확인

컬럼간에 상관관계가 큰 편이기 때문에 결측치 모델을 사용하기에 적합한 조건이다.


```python
# KNN Imputer 인스턴스화
from sklearn.impute import KNNImputer
KI = KNNImputer(n_neighbors = 5)

# KNN Imputer 학습
KI.fit(Train_X)

# 결측 대체
Train_X = pd.DataFrame(KI.transform(Train_X), columns = Train_X.columns)
Test_X = pd.DataFrame(KI.transform(Test_X), columns = Test_X.columns)
```


```python
Train_X.isnull().sum() / len(Train_X)
```




    BI-RADS    0.0
    Age        0.0
    Shape      0.0
    Margin     0.0
    Density    0.0
    dtype: float64




```python
Test_X.isnull().sum() / len(Test_X)
```




    BI-RADS    0.0
    Age        0.0
    Shape      0.0
    Margin     0.0
    Density    0.0
    dtype: float64


