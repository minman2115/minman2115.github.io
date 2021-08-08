---
layout: post
title: "변수분포문제 - 피쳐간 스케일 차이 해결하기"
tags: [데이터전처리]
comments: true
---

.

Data_Preprocessing_TIL(20210808)

[학습자료]

패스트캠퍼스 온라인 강의 "파이썬을 활용한 데이터 전처리 Level UP 올인원 패키지 Online." 를 공부하고 정리한 내용입니다.

URL : https://fastcampus.co.kr/data_online_preprocess

[학습내용]

- 피쳐간 스케일 차이문제란

특징간에 스케일이 달라서 발생하는 문제로 스케일이 큰 변수에 의해 혹은 스케일이 작은 변수에 의해 모델이 크게 영향을 받는 문제

스케일이 큰 변수에 영향을 받는 모델 : k-최근접 이웃

** k-최근접 이웃 모델은 거리기반 모델이기 때문에 거리가 스케일이 크면 영향을 받는다.

스케일이 작은 변수에 영향을 받는 모델 : 회귀모델, 서포트 백터머신, 신경망

스케일에 영향을 받지 않는 모델 : 나이브베이즈, 의사결정나무 (이진 분지에 한해서)


- 해결방법

스케일링을 사용하여 변수간에 스케일 차이를 줄여야 한다.

