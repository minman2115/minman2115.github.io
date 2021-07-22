---
layout: post
title: "데이터 결측문제 - 삭제해서 해결하는 경우"
tags: [데이터전처리]
comments: true
---

.

Data_Preprocessing_TIL(20210722)

#### [학습자료]

패스트캠퍼스 온라인 강의 "파이썬을 활용한 데이터 전처리 Level UP 올인원 패키지 Online." 를 공부하고 정리한 내용입니다.

URL : https://fastcampus.co.kr/data_online_preprocess

#### [개요]

- 문제정의 : 데이터에 결측치가 있어서, 모델학습을 할 수 없는 문제


- 결측치란

1) NaN : 값이 있어야 하는데 없는 결측으로, 대체, 추정, 예측 등으로 처리가 가능

2) None : 값이 없는게 값인 결측으로 새로운 값으로 정의하는 방식으로 처리가 가능

ex) 직업 : None ---> 직업 : 백수


- 데이터 결측문제의 처리하는 방법은 매우 간단하지만, 상황이 다양하기 때문에 이 상황별로 어떻게 처리해야 할지를 잘 선택해야함


- 결측 레코드란 결측치를 포함하는 레코드를 말함


- 결측치 비율 : 결측 레코드수 / 전체 레코드 수

![1](https://user-images.githubusercontent.com/41605276/126636137-e8c3499e-5c4a-4c01-900b-659469a9fdd0.PNG)


#### [결측치 삭제하기]


삭제는 상당히 간단한 방법이지만 여러 조건을 만족해야만 사용할 수 있는 의외로 복잡한 방법중에 하나이다.


삭제는 크게 결측있는 행을 삭제하는 방법과 결측있는 열을 삭제하는 방법이 있다.


- 결측있는 행 삭제하기

결측 레코드를 삭제하는 매우 간단한 방법이지만, 아래의 두가지 조건을 만족하는 경우에만 수행할 수 있다.


조건 1. 결측이 없는 레코드가 모델을 학습하는데 충분해야 한다.

근거 : 결측을 제거한 데이터로 모델을 학습하기 때문임

결정방법 : 학습 샘플갯수에 따른 성능의 수렴여부를 판단해서 결정한다.


조건 2. 결측이 새로운 데이터에는 없어야 한다.

근거 : 결측을 제거한 데이터로 학습한 모델은 결측이 포함된 레코드를 분류하거나 예측할 수 없기 때문이다.

결정방법 : 도메인 지식 기반으로 판단한다.


- 결측있는 열 삭제하기

결측 레코드를 포함하는 열을 삭제하는 매우 간단한 방법이지만, 두가지 조건을 만족하는 경우에만 사용이 가능하다.


조건 1. 어떤 특정컬럼인데 결측이 아주 많이 있다.

특정 컬럼에 켤측이 너무 많아서 행단위의 삭제가 불가한 경우, 예를 들어서 샘플의 갯수가 1만개인데 특정컬럼의 결측치가 9000개가 있다고 한다면 이 경우에는 그 컬럼자체를 삭제할 수 있는 조건 1을 만족한다고 할 수 있다.


조건 2. 해당 변수들이 도메인 지식으로 판단했을때 크게 중요하지 않다고 판단했다.


예를 들어서 설비 부품의 고장을 예측하는 문제에서 '진동수'라는 변수의 결측치가 30 ~ 40% 정도로 클때는 어떻게 해야할까. 일반적으로 30~40%의 결측이 있는 컬럼이라고 한다면 조건 1을 만족한다고 할 수 있다. 하지만 이 진동수라는 변수는 고장을 예측하는데 중요한 변수이므로 함부로 삭제하기는 애매하다. 이때는 도메인 지식 판단하에 진동수라는 컬럼을 지우는게 아니라 레코드 행을 지우는 쪽으로 판단하던가 아니면 그 결측값들이 어떤것들이었는지 예측을 해서 채워넣어야 한다. 결측값을 예측하는 모델을 만들어서 그 결측값을 채워넣었던 실제 사례도 있다.


- 위의 경우에서 많이 사용하는 함수 

1) Series 또는 DataFrame.isnull

값이 결측이면 True를 그렇지 않으면 False를 반환한다. (notnull 함수와 반대로 동작함)

sum 함수와 같이 사용해서 결측치 분포를 확인하는데 주로 사용함

일반적으로 isnull로 결측값을 산출한 다음 결측비율을 계산해서 현황을 파악한다.

