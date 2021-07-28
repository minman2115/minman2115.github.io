---
layout: post
title: "변수분포문제 - 피쳐와 라벨간 약한 관계 또는 비선형 관계일때"
tags: [데이터전처리]
comments: true
---

.

Data_Preprocessing_TIL(20210728)

[학습자료]

패스트캠퍼스 온라인 강의 "파이썬을 활용한 데이터 전처리 Level UP 올인원 패키지 Online." 를 공부하고 정리한 내용입니다.

URL : https://fastcampus.co.kr/data_online_preprocess

[학습내용]

- 변수분포 문제란 일반화된 모델을 학습하는데 어려움이 있는 분포를 가지는 변수가 있어서, 일반화된 모델을 학습하지 못하는 문제


- 변수분포 문제를 해결하지 않아도 모델학습 자체는 가능하나 당연히 좋은 성능을 낼 수 없다.


- 변수분포 문제의 대표적인 다섯가지

1) 피쳐과 라벨간 약한관계 또는 비선형 관계일때

2) 이상치 포함하는 데이터가 있을때 

3) 피쳐간에 상관성이 강해서 문제가 발생하는 경우

4) 변수간에 스케일 차이가 커서 발생하는 문제

5) 데이터가 정규분포를 따르지 않고 일반분포(알수없는 분포)를 따르는 경우


- 피쳐와 라벨간 약한관계 또는 비선형 관계일때는 어떤게 문제냐


피쳐와 라벨간 관계가 없거나 매우 약하다면, 어떠한 전처리 및 모델링을 하더라도 예측력이 높은 모델을 만들 수 없다.

사실 일반적으로는 피쳐와 라벨간에 관계가 있지만 이를 잘 못찾는 경우가 대다수이다. 예를 들어서 피쳐와 라벨간에 비선형 관계가 존재하는 경우인데 피쳐와 라벨간 비선형 관계가 존재한다면, 적절한 전처리를 통해 모델 성능을 크게 향상 시킬수 있다.

선형회귀 같은 경우에 x와 y가 아래 그림과 같이 곡선의 2차원 모양이다 라고 하면 x를 그대로 쓰는게 아니라 x의 제곱을 쓰면 더 좋은 성능이 좋을 것이다. 그리고 아래에 로지스틱 회귀 모델 사례 처럼 데이터의 구간에 대한 패턴을 파악하고 적절하게 나누면 또 좋은 성능을 낼 것이다.