![4](https://user-images.githubusercontent.com/41605276/128625081-ed3f4f51-1d21-4656-b4d9-3fc6e84a1d30.PNG)

모델에 따른 스케일러 선택

스텐다드 스케일러 : 특징에 정규분포를 가정하는 모델 (예시 : 회귀모델, 로지스틱 회귀모델)

민맥스 스케일러 : 특정분포를 가정하지 않는 모델 (예시 : 신경망, k-최근접 이웃)

관련 함수 : `sklearn.preprocessing.MinMaxScaler & StandardScaler`

민맥스 스케일링과 스텐다드 스케일링을 수행하는 함수

주요 메서드

fit : 변수별 통계량을 계산하여 저장 (민맥스 스케일러 : 최대값&최소값, 스텐다드 스케일러 : 평균 및 표준편차)

transform : 변수별 통계량을 바탕으로 스케일링 수행

inverse_transform : 스케일링된 값을 다시 원래값으로 변환

- 실습

야구선수들의 연봉을 예측하는 예시


```python
import os
import pandas as pd

os.chdir(r"C:/Users/user/Desktop/aa/5. 머신러닝 모델의 성능 향상을 위한 전처리\데이터")

df = pd.read_csv("baseball.csv")
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
      <th>Batting_average</th>
      <th>On-base_percentage</th>
      <th>Runs</th>
      <th>Hits</th>
      <th>Doubles</th>
      <th>Triples</th>
      <th>HomeRuns</th>
      <th>Runs_batted_in</th>
      <th>Walks</th>
      <th>Strike-Outs</th>
      <th>Stolen_bases</th>
      <th>Errors</th>
      <th>Free_agency_eligibility</th>
      <th>Free_agent</th>
      <th>Arbitration_eligibility</th>
      <th>Arbitration</th>
      <th>Salary</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.271</td>
      <td>0.328</td>
      <td>74</td>
      <td>161</td>
      <td>22</td>
      <td>6</td>
      <td>12</td>
      <td>58</td>
      <td>49</td>
      <td>133</td>
      <td>23</td>
      <td>17</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>109</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.264</td>
      <td>0.318</td>
      <td>24</td>
      <td>48</td>
      <td>7</td>
      <td>0</td>
      <td>1</td>
      <td>22</td>
      <td>15</td>
      <td>18</td>
      <td>0</td>
      <td>7</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>160</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.251</td>
      <td>0.338</td>
      <td>101</td>
      <td>141</td>
      <td>35</td>
      <td>3</td>
      <td>32</td>
      <td>105</td>
      <td>71</td>
      <td>104</td>
      <td>34</td>
      <td>6</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>2700</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.224</td>
      <td>0.274</td>
      <td>28</td>
      <td>94</td>
      <td>21</td>
      <td>1</td>
      <td>1</td>
      <td>44</td>
      <td>27</td>
      <td>54</td>
      <td>2</td>
      <td>7</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>550</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.206</td>
      <td>0.262</td>
      <td>14</td>
      <td>51</td>
      <td>18</td>
      <td>1</td>
      <td>1</td>
      <td>28</td>
      <td>17</td>
      <td>26</td>
      <td>0</td>
      <td>3</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>300</td>
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
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>332</th>
      <td>0.269</td>
      <td>0.303</td>
      <td>32</td>
      <td>58</td>
      <td>12</td>
      <td>1</td>
      <td>13</td>
      <td>33</td>
      <td>11</td>
      <td>51</td>
      <td>1</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>140</td>
    </tr>
    <tr>
      <th>333</th>
      <td>0.288</td>
      <td>0.366</td>
      <td>24</td>
      <td>47</td>
      <td>7</td>
      <td>0</td>
      <td>7</td>
      <td>24</td>
      <td>18</td>
      <td>23</td>
      <td>2</td>
      <td>3</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>525</td>
    </tr>
    <tr>
      <th>334</th>
      <td>0.208</td>
      <td>0.265</td>
      <td>12</td>
      <td>35</td>
      <td>11</td>
      <td>1</td>
      <td>0</td>
      <td>15</td>
      <td>14</td>
      <td>30</td>
      <td>2</td>
      <td>6</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>365</td>
    </tr>
    <tr>
      <th>335</th>
      <td>0.290</td>
      <td>0.349</td>
      <td>59</td>
      <td>141</td>
      <td>30</td>
      <td>2</td>
      <td>16</td>
      <td>64</td>
      <td>42</td>
      <td>102</td>
      <td>14</td>
      <td>6</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>990</td>
    </tr>
    <tr>
      <th>336</th>
      <td>0.225</td>
      <td>0.312</td>
      <td>37</td>
      <td>64</td>
      <td>12</td>
      <td>0</td>
      <td>17</td>
      <td>48</td>
      <td>36</td>
      <td>80</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>2167</td>
    </tr>
  </tbody>
</table>
<p>337 rows × 17 columns</p>
</div>




```python
# 특징과 라벨 분리
X = df.drop('Salary', axis = 1)
Y = df['Salary']

# 학습 데이터와 평가 데이터 분리
from sklearn.model_selection import train_test_split
Train_X, Test_X, Train_Y, Test_Y = train_test_split(X, Y)

# 특징 간 스케일 차이가 큼을 확인 => 스케일이 작은 특징은 영향을 거의 주지 못할 것이라 예상됨
Train_X.max() - Train_X.min()
```




    Batting_average              0.394
    On-base_percentage           0.423
    Runs                       133.000
    Hits                       215.000
    Doubles                     49.000
    Triples                     15.000
    HomeRuns                    44.000
    Runs_batted_in             133.000
    Walks                      138.000
    Strike-Outs                174.000
    Stolen_bases                72.000
    Errors                      27.000
    Free_agency_eligibility      1.000
    Free_agent                   1.000
    Arbitration_eligibility      1.000
    Arbitration                  1.000
    dtype: float64



위의 경우에는 KNN의 모델의 디폴트가 유클리디안 모델이기 때문에 Runs ,Hits,Strike-Outs 같은 스케일이 큰 변수들이 모델에 거의 영향을 미쳤을 것이다. Batting_average 같은 좋은 피쳐가 있음에도 불구하고 이거는 스케일이 작아서 모델에 영향을 거의 주지 않았을 것이다.

스케일링을 해도 성능이 오히려 떨어지는 경우도 당연히 있다. 예를 들어서 아주 쓸모가 없는 변수인데 스케일도 작아서 애초에 모델에 영향을 안주고 있었는데 스케일링을 해서 스케일을 맞출경우 안좋은 모델에 안좋은 영향이 커질 수 있기 때문이다. 

그래서 스케일링을 했을때 성능이 안좋아졌다면 그 근거가 뭔지 찾아야 한다.


```python
# 스케일링 전에 성능 확인
from sklearn.neighbors import KNeighborsRegressor as KNN
from sklearn.metrics import mean_absolute_error as MAE
model = KNN().fit(Train_X, Train_Y)
pred_Y = model.predict(Test_X)

score = MAE(Test_Y, pred_Y)
print(score)
```

    660.1341176470588
    


```python
# 스케일링 수행
from sklearn.preprocessing import MinMaxScaler
scaler = MinMaxScaler().fit(Train_X)

# sckitlearn의 출력값 형태는 ndarray이다.
s_Train_X = scaler.transform(Train_X)
s_Test_X = scaler.transform(Test_X)

s_Train_X.max() - s_Train_X.min()
```




    1.0




```python
s_Train_X
```




    array([[0.48984772, 0.6855792 , 0.48120301, ..., 0.        , 0.        ,
            0.        ],
           [0.57360406, 0.60520095, 0.42857143, ..., 0.        , 1.        ,
            0.        ],
           [0.47969543, 0.52009456, 0.33082707, ..., 1.        , 0.        ,
            0.        ],
           ...,
           [0.44416244, 0.49408983, 0.39849624, ..., 0.        , 0.        ,
            0.        ],
           [0.60913706, 0.79905437, 0.15789474, ..., 0.        , 0.        ,
            0.        ],
           [0.47715736, 0.59574468, 0.16541353, ..., 0.        , 0.        ,
            0.        ]])




```python
model = KNN().fit(s_Train_X, Train_Y)
pred_Y = model.predict(s_Test_X)

score = MAE(Test_Y, pred_Y)
print(score)
```

    463.0705882352941
    
