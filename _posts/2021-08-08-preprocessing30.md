---
layout: post
title: "변수분포문제 - 변수치우침 문제 해결하기"
tags: [데이터전처리]
comments: true
---

.


Data_Preprocessing_TIL(20210808)

[학습자료]

패스트캠퍼스 온라인 강의 "파이썬을 활용한 데이터 전처리 Level UP 올인원 패키지 Online." 를 공부하고 정리한 내용입니다.

URL : https://fastcampus.co.kr/data_online_preprocess

[학습내용]

- 변수치우침 문제란

모델링에 가장 적합한 분포는 정규분포이나 현실적으로 많은 경우에서는 변수가 특정방향으로 치우쳐 있다.

한쪽으로 치우친 변수에서 치우친 반대방향의 값 (꼬리부분) 들이 이상치처럼 작용할 수 있으므로, 이러한 치우침을 제거해줘야 한다.

![1](https://user-images.githubusercontent.com/41605276/128624634-6c541005-70ac-4cd5-840d-70cd66b99a26.PNG)

- 변수치우침이 있는지 확인방법 : 왜도 (skewness)

왜도는 분포의 비대칭정도를 나타내는 통계량으로, 왜도값에 따른 분포는 다음과 같다.

![2](https://user-images.githubusercontent.com/41605276/128624666-3508553f-ad82-442a-ad20-2113be822996.PNG)

보통 왜도의 절대값이 1.5 이상이면 분포가 치우쳐 있다고 판단한다.

scipy의 `scipy.stats` 라는 함수로 왜도를 측정할 수 있다.

`scipy.stats`는 다양한 확률 통계 관련 함수를 모듈로 제공한다.

`scipy.stats.mode` : 최빈값을 구하는 함수

`scipy.stats.skew` : 왜도를 구하는 함수

`scipy.stats.kurtosis` : 첨도를 구하는 함수

- 변수치우침 해결방안

변수치우침을 해결하는 기본적인 아이디어는 값간에 차이를 줄이는데 있다.

대표적인 처리방법은 아래와 같다.

![3](https://user-images.githubusercontent.com/41605276/128624775-0b509973-4c63-4b6f-bf13-c7b7e8231959.PNG)

- 실습


```python
import os
import pandas as pd

os.chdir(r"C:/Users/user/Desktop/aa/5. 머신러닝 모델의 성능 향상을 위한 전처리\데이터")

df = pd.read_csv("Sonar_Mines_Rocks.csv")
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
      <th>Band1</th>
      <th>Band2</th>
      <th>Band3</th>
      <th>Band4</th>
      <th>Band5</th>
      <th>Band6</th>
      <th>Band7</th>
      <th>Band8</th>
      <th>Band9</th>
      <th>Band10</th>
      <th>...</th>
      <th>Band52</th>
      <th>Band53</th>
      <th>Band54</th>
      <th>Band55</th>
      <th>Band56</th>
      <th>Band57</th>
      <th>Band58</th>
      <th>Band59</th>
      <th>Band60</th>
      <th>Y</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.020</td>
      <td>0.037</td>
      <td>0.043</td>
      <td>0.021</td>
      <td>0.095</td>
      <td>0.099</td>
      <td>0.154</td>
      <td>0.160</td>
      <td>0.311</td>
      <td>0.211</td>
      <td>...</td>
      <td>0.003</td>
      <td>0.006</td>
      <td>0.016</td>
      <td>0.007</td>
      <td>0.017</td>
      <td>0.018</td>
      <td>0.008</td>
      <td>0.009</td>
      <td>0.003</td>
      <td>R</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.045</td>
      <td>0.052</td>
      <td>0.084</td>
      <td>0.069</td>
      <td>0.118</td>
      <td>0.258</td>
      <td>0.216</td>
      <td>0.348</td>
      <td>0.334</td>
      <td>0.287</td>
      <td>...</td>
      <td>0.008</td>
      <td>0.009</td>
      <td>0.005</td>
      <td>0.009</td>
      <td>0.019</td>
      <td>0.014</td>
      <td>0.005</td>
      <td>0.005</td>
      <td>0.004</td>
      <td>R</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.026</td>
      <td>0.058</td>
      <td>0.110</td>
      <td>0.108</td>
      <td>0.097</td>
      <td>0.228</td>
      <td>0.243</td>
      <td>0.377</td>
      <td>0.560</td>
      <td>0.619</td>
      <td>...</td>
      <td>0.023</td>
      <td>0.017</td>
      <td>0.010</td>
      <td>0.018</td>
      <td>0.024</td>
      <td>0.032</td>
      <td>0.016</td>
      <td>0.010</td>
      <td>0.008</td>
      <td>R</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.010</td>
      <td>0.017</td>
      <td>0.062</td>
      <td>0.020</td>
      <td>0.020</td>
      <td>0.037</td>
      <td>0.110</td>
      <td>0.128</td>
      <td>0.060</td>
      <td>0.126</td>
      <td>...</td>
      <td>0.012</td>
      <td>0.004</td>
      <td>0.015</td>
      <td>0.008</td>
      <td>0.007</td>
      <td>0.005</td>
      <td>0.004</td>
      <td>0.004</td>
      <td>0.012</td>
      <td>R</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.076</td>
      <td>0.067</td>
      <td>0.048</td>
      <td>0.039</td>
      <td>0.059</td>
      <td>0.065</td>
      <td>0.121</td>
      <td>0.247</td>
      <td>0.356</td>
      <td>0.446</td>
      <td>...</td>
      <td>0.003</td>
      <td>0.005</td>
      <td>0.010</td>
      <td>0.011</td>
      <td>0.002</td>
      <td>0.007</td>
      <td>0.005</td>
      <td>0.011</td>
      <td>0.009</td>
      <td>R</td>
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
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>203</th>
      <td>0.019</td>
      <td>0.035</td>
      <td>0.017</td>
      <td>0.018</td>
      <td>0.039</td>
      <td>0.163</td>
      <td>0.203</td>
      <td>0.169</td>
      <td>0.233</td>
      <td>0.268</td>
      <td>...</td>
      <td>0.012</td>
      <td>0.010</td>
      <td>0.020</td>
      <td>0.003</td>
      <td>0.010</td>
      <td>0.006</td>
      <td>0.012</td>
      <td>0.019</td>
      <td>0.016</td>
      <td>M</td>
    </tr>
    <tr>
      <th>204</th>
      <td>0.032</td>
      <td>0.010</td>
      <td>0.030</td>
      <td>0.056</td>
      <td>0.076</td>
      <td>0.096</td>
      <td>0.099</td>
      <td>0.102</td>
      <td>0.103</td>
      <td>0.215</td>
      <td>...</td>
      <td>0.006</td>
      <td>0.009</td>
      <td>0.014</td>
      <td>0.006</td>
      <td>0.006</td>
      <td>0.003</td>
      <td>0.003</td>
      <td>0.006</td>
      <td>0.007</td>
      <td>M</td>
    </tr>
    <tr>
      <th>205</th>
      <td>0.052</td>
      <td>0.044</td>
      <td>0.018</td>
      <td>0.029</td>
      <td>0.035</td>
      <td>0.117</td>
      <td>0.126</td>
      <td>0.118</td>
      <td>0.126</td>
      <td>0.253</td>
      <td>...</td>
      <td>0.016</td>
      <td>0.003</td>
      <td>0.005</td>
      <td>0.006</td>
      <td>0.009</td>
      <td>0.014</td>
      <td>0.014</td>
      <td>0.008</td>
      <td>0.003</td>
      <td>M</td>
    </tr>
    <tr>
      <th>206</th>
      <td>0.030</td>
      <td>0.035</td>
      <td>0.049</td>
      <td>0.061</td>
      <td>0.017</td>
      <td>0.135</td>
      <td>0.146</td>
      <td>0.112</td>
      <td>0.194</td>
      <td>0.235</td>
      <td>...</td>
      <td>0.009</td>
      <td>0.005</td>
      <td>0.013</td>
      <td>0.004</td>
      <td>0.004</td>
      <td>0.003</td>
      <td>0.008</td>
      <td>0.004</td>
      <td>0.005</td>
      <td>M</td>
    </tr>
    <tr>
      <th>207</th>
      <td>0.026</td>
      <td>0.036</td>
      <td>0.014</td>
      <td>0.027</td>
      <td>0.021</td>
      <td>0.034</td>
      <td>0.066</td>
      <td>0.140</td>
      <td>0.184</td>
      <td>0.235</td>
      <td>...</td>
      <td>0.015</td>
      <td>0.013</td>
      <td>0.005</td>
      <td>0.004</td>
      <td>0.006</td>
      <td>0.004</td>
      <td>0.004</td>
      <td>0.006</td>
      <td>0.012</td>
      <td>M</td>
    </tr>
  </tbody>
</table>
<p>208 rows × 61 columns</p>
</div>




```python
# 특징과 라벨 분리
X = df.drop('Y', axis = 1)
Y = df['Y']

# 학습 데이터와 평가 데이터로 분리
from sklearn.model_selection import train_test_split
Train_X, Test_X, Train_Y, Test_Y = train_test_split(X, Y)

# 왜도 확인 => Band4의 왜도가 가장 큼 => 어떻게 생겼는지 확인해보자
Train_X.skew() 
```




    Band1     2.228868
    Band2     2.260476
    Band3     2.951125
    Band4     3.788544
    Band5     2.107194
    Band6     1.288695
    Band7     1.057396
    Band8     1.329445
    Band9     1.334646
    Band10    1.352715
    Band11    1.059416
    Band12    0.614725
    Band13    0.773976
    Band14    1.023539
    Band15    0.660078
    Band16    0.605930
    Band17    0.583182
    Band18    0.486804
    Band19    0.263132
    Band20   -0.067000
    Band21   -0.225531
    Band22   -0.340840
    Band23   -0.582071
    Band24   -0.667840
    Band25   -0.720573
    Band26   -0.662067
    Band27   -0.642183
    Band28   -0.606825
    Band29   -0.448073
    Band30   -0.048007
    Band31    0.279177
    Band32    0.329666
    Band33    0.526372
    Band34    0.645258
    Band35    0.609381
    Band36    0.664764
    Band37    0.744208
    Band38    0.967784
    Band39    0.912742
    Band40    0.849675
    Band41    0.737968
    Band42    0.756553
    Band43    1.069909
    Band44    1.387088
    Band45    1.460144
    Band46    1.791579
    Band47    1.893334
    Band48    1.241043
    Band49    1.165043
    Band50    1.442659
    Band51    3.042810
    Band52    2.242982
    Band53    0.821256
    Band54    1.016587
    Band55    1.437494
    Band56    1.054704
    Band57    1.460105
    Band58    1.930916
    Band59    1.668129
    Band60    3.241471
    dtype: float64




```python
%matplotlib inline
df['Band4'].hist()
```




    <AxesSubplot:>

    
![4](https://user-images.githubusercontent.com/41605276/128625953-d0dd7d62-9020-43f3-859f-09cea6a7a76f.png)


치우침을 제거했을 때의 성능 비교를 위한 모델 개발


```python
# 라벨 숫자로 바꾸기
Train_Y.replace({"M":-1, "R":1}, inplace = True)
Test_Y.replace({"M":-1, "R":1}, inplace = True)

# 원본 데이터로 모델링
from sklearn.metrics import f1_score
from sklearn.neural_network import MLPClassifier as MLP
model = MLP(random_state = 153, max_iter = 1000).fit(Train_X, Train_Y)
pred_Y = model.predict(Test_X)
score = f1_score(Test_Y, pred_Y)
print(score)
```

    0.7659574468085106
    


```python
# 왜도 기반 치우친 변수 제거 
import numpy as np
# 왜도의 절대값이 1.5 이상인 컬럼만 가져오기
biased_variables = Train_X.columns[Train_X.skew().abs() > 1.5]
biased_variables
```




    Index(['Band1', 'Band2', 'Band3', 'Band4', 'Band5', 'Band8', 'Band9', 'Band46',
           'Band47', 'Band50', 'Band51', 'Band55', 'Band56', 'Band57', 'Band58',
           'Band59', 'Band60'],
          dtype='object')




```python
# 치우침 제거
Train_X[biased_variables] = Train_X[biased_variables] - Train_X[biased_variables].min() + 1
Train_X[biased_variables]
```

    C:\ProgramData\Anaconda3\lib\site-packages\pandas\core\frame.py:3191: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      self[k1] = value[k2]
    




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
      <th>Band1</th>
      <th>Band2</th>
      <th>Band3</th>
      <th>Band4</th>
      <th>Band5</th>
      <th>Band8</th>
      <th>Band9</th>
      <th>Band46</th>
      <th>Band47</th>
      <th>Band50</th>
      <th>Band51</th>
      <th>Band55</th>
      <th>Band56</th>
      <th>Band57</th>
      <th>Band58</th>
      <th>Band59</th>
      <th>Band60</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>115</th>
      <td>1.039</td>
      <td>1.043</td>
      <td>1.043</td>
      <td>1.078</td>
      <td>1.035</td>
      <td>1.142</td>
      <td>1.074</td>
      <td>1.037</td>
      <td>1.080</td>
      <td>1.006</td>
      <td>1.022</td>
      <td>1.004</td>
      <td>1.016</td>
      <td>1.014</td>
      <td>1.008</td>
      <td>1.025</td>
      <td>1.019</td>
    </tr>
    <tr>
      <th>44</th>
      <td>1.024</td>
      <td>1.044</td>
      <td>1.037</td>
      <td>1.018</td>
      <td>1.125</td>
      <td>1.204</td>
      <td>1.077</td>
      <td>1.451</td>
      <td>1.310</td>
      <td>1.021</td>
      <td>1.021</td>
      <td>1.012</td>
      <td>1.020</td>
      <td>1.002</td>
      <td>1.020</td>
      <td>1.026</td>
      <td>1.017</td>
    </tr>
    <tr>
      <th>144</th>
      <td>1.028</td>
      <td>1.068</td>
      <td>1.097</td>
      <td>1.096</td>
      <td>1.073</td>
      <td>1.071</td>
      <td>1.168</td>
      <td>1.529</td>
      <td>1.329</td>
      <td>1.033</td>
      <td>1.034</td>
      <td>1.008</td>
      <td>1.005</td>
      <td>1.020</td>
      <td>1.013</td>
      <td>1.012</td>
      <td>1.001</td>
    </tr>
    <tr>
      <th>99</th>
      <td>1.018</td>
      <td>1.041</td>
      <td>1.053</td>
      <td>1.072</td>
      <td>1.055</td>
      <td>1.261</td>
      <td>1.113</td>
      <td>1.175</td>
      <td>1.084</td>
      <td>1.015</td>
      <td>1.008</td>
      <td>1.028</td>
      <td>1.009</td>
      <td>1.024</td>
      <td>1.022</td>
      <td>1.019</td>
      <td>1.009</td>
    </tr>
    <tr>
      <th>185</th>
      <td>1.032</td>
      <td>1.061</td>
      <td>1.036</td>
      <td>1.020</td>
      <td>1.037</td>
      <td>1.175</td>
      <td>1.257</td>
      <td>1.190</td>
      <td>1.094</td>
      <td>1.014</td>
      <td>1.033</td>
      <td>1.009</td>
      <td>1.004</td>
      <td>1.006</td>
      <td>1.005</td>
      <td>1.003</td>
      <td>1.005</td>
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
      <th>52</th>
      <td>1.007</td>
      <td>1.004</td>
      <td>1.006</td>
      <td>1.017</td>
      <td>1.052</td>
      <td>1.062</td>
      <td>1.050</td>
      <td>1.014</td>
      <td>1.047</td>
      <td>1.004</td>
      <td>1.002</td>
      <td>1.010</td>
      <td>1.005</td>
      <td>1.003</td>
      <td>1.006</td>
      <td>1.006</td>
      <td>1.002</td>
    </tr>
    <tr>
      <th>83</th>
      <td>1.020</td>
      <td>1.033</td>
      <td>1.037</td>
      <td>1.018</td>
      <td>1.101</td>
      <td>1.091</td>
      <td>1.107</td>
      <td>1.170</td>
      <td>1.183</td>
      <td>1.022</td>
      <td>1.010</td>
      <td>1.007</td>
      <td>1.009</td>
      <td>1.003</td>
      <td>1.003</td>
      <td>1.001</td>
      <td>1.006</td>
    </tr>
    <tr>
      <th>71</th>
      <td>1.002</td>
      <td>1.007</td>
      <td>1.007</td>
      <td>1.033</td>
      <td>1.046</td>
      <td>1.093</td>
      <td>1.116</td>
      <td>1.096</td>
      <td>1.038</td>
      <td>1.006</td>
      <td>1.008</td>
      <td>1.003</td>
      <td>1.000</td>
      <td>1.002</td>
      <td>1.005</td>
      <td>1.002</td>
      <td>1.001</td>
    </tr>
    <tr>
      <th>155</th>
      <td>1.019</td>
      <td>1.012</td>
      <td>1.000</td>
      <td>1.039</td>
      <td>1.064</td>
      <td>1.111</td>
      <td>1.159</td>
      <td>1.076</td>
      <td>1.073</td>
      <td>1.013</td>
      <td>1.017</td>
      <td>1.004</td>
      <td>1.003</td>
      <td>1.002</td>
      <td>1.004</td>
      <td>1.005</td>
      <td>1.001</td>
    </tr>
    <tr>
      <th>63</th>
      <td>1.005</td>
      <td>1.009</td>
      <td>1.000</td>
      <td>1.000</td>
      <td>1.013</td>
      <td>1.085</td>
      <td>1.076</td>
      <td>1.069</td>
      <td>1.060</td>
      <td>1.015</td>
      <td>1.003</td>
      <td>1.003</td>
      <td>1.002</td>
      <td>1.003</td>
      <td>1.003</td>
      <td>1.005</td>
      <td>1.002</td>
    </tr>
  </tbody>
</table>
<p>156 rows × 17 columns</p>
</div>




```python
Train_X[biased_variables] = np.log10(Train_X[biased_variables])
Train_X[biased_variables]
```

    C:\ProgramData\Anaconda3\lib\site-packages\pandas\core\frame.py:3191: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      self[k1] = value[k2]
    




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
      <th>Band1</th>
      <th>Band2</th>
      <th>Band3</th>
      <th>Band4</th>
      <th>Band5</th>
      <th>Band8</th>
      <th>Band9</th>
      <th>Band46</th>
      <th>Band47</th>
      <th>Band50</th>
      <th>Band51</th>
      <th>Band55</th>
      <th>Band56</th>
      <th>Band57</th>
      <th>Band58</th>
      <th>Band59</th>
      <th>Band60</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>115</th>
      <td>0.016616</td>
      <td>0.018284</td>
      <td>0.018284</td>
      <td>0.032619</td>
      <td>0.014940</td>
      <td>0.057666</td>
      <td>0.031004</td>
      <td>0.015779</td>
      <td>0.033424</td>
      <td>0.002598</td>
      <td>0.009451</td>
      <td>0.001734</td>
      <td>0.006894</td>
      <td>0.006038</td>
      <td>0.003461</td>
      <td>0.010724</td>
      <td>0.008174</td>
    </tr>
    <tr>
      <th>44</th>
      <td>0.010300</td>
      <td>0.018700</td>
      <td>0.015779</td>
      <td>0.007748</td>
      <td>0.051153</td>
      <td>0.080626</td>
      <td>0.032216</td>
      <td>0.161667</td>
      <td>0.117271</td>
      <td>0.009026</td>
      <td>0.009026</td>
      <td>0.005181</td>
      <td>0.008600</td>
      <td>0.000868</td>
      <td>0.008600</td>
      <td>0.011147</td>
      <td>0.007321</td>
    </tr>
    <tr>
      <th>144</th>
      <td>0.011993</td>
      <td>0.028571</td>
      <td>0.040207</td>
      <td>0.039811</td>
      <td>0.030600</td>
      <td>0.029789</td>
      <td>0.067443</td>
      <td>0.184407</td>
      <td>0.123525</td>
      <td>0.014100</td>
      <td>0.014521</td>
      <td>0.003461</td>
      <td>0.002166</td>
      <td>0.008600</td>
      <td>0.005609</td>
      <td>0.005181</td>
      <td>0.000434</td>
    </tr>
    <tr>
      <th>99</th>
      <td>0.007748</td>
      <td>0.017451</td>
      <td>0.022428</td>
      <td>0.030195</td>
      <td>0.023252</td>
      <td>0.100715</td>
      <td>0.046495</td>
      <td>0.070038</td>
      <td>0.035029</td>
      <td>0.006466</td>
      <td>0.003461</td>
      <td>0.011993</td>
      <td>0.003891</td>
      <td>0.010300</td>
      <td>0.009451</td>
      <td>0.008174</td>
      <td>0.003891</td>
    </tr>
    <tr>
      <th>185</th>
      <td>0.013680</td>
      <td>0.025715</td>
      <td>0.015360</td>
      <td>0.008600</td>
      <td>0.015779</td>
      <td>0.070038</td>
      <td>0.099335</td>
      <td>0.075547</td>
      <td>0.039017</td>
      <td>0.006038</td>
      <td>0.014100</td>
      <td>0.003891</td>
      <td>0.001734</td>
      <td>0.002598</td>
      <td>0.002166</td>
      <td>0.001301</td>
      <td>0.002166</td>
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
      <th>52</th>
      <td>0.003029</td>
      <td>0.001734</td>
      <td>0.002598</td>
      <td>0.007321</td>
      <td>0.022016</td>
      <td>0.026125</td>
      <td>0.021189</td>
      <td>0.006038</td>
      <td>0.019947</td>
      <td>0.001734</td>
      <td>0.000868</td>
      <td>0.004321</td>
      <td>0.002166</td>
      <td>0.001301</td>
      <td>0.002598</td>
      <td>0.002598</td>
      <td>0.000868</td>
    </tr>
    <tr>
      <th>83</th>
      <td>0.008600</td>
      <td>0.014100</td>
      <td>0.015779</td>
      <td>0.007748</td>
      <td>0.041787</td>
      <td>0.037825</td>
      <td>0.044148</td>
      <td>0.068186</td>
      <td>0.072985</td>
      <td>0.009451</td>
      <td>0.004321</td>
      <td>0.003029</td>
      <td>0.003891</td>
      <td>0.001301</td>
      <td>0.001301</td>
      <td>0.000434</td>
      <td>0.002598</td>
    </tr>
    <tr>
      <th>71</th>
      <td>0.000868</td>
      <td>0.003029</td>
      <td>0.003029</td>
      <td>0.014100</td>
      <td>0.019532</td>
      <td>0.038620</td>
      <td>0.047664</td>
      <td>0.039811</td>
      <td>0.016197</td>
      <td>0.002598</td>
      <td>0.003461</td>
      <td>0.001301</td>
      <td>0.000000</td>
      <td>0.000868</td>
      <td>0.002166</td>
      <td>0.000868</td>
      <td>0.000434</td>
    </tr>
    <tr>
      <th>155</th>
      <td>0.008174</td>
      <td>0.005181</td>
      <td>0.000000</td>
      <td>0.016616</td>
      <td>0.026942</td>
      <td>0.045714</td>
      <td>0.064083</td>
      <td>0.031812</td>
      <td>0.030600</td>
      <td>0.005609</td>
      <td>0.007321</td>
      <td>0.001734</td>
      <td>0.001301</td>
      <td>0.000868</td>
      <td>0.001734</td>
      <td>0.002166</td>
      <td>0.000434</td>
    </tr>
    <tr>
      <th>63</th>
      <td>0.002166</td>
      <td>0.003891</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.005609</td>
      <td>0.035430</td>
      <td>0.031812</td>
      <td>0.028978</td>
      <td>0.025306</td>
      <td>0.006466</td>
      <td>0.001301</td>
      <td>0.001301</td>
      <td>0.000868</td>
      <td>0.001301</td>
      <td>0.001301</td>
      <td>0.002166</td>
      <td>0.000868</td>
    </tr>
  </tbody>
</table>
<p>156 rows × 17 columns</p>
</div>




```python
# 치우침 제거 후 모델 평가
model = MLP(random_state = 153, max_iter = 1000).fit(Train_X, Train_Y)

# 테스트도 데이터도 같은 방법으로 전처리를 수행
Test_X[biased_variables] = Test_X[biased_variables] - Test_X[biased_variables].min() + 1
Test_X[biased_variables] = Test_X[biased_variables].apply(np.log)

pred_Y = model.predict(Test_X)
score = f1_score(Test_Y, pred_Y)
print(score)
```

    0.8837209302325582
    

    C:\ProgramData\Anaconda3\lib\site-packages\pandas\core\frame.py:3191: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      self[k1] = value[k2]
    
