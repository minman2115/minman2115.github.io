---
layout: post
title: "데이터 전처리시 이상치 제거하기"
tags: [데이터전처리]
comments: true
---

.

Data_Preprocessing_TIL(20210729)

[학습자료]

패스트캠퍼스 온라인 강의 "파이썬을 활용한 데이터 전처리 Level UP 올인원 패키지 Online." 를 공부하고 정리한 내용입니다.

URL : https://fastcampus.co.kr/data_online_preprocess

[학습내용]

- 이상치 데이터란

변수범위에서 많이 벗어난 아주 작은 값이나 아주 큰 값으로, 일반화된 모델을 생성하는데 악영향을끼치는 값으로 이상치를 포함하는 레코드를 제거하는 방법으로 이상치를 제거해야 한다. 

절대 추정의 대상이 아님에 주의하자.

이상치는 일반적으로 1프로 미만으로 분포되어 있다.

![1](https://user-images.githubusercontent.com/41605276/127489669-baf6f183-1395-4a2e-b370-696cc6b8c98d.PNG)

- 이상치 데이터여부 판단 방법 1. IQR 규칙 활용

변수별로 IQR 규칙을 만족하지 않는 샘플들을 판단하여 삭제하는 방법으로 가장 많이 쓰이는 방법중에 하나이다.

직관적이고 사용이 간편하다는 장점이 있지만, 단일변수로 이상치를 판단하기 어려운 경우가 있다는 문제가 있음

다시말해서 이 IQR 기준으로만 갖고는 이상치라고 단언할 수는 없다는 것이다.

![2](https://user-images.githubusercontent.com/41605276/127489977-8c6ebccb-ab8e-4717-b01e-2e9bc97193e1.PNG)

- 자주쓰이는 함수 : numpy.quantile

array의 q번째 quantile을 구하는 함수

주요입력

a : input array (list, ndarray, array 등)

q : quantile (0과 1사이)

- 이상치 데이터여부 판단 방법 2. 밀도기반 군집화 수행

말그대로 데이터의 밀도를 기반으로 군집을 만드는 방법이다. 이 방법의 대표 알고리즘이 DBSCAN이다.

디비스캔에서는 앱실론이라고 부르는 반경내에 특정갯수의 샘플이 들어오면 이 샘플들을 중심점이라고 부르고 그리고 중심점은 아니지만 그 중심점의 경계안에 들어온 샘플을 경계점이라고 부른다. 그리고 중심점도 아니고 경계점도 아닌 점들은 이상치라고 판단한다.

![3](https://user-images.githubusercontent.com/41605276/127491090-af852de8-f894-4e76-a788-5d2683ef0343.PNG)

다만 DBSCAN 등의 밀도 기반 군집화 모델의 파라미터 튜닝이 쉽지 않다는 단점이 있음

- 자주쓰이는 함수 : sklearn.cluster.DBSCAN

DBSCAN 군집화를 수행하는 인스턴스를 생성하는 함수

주요입력

eps : 이웃이라고 판단하는 반경

min_samples : 중심점이라 판단하기 위해, eps 내에 들어와야 하는 최소 샘플 수 

metric : 사용하는 거리 척도

주요 attribute

.labels_ : 각 샘플이 속한 군집 정보 (-1 : 이상치)

- 실습

실습 1. 이상치 탐색 및 제거 : IQR Rule 활용


```python
import os
import pandas as pd

os.chdir(r"C:/Users/user/Desktop/aa/5. 머신러닝 모델의 성능 향상을 위한 전처리\데이터")

df = pd.read_csv("glass.csv")
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
      <th>RI</th>
      <th>Na</th>
      <th>Mg</th>
      <th>Al</th>
      <th>Si</th>
      <th>K</th>
      <th>Ca</th>
      <th>Fe</th>
      <th>Glass_type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1.51766</td>
      <td>13.21</td>
      <td>3.69</td>
      <td>1.29</td>
      <td>72.61</td>
      <td>0.57</td>
      <td>8.22</td>
      <td>0.00</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1.51742</td>
      <td>13.27</td>
      <td>3.62</td>
      <td>1.24</td>
      <td>73.08</td>
      <td>0.55</td>
      <td>8.07</td>
      <td>0.00</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1.51756</td>
      <td>13.15</td>
      <td>3.61</td>
      <td>1.05</td>
      <td>73.24</td>
      <td>0.57</td>
      <td>8.24</td>
      <td>0.00</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1.51918</td>
      <td>14.04</td>
      <td>3.58</td>
      <td>1.37</td>
      <td>72.08</td>
      <td>0.56</td>
      <td>8.30</td>
      <td>0.00</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1.51755</td>
      <td>13.00</td>
      <td>3.60</td>
      <td>1.36</td>
      <td>72.99</td>
      <td>0.57</td>
      <td>8.40</td>
      <td>0.11</td>
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
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>209</th>
      <td>1.51653</td>
      <td>11.95</td>
      <td>0.00</td>
      <td>1.19</td>
      <td>75.18</td>
      <td>2.70</td>
      <td>8.93</td>
      <td>0.00</td>
      <td>7</td>
    </tr>
    <tr>
      <th>210</th>
      <td>1.51514</td>
      <td>14.85</td>
      <td>0.00</td>
      <td>2.42</td>
      <td>73.72</td>
      <td>0.00</td>
      <td>8.39</td>
      <td>0.00</td>
      <td>7</td>
    </tr>
    <tr>
      <th>211</th>
      <td>1.51658</td>
      <td>14.80</td>
      <td>0.00</td>
      <td>1.99</td>
      <td>73.11</td>
      <td>0.00</td>
      <td>8.28</td>
      <td>0.00</td>
      <td>7</td>
    </tr>
    <tr>
      <th>212</th>
      <td>1.51732</td>
      <td>14.95</td>
      <td>0.00</td>
      <td>1.80</td>
      <td>72.99</td>
      <td>0.00</td>
      <td>8.61</td>
      <td>0.00</td>
      <td>7</td>
    </tr>
    <tr>
      <th>213</th>
      <td>1.51831</td>
      <td>14.39</td>
      <td>0.00</td>
      <td>1.82</td>
      <td>72.86</td>
      <td>1.41</td>
      <td>6.47</td>
      <td>0.00</td>
      <td>7</td>
    </tr>
  </tbody>
</table>
<p>214 rows × 9 columns</p>
</div>




```python
# 특징과 라벨 분리
X = df.drop(['Glass_type'], axis = 1)
Y = df['Glass_type']
```


```python
# 학습 데이터와 평가 데이터 분리
from sklearn.model_selection import train_test_split
Train_X, Test_X, Train_Y, Test_Y = train_test_split(X, Y)
Train_X.shape
```




    (160, 8)




```python
import numpy as np
def IQR_rule(val_list): # 한 특징에 포함된 값 (열 벡터)
    # IQR 계산    
    Q1 = np.quantile(val_list, 0.25)
    Q3 = np.quantile(val_list, 0.75)
    IQR = Q3 - Q1
    
    # IQR rule을 위배하지 않는 bool list 계산 (True: 이상치 X, False: 이상치 O)
    not_outlier_condition = (Q3 + 1.5 * IQR > val_list) & (Q1 - 1.5 * IQR < val_list)
    return not_outlier_condition
```


```python
# apply를 이용하여 모든 컬럼에 IQR rule 함수 적용
conditions = Train_X.apply(IQR_rule) 
conditions
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
      <th>RI</th>
      <th>Na</th>
      <th>Mg</th>
      <th>Al</th>
      <th>Si</th>
      <th>K</th>
      <th>Ca</th>
      <th>Fe</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>98</th>
      <td>True</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>148</th>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>17</th>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>102</th>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
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
      <td>...</td>
    </tr>
    <tr>
      <th>212</th>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>133</th>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>193</th>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>False</td>
    </tr>
    <tr>
      <th>197</th>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>123</th>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
  </tbody>
</table>
<p>160 rows × 8 columns</p>
</div>




```python
# 하나라도 IQR 규칙을 위반하는 요소를 갖는 레코드를 제거하기 위한 규칙
total_condition = conditions.sum(axis = 1) == len(Train_X.columns)
total_condition
```




    0       True
    98     False
    148     True
    17     False
    102    False
           ...  
    212     True
    133     True
    193    False
    197     True
    123     True
    Length: 160, dtype: bool




```python
# 이상치 제거
Train_X = Train_X.loc[total_condition]
Train_X
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
      <th>RI</th>
      <th>Na</th>
      <th>Mg</th>
      <th>Al</th>
      <th>Si</th>
      <th>K</th>
      <th>Ca</th>
      <th>Fe</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1.51766</td>
      <td>13.21</td>
      <td>3.69</td>
      <td>1.29</td>
      <td>72.61</td>
      <td>0.57</td>
      <td>8.22</td>
      <td>0.00</td>
    </tr>
    <tr>
      <th>148</th>
      <td>1.51409</td>
      <td>14.25</td>
      <td>3.09</td>
      <td>2.08</td>
      <td>72.28</td>
      <td>1.10</td>
      <td>7.08</td>
      <td>0.00</td>
    </tr>
    <tr>
      <th>155</th>
      <td>1.51655</td>
      <td>12.75</td>
      <td>2.85</td>
      <td>1.44</td>
      <td>73.27</td>
      <td>0.57</td>
      <td>8.79</td>
      <td>0.22</td>
    </tr>
    <tr>
      <th>176</th>
      <td>1.51662</td>
      <td>12.85</td>
      <td>3.51</td>
      <td>1.44</td>
      <td>73.01</td>
      <td>0.68</td>
      <td>8.23</td>
      <td>0.25</td>
    </tr>
    <tr>
      <th>124</th>
      <td>1.51775</td>
      <td>12.85</td>
      <td>3.48</td>
      <td>1.23</td>
      <td>72.97</td>
      <td>0.61</td>
      <td>8.56</td>
      <td>0.22</td>
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
      <td>...</td>
    </tr>
    <tr>
      <th>100</th>
      <td>1.51645</td>
      <td>14.94</td>
      <td>0.00</td>
      <td>1.87</td>
      <td>73.11</td>
      <td>0.00</td>
      <td>8.67</td>
      <td>0.00</td>
    </tr>
    <tr>
      <th>212</th>
      <td>1.51732</td>
      <td>14.95</td>
      <td>0.00</td>
      <td>1.80</td>
      <td>72.99</td>
      <td>0.00</td>
      <td>8.61</td>
      <td>0.00</td>
    </tr>
    <tr>
      <th>133</th>
      <td>1.51926</td>
      <td>13.20</td>
      <td>3.33</td>
      <td>1.28</td>
      <td>72.36</td>
      <td>0.60</td>
      <td>9.14</td>
      <td>0.11</td>
    </tr>
    <tr>
      <th>197</th>
      <td>1.51829</td>
      <td>14.46</td>
      <td>2.24</td>
      <td>1.62</td>
      <td>72.38</td>
      <td>0.00</td>
      <td>9.26</td>
      <td>0.00</td>
    </tr>
    <tr>
      <th>123</th>
      <td>1.51747</td>
      <td>12.84</td>
      <td>3.50</td>
      <td>1.14</td>
      <td>73.27</td>
      <td>0.56</td>
      <td>8.55</td>
      <td>0.00</td>
    </tr>
  </tbody>
</table>
<p>115 rows × 8 columns</p>
</div>




```python
Train_X.shape 
```




    (115, 8)




```python
# 이상치의 비율이 거의 30퍼 가까이 되는데 이상치가 30프로라는거는 이는 사실 말이 안되고,
# 일반적으로는 이상치는 1프로 미만이다.
# 위에서 (Q3 + 1.5 * IQR > val_list) & (Q1 - 1.5 * IQR < val_list)로 IRQ를 계산했는데
# 1.5라는 숫자가 공식적으로 지정된 숫자는 아니어서 이를 조절해도 무방한데
# 1.5를 높이는 걸로 조정하면 이상치의 비율이 떨어질것이다.
# 이런식으로 해서 이상치를 1프로 미만으로 낮춰서 조정해주면 된다.
45/160
```




    0.28125



이상치 탐색 및 제거 실습 2. DBSCAN 활용


```python
import pandas as pd
import os
import numpy as np

df = pd.read_csv("glass.csv")
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
      <th>RI</th>
      <th>Na</th>
      <th>Mg</th>
      <th>Al</th>
      <th>Si</th>
      <th>K</th>
      <th>Ca</th>
      <th>Fe</th>
      <th>Glass_type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1.51766</td>
      <td>13.21</td>
      <td>3.69</td>
      <td>1.29</td>
      <td>72.61</td>
      <td>0.57</td>
      <td>8.22</td>
      <td>0.00</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1.51742</td>
      <td>13.27</td>
      <td>3.62</td>
      <td>1.24</td>
      <td>73.08</td>
      <td>0.55</td>
      <td>8.07</td>
      <td>0.00</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1.51756</td>
      <td>13.15</td>
      <td>3.61</td>
      <td>1.05</td>
      <td>73.24</td>
      <td>0.57</td>
      <td>8.24</td>
      <td>0.00</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1.51918</td>
      <td>14.04</td>
      <td>3.58</td>
      <td>1.37</td>
      <td>72.08</td>
      <td>0.56</td>
      <td>8.30</td>
      <td>0.00</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1.51755</td>
      <td>13.00</td>
      <td>3.60</td>
      <td>1.36</td>
      <td>72.99</td>
      <td>0.57</td>
      <td>8.40</td>
      <td>0.11</td>
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
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>209</th>
      <td>1.51653</td>
      <td>11.95</td>
      <td>0.00</td>
      <td>1.19</td>
      <td>75.18</td>
      <td>2.70</td>
      <td>8.93</td>
      <td>0.00</td>
      <td>7</td>
    </tr>
    <tr>
      <th>210</th>
      <td>1.51514</td>
      <td>14.85</td>
      <td>0.00</td>
      <td>2.42</td>
      <td>73.72</td>
      <td>0.00</td>
      <td>8.39</td>
      <td>0.00</td>
      <td>7</td>
    </tr>
    <tr>
      <th>211</th>
      <td>1.51658</td>
      <td>14.80</td>
      <td>0.00</td>
      <td>1.99</td>
      <td>73.11</td>
      <td>0.00</td>
      <td>8.28</td>
      <td>0.00</td>
      <td>7</td>
    </tr>
    <tr>
      <th>212</th>
      <td>1.51732</td>
      <td>14.95</td>
      <td>0.00</td>
      <td>1.80</td>
      <td>72.99</td>
      <td>0.00</td>
      <td>8.61</td>
      <td>0.00</td>
      <td>7</td>
    </tr>
    <tr>
      <th>213</th>
      <td>1.51831</td>
      <td>14.39</td>
      <td>0.00</td>
      <td>1.82</td>
      <td>72.86</td>
      <td>1.41</td>
      <td>6.47</td>
      <td>0.00</td>
      <td>7</td>
    </tr>
  </tbody>
</table>
<p>214 rows × 9 columns</p>
</div>




```python
# 특징과 라벨 분리
X = df.drop(['Glass_type'], axis = 1)
Y = df['Glass_type']
```


```python
# 학습 데이터와 평가 데이터 분리
from sklearn.model_selection import train_test_split
Train_X, Test_X, Train_Y, Test_Y = train_test_split(X, Y)
Train_X.shape
```




    (160, 8)




```python
# cdist를 불러온 이유는 DBSCAN의 파라미터를 조절을 하는데 사용되기 때문이다.
# 앱실론이라는 것은 결국 거리일텐데 이 거리라는게 데이터마다 차이가 클수도 있다.
# cdist를 이용하면 쉽게 데이터간에 거리를 구할 수 있다.
from scipy.spatial.distance import cdist
from sklearn.cluster import DBSCAN
# Train_X와 Train_X 거리 행렬계산 => DBSCAN의 파라미터를 설정하기 위함
DM = cdist(Train_X, Train_X)
DM
```




    array([[0.        , 0.70213971, 0.31128829, ..., 3.64593016, 0.79968827,
            0.95530139],
           [0.70213971, 0.        , 0.54781482, ..., 3.31397179, 0.39534864,
            0.69209942],
           [0.31128829, 0.54781482, 0.        , ..., 3.45168131, 0.65802976,
            0.75039993],
           ...,
           [3.64593016, 3.31397179, 3.45168131, ..., 0.        , 3.54681744,
            3.59279877],
           [0.79968827, 0.39534864, 0.65802976, ..., 3.54681744, 0.        ,
            0.72684527],
           [0.95530139, 0.69209942, 0.75039993, ..., 3.59279877, 0.72684527,
            0.        ]])




```python
# 샘플 간 거리의 10% quantile이 0.6692정도임을 확인
# 여기서 10%는 작은값을 기준으로 상위 10%를 말하는 것임
# 작은값을 기준으로 상위 10%의 거리가 0.6692정도 라는 것이다.
# 0.6692라고 나온값은 단순히 참고하는 수치이고 이게 정확한 기준은 아니다.
np.quantile(DM, 0.1) 
```




    0.6692467740466362




```python
cluster_model = DBSCAN(eps = 0.67, min_samples = 3).fit(Train_X)
print(sum(cluster_model.labels_ == -1))
# 34개가 이상치로 판단 => 이정도면 너무 많은 양이 아닌가?? => 파라미터 조정
```

    34
    


```python
cluster_model = DBSCAN(eps = 2, min_samples = 3).fit(Train_X)
# 앱실론을 크게 늘리면 당연히 반경내에 샘플이 많이 들어갈 가능성이 높다. 
# 그래서 이상치라고 판단되는 데이터의 수도 줄어들 것이다.
# 13개 정도면 괜찮은 양이라고 판단하여 삭제 수행
print(sum(cluster_model.labels_ == -1)) 
```

    13
    


```python
Train_X = Train_X[cluster_model.labels_ != -1]
Train_X
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
      <th>RI</th>
      <th>Na</th>
      <th>Mg</th>
      <th>Al</th>
      <th>Si</th>
      <th>K</th>
      <th>Ca</th>
      <th>Fe</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>114</th>
      <td>1.51761</td>
      <td>12.81</td>
      <td>3.54</td>
      <td>1.23</td>
      <td>73.24</td>
      <td>0.58</td>
      <td>8.39</td>
      <td>0.00</td>
    </tr>
    <tr>
      <th>11</th>
      <td>1.51720</td>
      <td>13.38</td>
      <td>3.50</td>
      <td>1.15</td>
      <td>72.85</td>
      <td>0.50</td>
      <td>8.43</td>
      <td>0.00</td>
    </tr>
    <tr>
      <th>28</th>
      <td>1.51824</td>
      <td>12.87</td>
      <td>3.48</td>
      <td>1.29</td>
      <td>72.95</td>
      <td>0.60</td>
      <td>8.43</td>
      <td>0.00</td>
    </tr>
    <tr>
      <th>105</th>
      <td>1.51651</td>
      <td>14.38</td>
      <td>0.00</td>
      <td>1.94</td>
      <td>73.61</td>
      <td>0.00</td>
      <td>8.48</td>
      <td>0.00</td>
    </tr>
    <tr>
      <th>120</th>
      <td>1.51764</td>
      <td>12.98</td>
      <td>3.54</td>
      <td>1.21</td>
      <td>73.00</td>
      <td>0.65</td>
      <td>8.53</td>
      <td>0.00</td>
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
      <td>...</td>
    </tr>
    <tr>
      <th>76</th>
      <td>1.51665</td>
      <td>13.14</td>
      <td>3.45</td>
      <td>1.76</td>
      <td>72.48</td>
      <td>0.60</td>
      <td>8.38</td>
      <td>0.17</td>
    </tr>
    <tr>
      <th>106</th>
      <td>1.51711</td>
      <td>14.23</td>
      <td>0.00</td>
      <td>2.08</td>
      <td>73.36</td>
      <td>0.00</td>
      <td>8.62</td>
      <td>0.00</td>
    </tr>
    <tr>
      <th>67</th>
      <td>1.52020</td>
      <td>13.98</td>
      <td>1.35</td>
      <td>1.63</td>
      <td>71.76</td>
      <td>0.39</td>
      <td>10.56</td>
      <td>0.18</td>
    </tr>
    <tr>
      <th>41</th>
      <td>1.51646</td>
      <td>13.41</td>
      <td>3.55</td>
      <td>1.25</td>
      <td>72.81</td>
      <td>0.68</td>
      <td>8.10</td>
      <td>0.00</td>
    </tr>
    <tr>
      <th>164</th>
      <td>1.51847</td>
      <td>13.10</td>
      <td>3.97</td>
      <td>1.19</td>
      <td>72.44</td>
      <td>0.60</td>
      <td>8.43</td>
      <td>0.00</td>
    </tr>
  </tbody>
</table>
<p>147 rows × 8 columns</p>
</div>




```python
Train_X.shape
```




    (147, 8)