![1](https://user-images.githubusercontent.com/41605276/127313102-2ab1a61f-3137-4e85-a916-c326b1d4410b.PNG)

참고로 대다수의 머신러닝 모델은 선형식(y=wx+b)을 포함한다.

피쳐가 만약에 10개정도 된다면 피쳐 하나하나 그래프를 그려가면서 관계를 확인하면 되기 때문에 큰 문제는 없을 것이다. 하지만 피쳐가 그이상으로 많다면 이를 일일히 하나씩 확인하는 것이 어려울 것이다. 또한 그래프만 찍어본다고 해서 이게 정말로 관계가 있는건지는 확신할수도 없다. 하나의 피쳐만 하나의 레벨에 영향을 주는게 아니기 때문이다. 여러개의 피쳐가 하나의 라벨에 동시에 영향을 주는 경우가 일반적일 것이다. 이를 제대로 보기 위해서는 다차원 그래프를 그려봐야 하는데 차원이 3차원 이상만 되더라도 그릴수가 없는 지경이다. 결론적으로 그래프를 그려서 관계를 확인한다는 것은 쉽지 않다. 


- 그러면 어떻게 하면 되냐

간단한 방법으로는 어떤 관계가 라벨에 관계가 있는지 모르니까 피쳐별로 다 라벨과의 관계를 그려보는 것이다. x 제곱도 만들어보고, 로그 x도 만들어보고, exp(x)도 만들어보고, 아니면 두 컬럼을 더해보고, 빼보고 하는 등의 피쳐들을 만들어보는 것이다. 

가장 이상적인 해결방안은 각 피쳐에 대해, 피쳐와 라벨 간 관계를 나타내는 그래프를 통해 적절한 피쳐 변환을 수행해야 한다. 하지만 피쳐갯수가 많고, 다른 피쳐에 의한 영향도가 존재하는 등 그래프를 통해 적절한 변환 방법을 선택하는 것은 쉽지 않아, 다양한 변환방법을 사용하여 피쳐를 생성한 뒤 피쳐선택을 수행해야 한다.


- 실습

다양한 변환방법을 사용하여 피쳐를 생성해서 모델링을 해보고, 피쳐를 생성하지 않은 모델과 성능을 비교해보자


```python
import os
import pandas as pd

os.chdir(r"C:/Users/user/Desktop/aa/5. 머신러닝 모델의 성능 향상을 위한 전처리\데이터")

df = pd.read_csv("Combined_Cycle_Power_Plant.csv")
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
      <th>T</th>
      <th>V</th>
      <th>AP</th>
      <th>RH</th>
      <th>EP</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>8.34</td>
      <td>40.77</td>
      <td>1010.84</td>
      <td>90.01</td>
      <td>480.48</td>
    </tr>
    <tr>
      <th>1</th>
      <td>23.64</td>
      <td>58.49</td>
      <td>1011.40</td>
      <td>74.20</td>
      <td>445.75</td>
    </tr>
    <tr>
      <th>2</th>
      <td>29.74</td>
      <td>56.90</td>
      <td>1007.15</td>
      <td>41.91</td>
      <td>438.76</td>
    </tr>
    <tr>
      <th>3</th>
      <td>19.07</td>
      <td>49.69</td>
      <td>1007.22</td>
      <td>76.79</td>
      <td>453.09</td>
    </tr>
    <tr>
      <th>4</th>
      <td>11.80</td>
      <td>40.66</td>
      <td>1017.13</td>
      <td>97.20</td>
      <td>464.43</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>9563</th>
      <td>15.12</td>
      <td>48.92</td>
      <td>1011.80</td>
      <td>72.93</td>
      <td>462.59</td>
    </tr>
    <tr>
      <th>9564</th>
      <td>33.41</td>
      <td>77.95</td>
      <td>1010.30</td>
      <td>59.72</td>
      <td>432.90</td>
    </tr>
    <tr>
      <th>9565</th>
      <td>15.99</td>
      <td>43.34</td>
      <td>1014.20</td>
      <td>78.66</td>
      <td>465.96</td>
    </tr>
    <tr>
      <th>9566</th>
      <td>17.65</td>
      <td>59.87</td>
      <td>1018.58</td>
      <td>94.65</td>
      <td>450.93</td>
    </tr>
    <tr>
      <th>9567</th>
      <td>23.68</td>
      <td>51.30</td>
      <td>1011.86</td>
      <td>71.24</td>
      <td>451.67</td>
    </tr>
  </tbody>
</table>
<p>9568 rows × 5 columns</p>
</div>




```python
# 특징과 라벨 분리
X = df.drop('EP', axis = 1)
Y = df['EP']
```


```python
# 신규 데이터 생성
# 특징이 추가된 데이터를 부착할 데이터
X_added = X.copy() 

import numpy as np
# 로그와 제곱 관련 변수만 추가
for col in X.columns:
    X_added[col + '_squared'] = X[col] ** 2
    X_added[col + '_log'] = np.log(X[col])

X_added
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
      <th>T</th>
      <th>V</th>
      <th>AP</th>
      <th>RH</th>
      <th>T_squared</th>
      <th>T_log</th>
      <th>V_squared</th>
      <th>V_log</th>
      <th>AP_squared</th>
      <th>AP_log</th>
      <th>RH_squared</th>
      <th>RH_log</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>8.34</td>
      <td>40.77</td>
      <td>1010.84</td>
      <td>90.01</td>
      <td>69.5556</td>
      <td>2.121063</td>
      <td>1662.1929</td>
      <td>3.707947</td>
      <td>1.021798e+06</td>
      <td>6.918537</td>
      <td>8101.8001</td>
      <td>4.499921</td>
    </tr>
    <tr>
      <th>1</th>
      <td>23.64</td>
      <td>58.49</td>
      <td>1011.40</td>
      <td>74.20</td>
      <td>558.8496</td>
      <td>3.162940</td>
      <td>3421.0801</td>
      <td>4.068856</td>
      <td>1.022930e+06</td>
      <td>6.919091</td>
      <td>5505.6400</td>
      <td>4.306764</td>
    </tr>
    <tr>
      <th>2</th>
      <td>29.74</td>
      <td>56.90</td>
      <td>1007.15</td>
      <td>41.91</td>
      <td>884.4676</td>
      <td>3.392493</td>
      <td>3237.6100</td>
      <td>4.041295</td>
      <td>1.014351e+06</td>
      <td>6.914880</td>
      <td>1756.4481</td>
      <td>3.735524</td>
    </tr>
    <tr>
      <th>3</th>
      <td>19.07</td>
      <td>49.69</td>
      <td>1007.22</td>
      <td>76.79</td>
      <td>363.6649</td>
      <td>2.948116</td>
      <td>2469.0961</td>
      <td>3.905804</td>
      <td>1.014492e+06</td>
      <td>6.914949</td>
      <td>5896.7041</td>
      <td>4.341074</td>
    </tr>
    <tr>
      <th>4</th>
      <td>11.80</td>
      <td>40.66</td>
      <td>1017.13</td>
      <td>97.20</td>
      <td>139.2400</td>
      <td>2.468100</td>
      <td>1653.2356</td>
      <td>3.705245</td>
      <td>1.034553e+06</td>
      <td>6.924740</td>
      <td>9447.8400</td>
      <td>4.576771</td>
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
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>9563</th>
      <td>15.12</td>
      <td>48.92</td>
      <td>1011.80</td>
      <td>72.93</td>
      <td>228.6144</td>
      <td>2.716018</td>
      <td>2393.1664</td>
      <td>3.890186</td>
      <td>1.023739e+06</td>
      <td>6.919486</td>
      <td>5318.7849</td>
      <td>4.289500</td>
    </tr>
    <tr>
      <th>9564</th>
      <td>33.41</td>
      <td>77.95</td>
      <td>1010.30</td>
      <td>59.72</td>
      <td>1116.2281</td>
      <td>3.508855</td>
      <td>6076.2025</td>
      <td>4.356068</td>
      <td>1.020706e+06</td>
      <td>6.918003</td>
      <td>3566.4784</td>
      <td>4.089667</td>
    </tr>
    <tr>
      <th>9565</th>
      <td>15.99</td>
      <td>43.34</td>
      <td>1014.20</td>
      <td>78.66</td>
      <td>255.6801</td>
      <td>2.771964</td>
      <td>1878.3556</td>
      <td>3.769076</td>
      <td>1.028602e+06</td>
      <td>6.921855</td>
      <td>6187.3956</td>
      <td>4.365135</td>
    </tr>
    <tr>
      <th>9566</th>
      <td>17.65</td>
      <td>59.87</td>
      <td>1018.58</td>
      <td>94.65</td>
      <td>311.5225</td>
      <td>2.870736</td>
      <td>3584.4169</td>
      <td>4.092176</td>
      <td>1.037505e+06</td>
      <td>6.926165</td>
      <td>8958.6225</td>
      <td>4.550186</td>
    </tr>
    <tr>
      <th>9567</th>
      <td>23.68</td>
      <td>51.30</td>
      <td>1011.86</td>
      <td>71.24</td>
      <td>560.7424</td>
      <td>3.164631</td>
      <td>2631.6900</td>
      <td>3.937691</td>
      <td>1.023861e+06</td>
      <td>6.919546</td>
      <td>5075.1376</td>
      <td>4.266054</td>
    </tr>
  </tbody>
</table>
<p>9568 rows × 12 columns</p>
</div>




```python
from sklearn.model_selection import cross_val_score
from sklearn.linear_model import LinearRegression as LR

# 5겹 교차 검증 기반의 평가 수행
X_score = cross_val_score(LR(), X, Y, cv = 5, scoring = 'neg_mean_absolute_error').mean()
X_added_score = cross_val_score(LR(), X_added, Y, cv = 5, scoring = 'neg_mean_absolute_error').mean()

# 결론적으로 성능이 좋아졌다. 
print("특징 추가 전:{}, 특징 추가 후:{}".format(X_score, X_added_score))
```

    특징 추가 전:-3.6282513807290457, 특징 추가 후:-3.332221506696368
    