![2](https://user-images.githubusercontent.com/41605276/126638471-49447c8d-286f-4aba-98e8-cdd5c9676cc1.png)

2) DataFrame.dropna

결측치가 포함된 행이나 열을 제거하는데 사용

주요입력

axis : 1이면 결측이 포함된 열을 삭제하고, 0이면 결측이 포함된 행을 삭제한다.

how : 'any'면 결측이 하나라도 포함되면 삭제하며, 'all'이면 모든값이 결측인 경우에만 삭제한다.(일반적으로 any를 많이 사용한다)

![3](https://user-images.githubusercontent.com/41605276/126638740-dd300013-4d27-42b0-8537-056404db56d9.PNG)

- 실습 1. 결측있는 행 삭제하기

데이터 불러오기


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
      <td>0</td>
      <td>5.0</td>
      <td>67.0</td>
      <td>3.0</td>
      <td>5.0</td>
      <td>3.0</td>
      <td>1</td>
    </tr>
    <tr>
      <td>1</td>
      <td>4.0</td>
      <td>43.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>1</td>
    </tr>
    <tr>
      <td>2</td>
      <td>5.0</td>
      <td>58.0</td>
      <td>4.0</td>
      <td>5.0</td>
      <td>3.0</td>
      <td>1</td>
    </tr>
    <tr>
      <td>3</td>
      <td>4.0</td>
      <td>28.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>3.0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>4</td>
      <td>5.0</td>
      <td>74.0</td>
      <td>1.0</td>
      <td>5.0</td>
      <td>NaN</td>
      <td>1</td>
    </tr>
    <tr>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <td>956</td>
      <td>4.0</td>
      <td>47.0</td>
      <td>2.0</td>
      <td>1.0</td>
      <td>3.0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>957</td>
      <td>4.0</td>
      <td>56.0</td>
      <td>4.0</td>
      <td>5.0</td>
      <td>3.0</td>
      <td>1</td>
    </tr>
    <tr>
      <td>958</td>
      <td>4.0</td>
      <td>64.0</td>
      <td>4.0</td>
      <td>5.0</td>
      <td>3.0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>959</td>
      <td>5.0</td>
      <td>66.0</td>
      <td>4.0</td>
      <td>5.0</td>
      <td>3.0</td>
      <td>1</td>
    </tr>
    <tr>
      <td>960</td>
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



피쳐와 라벨 분리


```python
X = df.drop('Output', axis = 1)
X
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
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>5.0</td>
      <td>67.0</td>
      <td>3.0</td>
      <td>5.0</td>
      <td>3.0</td>
    </tr>
    <tr>
      <td>1</td>
      <td>4.0</td>
      <td>43.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>2</td>
      <td>5.0</td>
      <td>58.0</td>
      <td>4.0</td>
      <td>5.0</td>
      <td>3.0</td>
    </tr>
    <tr>
      <td>3</td>
      <td>4.0</td>
      <td>28.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>3.0</td>
    </tr>
    <tr>
      <td>4</td>
      <td>5.0</td>
      <td>74.0</td>
      <td>1.0</td>
      <td>5.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <td>956</td>
      <td>4.0</td>
      <td>47.0</td>
      <td>2.0</td>
      <td>1.0</td>
      <td>3.0</td>
    </tr>
    <tr>
      <td>957</td>
      <td>4.0</td>
      <td>56.0</td>
      <td>4.0</td>
      <td>5.0</td>
      <td>3.0</td>
    </tr>
    <tr>
      <td>958</td>
      <td>4.0</td>
      <td>64.0</td>
      <td>4.0</td>
      <td>5.0</td>
      <td>3.0</td>
    </tr>
    <tr>
      <td>959</td>
      <td>5.0</td>
      <td>66.0</td>
      <td>4.0</td>
      <td>5.0</td>
      <td>3.0</td>
    </tr>
    <tr>
      <td>960</td>
      <td>4.0</td>
      <td>62.0</td>
      <td>3.0</td>
      <td>3.0</td>
      <td>3.0</td>
    </tr>
  </tbody>
</table>
<p>961 rows × 5 columns</p>
</div>




```python
Y = df['Output']
Y
```




    0      1
    1      1
    2      1
    3      0
    4      1
          ..
    956    0
    957    1
    958    0
    959    1
    960    0
    Name: Output, Length: 961, dtype: int64



Train 데이터와 Test 데이터로 분할


```python
from sklearn.model_selection import train_test_split
Train_X, Test_X, Train_Y, Test_Y = train_test_split(X, Y)
```

열별 결측치 개수 확인

단순히 결측치만 확인했을때는 잘 모르겠다.


```python
Train_X.isnull().sum(axis = 0)
```




    BI-RADS     1
    Age         5
    Shape      28
    Margin     35
    Density    55
    dtype: int64



열별 결측치 비율 확인

결측이 전체적으로 많은 편이 아니나, 모든 컬럼에 결측이 1회 이상 발생 => 열 삭제 불가


```python
Train_X.isnull().sum(axis = 0) / len(Train_X)
```




    BI-RADS    0.001389
    Age        0.006944
    Shape      0.038889
    Margin     0.048611
    Density    0.076389
    dtype: float64



결측이 포함된 행 레코드 제거


```python
Train_X.dropna(inplace = True)
# Train_X = Train_X.dropna() 이거와 똑같은거다.
```

    C:\ProgramData\Anaconda3\lib\site-packages\ipykernel_launcher.py:1: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      """Entry point for launching an IPython kernel.
    

결측이 포함된 레코드 제거 (주의: 새로 들어온 레코드의 결측이 있으면 처리하지 못함을 의미)


```python
Test_X.dropna(inplace = True) 
```

    C:\ProgramData\Anaconda3\lib\site-packages\ipykernel_launcher.py:1: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      """Entry point for launching an IPython kernel.
    

- 실습 2. 결측이 있는 열 삭제하기

데이터 불러오기

** 데이터 현황을 파악할때 각 컬럼별로 유니크한 값들을 먼저 확인하자.

COMFORT 피쳐에 ?라고 되어 있는데 담당자한테 물어보니까 결측값을 ?로 그냥 처리했다고 하더라. 대충 print 했을때 ?가 보여서 다행이지 안보였으면 이런값이 있는줄도 몰랐을 것이다.




```python
df = pd.read_csv("post_operative.csv")
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
      <th>L-CORE</th>
      <th>L-SURF</th>
      <th>L-O2</th>
      <th>L-BP</th>
      <th>SURF-STBL</th>
      <th>CORE-STBL</th>
      <th>BP-STBL</th>
      <th>COMFORT</th>
      <th>Decision</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>mid</td>
      <td>low</td>
      <td>excellent</td>
      <td>mid</td>
      <td>stable</td>
      <td>stable</td>
      <td>stable</td>
      <td>15</td>
      <td>A</td>
    </tr>
    <tr>
      <td>1</td>
      <td>mid</td>
      <td>high</td>
      <td>excellent</td>
      <td>high</td>
      <td>stable</td>
      <td>stable</td>
      <td>stable</td>
      <td>?</td>
      <td>S</td>
    </tr>
    <tr>
      <td>2</td>
      <td>high</td>
      <td>low</td>
      <td>excellent</td>
      <td>high</td>
      <td>stable</td>
      <td>stable</td>
      <td>mod-stable</td>
      <td>?</td>
      <td>A</td>
    </tr>
    <tr>
      <td>3</td>
      <td>mid</td>
      <td>low</td>
      <td>good</td>
      <td>high</td>
      <td>stable</td>
      <td>unstable</td>
      <td>mod-stable</td>
      <td>15</td>
      <td>A</td>
    </tr>
    <tr>
      <td>4</td>
      <td>mid</td>
      <td>mid</td>
      <td>excellent</td>
      <td>high</td>
      <td>stable</td>
      <td>stable</td>
      <td>stable</td>
      <td>?</td>
      <td>A</td>
    </tr>
    <tr>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <td>85</td>
      <td>mid</td>
      <td>mid</td>
      <td>excellent</td>
      <td>mid</td>
      <td>unstable</td>
      <td>stable</td>
      <td>stable</td>
      <td>10</td>
      <td>A</td>
    </tr>
    <tr>
      <td>86</td>
      <td>mid</td>
      <td>mid</td>
      <td>excellent</td>
      <td>mid</td>
      <td>unstable</td>
      <td>stable</td>
      <td>stable</td>
      <td>15</td>
      <td>S</td>
    </tr>
    <tr>
      <td>87</td>
      <td>mid</td>
      <td>mid</td>
      <td>good</td>
      <td>mid</td>
      <td>unstable</td>
      <td>stable</td>
      <td>stable</td>
      <td>15</td>
      <td>A</td>
    </tr>
    <tr>
      <td>88</td>
      <td>mid</td>
      <td>mid</td>
      <td>excellent</td>
      <td>mid</td>
      <td>unstable</td>
      <td>stable</td>
      <td>stable</td>
      <td>?</td>
      <td>A</td>
    </tr>
    <tr>
      <td>89</td>
      <td>mid</td>
      <td>mid</td>
      <td>good</td>
      <td>mid</td>
      <td>unstable</td>
      <td>stable</td>
      <td>stable</td>
      <td>15</td>
      <td>S</td>
    </tr>
  </tbody>
</table>
<p>90 rows × 9 columns</p>
</div>




```python
# 특징과 라벨 분리
X = df.drop('Decision', axis = 1)
Y = df['Decision']
```


```python
# 학습 데이터와 평가 데이터 분할
from sklearn.model_selection import train_test_split
Train_X, Test_X, Train_Y, Test_Y = train_test_split(X, Y)
```

COMFORT 변수에 '?'로 결측이 표시되어 있음을 확인


```python
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
      <th>L-CORE</th>
      <th>L-SURF</th>
      <th>L-O2</th>
      <th>L-BP</th>
      <th>SURF-STBL</th>
      <th>CORE-STBL</th>
      <th>BP-STBL</th>
      <th>COMFORT</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>41</td>
      <td>low</td>
      <td>mid</td>
      <td>good</td>
      <td>mid</td>
      <td>stable</td>
      <td>stable</td>
      <td>stable</td>
      <td>?</td>
    </tr>
    <tr>
      <td>11</td>
      <td>mid</td>
      <td>low</td>
      <td>good</td>
      <td>high</td>
      <td>stable</td>
      <td>stable</td>
      <td>mod-stable</td>
      <td>10</td>
    </tr>
    <tr>
      <td>5</td>
      <td>high</td>
      <td>low</td>
      <td>good</td>
      <td>mid</td>
      <td>stable</td>
      <td>stable</td>
      <td>unstable</td>
      <td>15</td>
    </tr>
    <tr>
      <td>68</td>
      <td>mid</td>
      <td>mid</td>
      <td>excellent</td>
      <td>high</td>
      <td>stable</td>
      <td>stable</td>
      <td>stable</td>
      <td>10</td>
    </tr>
    <tr>
      <td>32</td>
      <td>mid</td>
      <td>mid</td>
      <td>excellent</td>
      <td>high</td>
      <td>unstable</td>
      <td>stable</td>
      <td>mod-stable</td>
      <td>?</td>
    </tr>
  </tbody>
</table>
</div>



결측이 '?'로 표시되어 있음 => 데이터에 대한 이해가 없으면 적절히 처리가 안될 수 있음

일반적으로 결측률 30프로를 기준으로 이보다 높아지면 넘어가면 결측비율이 높다고 판단한다.


```python
import numpy as np
Train_X.replace('?', np.nan).isnull().sum() / len(Train_X)
```




    L-CORE       0.000000
    L-SURF       0.000000
    L-O2         0.000000
    L-BP         0.000000
    SURF-STBL    0.000000
    CORE-STBL    0.000000
    BP-STBL      0.000000
    COMFORT      0.268657
    dtype: float64



모든 결측이 COMFORT에 쏠렸으며, 해당 변수가 중요하지 않다는 도메인 지식 기반 하에 삭제한다.


```python
Train_X.dropna(axis = 1, inplace = True)
```

    C:\ProgramData\Anaconda3\lib\site-packages\ipykernel_launcher.py:1: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      """Entry point for launching an IPython kernel.
    

Test_X에는 COMFORT가 결측이 없었을 수도 있으므로, drop을 이용하여 삭제

(최악의 경우에 Tran_X에는 결측값이 없었는데, Test_X에 결측값이 있을수도 있다는 점도 생각하자)


```python
# Test_X = Test_X[Train_X.columns]
Test_X.drop('COMFORT', axis = 1, inplace = True)
```

    C:\ProgramData\Anaconda3\lib\site-packages\pandas\core\frame.py:4102: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      errors=errors,
    


```python
Train_X.isnull().sum(axis = 0)
```




    L-CORE       0
    L-SURF       0
    L-O2         0
    L-BP         0
    SURF-STBL    0
    CORE-STBL    0
    BP-STBL      0
    COMFORT      0
    dtype: int64


