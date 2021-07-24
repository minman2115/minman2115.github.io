---
layout: post
title: "데이터 결측문제 - 대표값 또는 근처값(시계열변수에만 한정)으로 대체해서 해결하는 경우"
tags: [데이터전처리]
comments: true
---

.


Data_Preprocessing_TIL(20210724)

[학습자료]

패스트캠퍼스 온라인 강의 "파이썬을 활용한 데이터 전처리 Level UP 올인원 패키지 Online." 를 공부하고 정리한 내용입니다.

URL : https://fastcampus.co.kr/data_online_preprocess


[학습내용]


- 가장 흔하게 사용하는 방법이지만 아래의 두가지 케이스의 경우에는 사용하기에 부적합하다.

케이스 1. 일부 소수 피쳐에 결측이 쏠린 경우

케이스 2. 피쳐간에 상관성이 큰 경우

아래에 오른쪽 그림을 보면 원래는 V1과 V2에 관계에 따라 데이터들이 들어가게 되는건데 무작정 대표값으로 대체했을때는 데이터에 왜곡이 발생할 수 밖에 없기 때문이다.

![1](https://user-images.githubusercontent.com/41605276/126863309-18ac0f2e-150a-4e30-826d-ab683549eca1.PNG)


- sklearn을 이용한 데이터 전처리 모델

sklearn을 사용해서 전처리를 한다면 일반적으로 아래 그림과 같은 구조로 한다.

Test data는 전처리 모델을 학습하는데 사용하지 않는다는 것을 주의해야 한다.

![2](https://user-images.githubusercontent.com/41605276/126863359-25b06e7c-cf25-4e01-89df-3d225848906c.PNG)

step 1) 전처리 모델을 인스턴스화 시킨다.

인스턴스화 시킨게 Preprocessing model이다.

step 2) Preprocessing model을 Train data를 갖고 fitting을 시킨다.

그러면 Preprocessing model은 Train data를 어떻게 바꿔야 하구나 라는 정보를 기억하게 된다.

예를 들어서 각변수에 결측이 있는데 이 결측값을 뭘로 대체해야 하는지 (각 변수별로 대표값이 어떤건지) 알게 된다.

step 3) 그리고 Preprocessing model을 갖고 transform을 하면 Train data의 결측값들이 모델에 의해 추정된 대표값들로 채워지게 될 것이다. (preprocessed train data 생성)

또는 fit&trainsform을 한꺼번에 하는 함수로 원샷에 처리도 가능하다.

step 4) 그런 다음에 test data를 Preprocessing model로 transform을 해서 preprocessed test data를 생성한다. 


또는 데이터를 train&test data를 분할하기전에 전체 데이터에 대해서 Preprocessing model을 갖고 fit&transform을 할수도 있다. 그러나 이런 경우에는 test data를 치팅한 효과가 나타나기 때문에 좋은 방법은 아니다. 

주의해야할점은 전처리를 하던 모델링을 하던 우리가 다루는 데이터는 train data에 불과하다.


- 결측이 있는 변수의 대표값으로 결측을 대체하는 인스턴스 : sklearn.impute.SimpleImputer

주요입력

strategy : 대표통계량을 지정('mean', 'most_frequent', 'median')

![4](https://user-images.githubusercontent.com/41605276/126863483-49b2455b-3755-461d-9f2f-f14346cf27d7.PNG)

변수타입에 따라 두개의 인스턴스를 같이 적용해야 할 수도 있음

예를들어서 만약에 v1이 범주형이고, v2가 연속형 변수이면 v1에는 most_frequent가 들어갈테고, v2는 mean이 될것이다. 


- 시계열 변수에 한해서 결측값을 근처값으로 대체하는 방법을 사용할 수 있음

시계열 변수인 경우에는 결측이 바로 이전 값 혹은 이후값과 유사할 가능성이 높기 때문에 근처값으로 대체하는 경우가 있다.

![3](https://user-images.githubusercontent.com/41605276/126863537-a1442ef1-d526-42e8-8a98-7616c9d8ab1f.PNG)


- 결측치를 특정값이나 방법으로 채우는 함수 : DataFrame.fillna

주요입력

![5](https://user-images.githubusercontent.com/41605276/126864233-f77e2549-4f8f-4a87-9649-713e2423ef68.png)

주의해야 할점은 반드시 시간에 따라 먼저 정렬이 되어 있는 상태여야 한다.

- 실습

실습 1. 대표값을 활용한 결측치 대체하기

단순한 케이스 : 모든 피쳐의 타입이 같을때


```python
import os
import pandas as pd

os.chdir(r"C:/Users/user/Desktop/aa/part-4.-머신러닝을-위한-필수-전처리/Part 4. 머신러닝을 위한 필수 전처리/데이터/")

df = pd.read_csv("cleveland.csv")
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
      <th>Age</th>
      <th>Sex</th>
      <th>Cp</th>
      <th>Trestbps</th>
      <th>Chol</th>
      <th>Fbs</th>
      <th>Restecg</th>
      <th>Thalach</th>
      <th>Exang</th>
      <th>Oldpeak</th>
      <th>Slope</th>
      <th>Ca</th>
      <th>Thal</th>
      <th>Output</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>63</td>
      <td>1</td>
      <td>1</td>
      <td>145</td>
      <td>233</td>
      <td>1</td>
      <td>2</td>
      <td>150</td>
      <td>0</td>
      <td>2.3</td>
      <td>3</td>
      <td>0.0</td>
      <td>6.0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>1</td>
      <td>37</td>
      <td>1</td>
      <td>3</td>
      <td>130</td>
      <td>250</td>
      <td>0</td>
      <td>0</td>
      <td>187</td>
      <td>0</td>
      <td>3.5</td>
      <td>3</td>
      <td>0.0</td>
      <td>3.0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>2</td>
      <td>41</td>
      <td>0</td>
      <td>2</td>
      <td>130</td>
      <td>204</td>
      <td>0</td>
      <td>2</td>
      <td>172</td>
      <td>0</td>
      <td>1.4</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>3</td>
      <td>56</td>
      <td>1</td>
      <td>2</td>
      <td>120</td>
      <td>236</td>
      <td>0</td>
      <td>0</td>
      <td>178</td>
      <td>0</td>
      <td>0.8</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>4</td>
      <td>57</td>
      <td>0</td>
      <td>4</td>
      <td>120</td>
      <td>354</td>
      <td>0</td>
      <td>0</td>
      <td>163</td>
      <td>1</td>
      <td>0.6</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.0</td>
      <td>0</td>
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
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <td>214</td>
      <td>58</td>
      <td>0</td>
      <td>4</td>
      <td>130</td>
      <td>197</td>
      <td>0</td>
      <td>0</td>
      <td>131</td>
      <td>0</td>
      <td>0.6</td>
      <td>2</td>
      <td>0.0</td>
      <td>3.0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>215</td>
      <td>35</td>
      <td>1</td>
      <td>2</td>
      <td>122</td>
      <td>192</td>
      <td>0</td>
      <td>0</td>
      <td>174</td>
      <td>0</td>
      <td>0.0</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>216</td>
      <td>56</td>
      <td>1</td>
      <td>2</td>
      <td>120</td>
      <td>240</td>
      <td>0</td>
      <td>0</td>
      <td>169</td>
      <td>0</td>
      <td>0.0</td>
      <td>3</td>
      <td>0.0</td>
      <td>3.0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>217</td>
      <td>63</td>
      <td>0</td>
      <td>4</td>
      <td>124</td>
      <td>197</td>
      <td>0</td>
      <td>0</td>
      <td>136</td>
      <td>1</td>
      <td>0.0</td>
      <td>2</td>
      <td>0.0</td>
      <td>3.0</td>
      <td>1</td>
    </tr>
    <tr>
      <td>218</td>
      <td>57</td>
      <td>0</td>
      <td>4</td>
      <td>140</td>
      <td>241</td>
      <td>0</td>
      <td>0</td>
      <td>123</td>
      <td>1</td>
      <td>0.2</td>
      <td>2</td>
      <td>0.0</td>
      <td>7.0</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
<p>219 rows × 14 columns</p>
</div>




```python
# 특징과 라벨 분리
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
      <th>Age</th>
      <th>Sex</th>
      <th>Cp</th>
      <th>Trestbps</th>
      <th>Chol</th>
      <th>Fbs</th>
      <th>Restecg</th>
      <th>Thalach</th>
      <th>Exang</th>
      <th>Oldpeak</th>
      <th>Slope</th>
      <th>Ca</th>
      <th>Thal</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>63</td>
      <td>1</td>
      <td>1</td>
      <td>145</td>
      <td>233</td>
      <td>1</td>
      <td>2</td>
      <td>150</td>
      <td>0</td>
      <td>2.3</td>
      <td>3</td>
      <td>0.0</td>
      <td>6.0</td>
    </tr>
    <tr>
      <td>1</td>
      <td>37</td>
      <td>1</td>
      <td>3</td>
      <td>130</td>
      <td>250</td>
      <td>0</td>
      <td>0</td>
      <td>187</td>
      <td>0</td>
      <td>3.5</td>
      <td>3</td>
      <td>0.0</td>
      <td>3.0</td>
    </tr>
    <tr>
      <td>2</td>
      <td>41</td>
      <td>0</td>
      <td>2</td>
      <td>130</td>
      <td>204</td>
      <td>0</td>
      <td>2</td>
      <td>172</td>
      <td>0</td>
      <td>1.4</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.0</td>
    </tr>
    <tr>
      <td>3</td>
      <td>56</td>
      <td>1</td>
      <td>2</td>
      <td>120</td>
      <td>236</td>
      <td>0</td>
      <td>0</td>
      <td>178</td>
      <td>0</td>
      <td>0.8</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.0</td>
    </tr>
    <tr>
      <td>4</td>
      <td>57</td>
      <td>0</td>
      <td>4</td>
      <td>120</td>
      <td>354</td>
      <td>0</td>
      <td>0</td>
      <td>163</td>
      <td>1</td>
      <td>0.6</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.0</td>
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
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <td>214</td>
      <td>58</td>
      <td>0</td>
      <td>4</td>
      <td>130</td>
      <td>197</td>
      <td>0</td>
      <td>0</td>
      <td>131</td>
      <td>0</td>
      <td>0.6</td>
      <td>2</td>
      <td>0.0</td>
      <td>3.0</td>
    </tr>
    <tr>
      <td>215</td>
      <td>35</td>
      <td>1</td>
      <td>2</td>
      <td>122</td>
      <td>192</td>
      <td>0</td>
      <td>0</td>
      <td>174</td>
      <td>0</td>
      <td>0.0</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.0</td>
    </tr>
    <tr>
      <td>216</td>
      <td>56</td>
      <td>1</td>
      <td>2</td>
      <td>120</td>
      <td>240</td>
      <td>0</td>
      <td>0</td>
      <td>169</td>
      <td>0</td>
      <td>0.0</td>
      <td>3</td>
      <td>0.0</td>
      <td>3.0</td>
    </tr>
    <tr>
      <td>217</td>
      <td>63</td>
      <td>0</td>
      <td>4</td>
      <td>124</td>
      <td>197</td>
      <td>0</td>
      <td>0</td>
      <td>136</td>
      <td>1</td>
      <td>0.0</td>
      <td>2</td>
      <td>0.0</td>
      <td>3.0</td>
    </tr>
    <tr>
      <td>218</td>
      <td>57</td>
      <td>0</td>
      <td>4</td>
      <td>140</td>
      <td>241</td>
      <td>0</td>
      <td>0</td>
      <td>123</td>
      <td>1</td>
      <td>0.2</td>
      <td>2</td>
      <td>0.0</td>
      <td>7.0</td>
    </tr>
  </tbody>
</table>
<p>219 rows × 13 columns</p>
</div>




```python
Y = df['Output']
Y
```




    0      0
    1      0
    2      0
    3      0
    4      0
          ..
    214    0
    215    0
    216    0
    217    1
    218    1
    Name: Output, Length: 219, dtype: int64




```python
# 학습 데이터와 평가 데이터로 분리
from sklearn.model_selection import train_test_split
Train_X, Test_X, Train_Y, Test_Y = train_test_split(X, Y)

# 결측치 확인
Train_X.isnull().sum()
```




    Age         0
    Sex         0
    Cp          0
    Trestbps    0
    Chol        0
    Fbs         0
    Restecg     0
    Thalach     0
    Exang       0
    Oldpeak     0
    Slope       0
    Ca          4
    Thal        1
    dtype: int64



결측치가 거의 없어서 지워도 무방한 수치이지만, 새로 들어온 데이터에 결측이 있을 수도 있다는 도메인 지식이 있다고 가정해보자

그러면 지웠을때 새로들어온 데이터도 그대로 지워야 하기 때문에 그거를 막기 위해서 결측값을 대표값을 사용하기로 결정했다고 가정하자.

그러면 이제 평균 상관 계수 확인해야 한다. (주의: 모든 변수가 연속형이므로 가능한 접근)

수치가 높지 않다고 판단이 되야지 특징 간 관계가 크지 않고 그러면 대표값 대체 활용 가능 판단할 수 있기 때문이다.


```python
Train_X.corr().sum() / (len(Train_X.columns) - 1)
```




    Age         0.172571
    Sex         0.141409
    Cp          0.098236
    Trestbps    0.151973
    Chol        0.102163
    Fbs         0.131794
    Restecg     0.146327
    Thalach    -0.097278
    Exang       0.172573
    Oldpeak     0.189048
    Slope       0.144286
    Ca          0.160319
    Thal        0.193095
    dtype: float64



확인해보니 변수들 간에 관계가 크지 않다고 판단했다.


```python
# 대표값을 활용한 결측치 대체
from sklearn.impute import SimpleImputer

# SimpleImputer 인스턴스화
SI = SimpleImputer(strategy = 'mean')

# 학습
# fit을 하면 위에 Ca 변수의 대표값과 Thal 변수의 대표값을 계산할 수 있게 된다.
# 엄밀하게는 모든 피쳐에 대해 mean을 계산해서 기억하고 있을 것이다.
SI.fit(Train_X)

# 주의사항 : sklearn instance의 출력은 ndarray이므로 다시 DataFrame으로 바꿔줌
Train_X = pd.DataFrame(SI.transform(Train_X), columns = Train_X.columns)
Test_X = pd.DataFrame(SI.transform(Test_X), columns = Test_X.columns)
Train_X.isnull().sum()
```




    Age         0
    Sex         0
    Cp          0
    Trestbps    0
    Chol        0
    Fbs         0
    Restecg     0
    Thalach     0
    Exang       0
    Oldpeak     0
    Slope       0
    Ca          0
    Thal        0
    dtype: int64



복잡한 케이스 실습 :  다른 타입의 특징이 있는 경우


```python
df = pd.read_csv("saheart.csv")
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
      <th>Sbp</th>
      <th>Tobacco</th>
      <th>Ldl</th>
      <th>Adiposity</th>
      <th>Typea</th>
      <th>Obesity</th>
      <th>Alcohol</th>
      <th>Age</th>
      <th>Famhist</th>
      <th>Chd</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>214</td>
      <td>0.0</td>
      <td>5.98</td>
      <td>31.72</td>
      <td>64</td>
      <td>28.45</td>
      <td>0.00</td>
      <td>58</td>
      <td>0.0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>1</td>
      <td>164</td>
      <td>0.0</td>
      <td>3.17</td>
      <td>30.98</td>
      <td>44</td>
      <td>25.99</td>
      <td>43.20</td>
      <td>53</td>
      <td>1.0</td>
      <td>1</td>
    </tr>
    <tr>
      <td>2</td>
      <td>164</td>
      <td>0.0</td>
      <td>14.16</td>
      <td>36.85</td>
      <td>52</td>
      <td>28.50</td>
      <td>17.02</td>
      <td>55</td>
      <td>0.0</td>
      <td>1</td>
    </tr>
    <tr>
      <td>3</td>
      <td>206</td>
      <td>0.0</td>
      <td>4.17</td>
      <td>33.23</td>
      <td>69</td>
      <td>27.36</td>
      <td>6.17</td>
      <td>50</td>
      <td>0.0</td>
      <td>1</td>
    </tr>
    <tr>
      <td>4</td>
      <td>136</td>
      <td>0.0</td>
      <td>4.37</td>
      <td>20.22</td>
      <td>59</td>
      <td>25.12</td>
      <td>47.16</td>
      <td>31</td>
      <td>1.0</td>
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
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <td>457</td>
      <td>152</td>
      <td>10.1</td>
      <td>4.71</td>
      <td>24.65</td>
      <td>65</td>
      <td>26.21</td>
      <td>24.53</td>
      <td>57</td>
      <td>NaN</td>
      <td>0</td>
    </tr>
    <tr>
      <td>458</td>
      <td>162</td>
      <td>5.3</td>
      <td>7.95</td>
      <td>33.58</td>
      <td>58</td>
      <td>36.06</td>
      <td>8.23</td>
      <td>48</td>
      <td>1.0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>459</td>
      <td>126</td>
      <td>0.0</td>
      <td>5.98</td>
      <td>29.06</td>
      <td>56</td>
      <td>25.39</td>
      <td>11.52</td>
      <td>64</td>
      <td>1.0</td>
      <td>1</td>
    </tr>
    <tr>
      <td>460</td>
      <td>138</td>
      <td>2.0</td>
      <td>5.11</td>
      <td>31.40</td>
      <td>49</td>
      <td>27.25</td>
      <td>2.06</td>
      <td>64</td>
      <td>1.0</td>
      <td>1</td>
    </tr>
    <tr>
      <td>461</td>
      <td>114</td>
      <td>0.0</td>
      <td>1.94</td>
      <td>11.02</td>
      <td>54</td>
      <td>20.17</td>
      <td>38.98</td>
      <td>16</td>
      <td>0.0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>462 rows × 10 columns</p>
</div>




```python
# 특징과 라벨 분리
X = df.drop('Chd', axis = 1)
Y = df['Chd']
```


```python
# 학습 데이터와 평가 데이터로 분리
from sklearn.model_selection import train_test_split
Train_X, Test_X, Train_Y, Test_Y = train_test_split(X, Y)
```


```python
# 결측치 제거 확인
Train_X.isnull().sum()
# 결측치가 많지 않음
# 지워도 무방한 수치이지만, 새로 들어온 데이터에 결측이 있을 수도 있다는 도메인 지식이 있다고 가정
```




    Sbp          0
    Tobacco      0
    Ldl          0
    Adiposity    0
    Typea        0
    Obesity      8
    Alcohol      4
    Age          0
    Famhist      5
    dtype: int64




```python
# 평균 상관 계수 확인 (주의: 모든 변수가 연속형이므로 가능한 접근)
Train_X.corr().sum() / (len(Train_X.columns) - 1)

# 수치가 높지 않다고 판단 => 특징 간 관계가 크지 않음 => 대표값 대체 활용 가능 판단
```




    Sbp          0.326342
    Tobacco      0.205252
    Ldl          0.313818
    Adiposity    0.445875
    Typea        0.123615
    Obesity      0.371432
    Alcohol      0.231953
    Age          0.375101
    Famhist      0.259006
    dtype: float64



사실 엄밀하게 따지면 위에서 나온 상관계수는 틀린것이다. 데이터가 연속형과 범주형이 섞여있기 때문에 통계분석 (아노바 검정을 하거나 연속형일 경우 피어슨 상관계수를 구한다던가 하는 방식)으로 이 변수들간에 관계성을 구해야 하는게 맞다.

데이터를 설명하자면 Famhist라는게 범주형 변수이고, 그 외 변수들이 연속형 변수이다.

대표값을 평균을 사용할지, 최빈값을 사용할지 결정이 어렵기 때문에 결론적으로는 둘 다 사용해야 한다. 

따라서 데이터를 아래와 같이 분할해야 한다.


```python
Train_X_cate = Train_X[['Famhist']]
Train_X_cont = Train_X.drop('Famhist', axis = 1)

Test_X_cate = Test_X[['Famhist']]
Test_X_cont = Test_X.drop('Famhist', axis = 1)
```

그런 다음에 대표값을 활용한 결측치 대체한다.


```python
from sklearn.impute import SimpleImputer

# SimpleImputer 인스턴스화
SI_mode = SimpleImputer(strategy = 'most_frequent')
SI_mean = SimpleImputer(strategy = 'mean')

# 학습
SI_mode.fit(Train_X_cate)
SI_mean.fit(Train_X_cont)

# sklearn instance의 출력은 ndarray이므로 다시 DataFrame으로 바꿔줌
Train_X_cate = pd.DataFrame(SI_mode.transform(Train_X_cate),columns = Train_X_cate.columns)
Test_X_cate = pd.DataFrame(SI_mode.transform(Test_X_cate),columns = Test_X_cate.columns)
Train_X_cont = pd.DataFrame(SI_mean.transform(Train_X_cont),columns = Train_X_cont.columns)
Test_X_cont = pd.DataFrame(SI_mean.transform(Test_X_cont),columns = Test_X_cont.columns)

# 다시 두 데이터를 열단위로 이어붙여야 함
# 열단위로 붙일거기 때문에 axis가 1이고, 컬럼이름이 살아있어야 하기 때문에 ignore index를 false로 해야한다.
# concat 함수에서는 default로 ignore index를 false이기 때문에 따로 명시는 안해줬다.
Train_X = pd.concat([Train_X_cate, Train_X_cont], axis = 1)
Test_X = pd.concat([Test_X_cate, Test_X_cont], axis = 1)

# 결측제거 확인
Train_X.isnull().sum()
```




    Famhist      0
    Sbp          0
    Tobacco      0
    Ldl          0
    Adiposity    0
    Typea        0
    Obesity      0
    Alcohol      0
    Age          0
    dtype: int64



Tip. 이진형 변수와 연속형 변수만 포함된 경우에는 SI_mean만 사용하여 결측치를 평균으로 대체한 뒤에, 이진형 변수에 대해서만 round 처리를 하면 하나의 인스턴스만 활용할 수 있음

위와 같이 범주형과 연속형 처럼 따로 전처리 모델을 학습할 필요는 없다.

이진형 변수는 mean을 쓴다음에 거기에 round 처리를 해줘야 한다. 왜냐하면 이진형 변수의 평균이 결국에는 1의 비율과 같기 때문이다. 만약에 평균을 구했을때 0.4이면 0 이 더 많다는 소리이고, 0.7이다라고 하면 1이 더 많다는 얘기이다. 그러면 그 값을 바탕으로 round 시켜주면 0.5 이상이면 1로 바뀔테고 0.5미만이면 0으로 바뀔것이다. 

실습 2. 시계열 결측치 대체하기

데이터를 train_test_split을 이용하여 임의로 분할한 경우에는 적용이 불가능하다.

왜냐하면 train_test_split를 쓰면 시간순서가 꼬이기 때문이다.

분할하기 전에 결측치 대체가 가능한 유일한 케이스다. 왜냐하면 테스트 데이터에 대한 결측을 채우게 되면 테스트 데이터 내에서만 알아서 결측값이 채워지고, 마찬가지로 트레인 데이터에 대한 결측도 트레인 데이터 내에서만 알아서 결측이 채워지기 때문이다.


```python
df = pd.read_excel("AirQuality.xlsx")
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
      <th>Date</th>
      <th>Time</th>
      <th>CO(GT)</th>
      <th>PT08.S1(CO)</th>
      <th>NMHC(GT)</th>
      <th>C6H6(GT)</th>
      <th>PT08.S2(NMHC)</th>
      <th>NOx(GT)</th>
      <th>PT08.S3(NOx)</th>
      <th>NO2(GT)</th>
      <th>PT08.S4(NO2)</th>
      <th>PT08.S5(O3)</th>
      <th>T</th>
      <th>RH</th>
      <th>AH</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>2004-03-10</td>
      <td>18:00:00</td>
      <td>2.6</td>
      <td>1360.000000</td>
      <td>150.0</td>
      <td>11.881723</td>
      <td>1045.50</td>
      <td>166.0</td>
      <td>1056.250000</td>
      <td>113.0</td>
      <td>1692.00</td>
      <td>1267.500000</td>
      <td>13.600</td>
      <td>48.875001</td>
      <td>0.757754</td>
    </tr>
    <tr>
      <td>1</td>
      <td>2004-03-10</td>
      <td>19:00:00</td>
      <td>2.0</td>
      <td>1292.250000</td>
      <td>112.0</td>
      <td>9.397165</td>
      <td>954.75</td>
      <td>103.0</td>
      <td>1173.750000</td>
      <td>92.0</td>
      <td>1558.75</td>
      <td>972.250000</td>
      <td>13.300</td>
      <td>47.700000</td>
      <td>0.725487</td>
    </tr>
    <tr>
      <td>2</td>
      <td>2004-03-10</td>
      <td>20:00:00</td>
      <td>2.2</td>
      <td>1402.000000</td>
      <td>88.0</td>
      <td>8.997817</td>
      <td>939.25</td>
      <td>131.0</td>
      <td>1140.000000</td>
      <td>114.0</td>
      <td>1554.50</td>
      <td>1074.000000</td>
      <td>11.900</td>
      <td>53.975000</td>
      <td>0.750239</td>
    </tr>
    <tr>
      <td>3</td>
      <td>2004-03-10</td>
      <td>21:00:00</td>
      <td>2.2</td>
      <td>1375.500000</td>
      <td>80.0</td>
      <td>9.228796</td>
      <td>948.25</td>
      <td>172.0</td>
      <td>1092.000000</td>
      <td>122.0</td>
      <td>1583.75</td>
      <td>1203.250000</td>
      <td>11.000</td>
      <td>60.000000</td>
      <td>0.786713</td>
    </tr>
    <tr>
      <td>4</td>
      <td>2004-03-10</td>
      <td>22:00:00</td>
      <td>1.6</td>
      <td>1272.250000</td>
      <td>51.0</td>
      <td>6.518224</td>
      <td>835.50</td>
      <td>131.0</td>
      <td>1205.000000</td>
      <td>116.0</td>
      <td>1490.00</td>
      <td>1110.000000</td>
      <td>11.150</td>
      <td>59.575001</td>
      <td>0.788794</td>
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
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <td>505</td>
      <td>2004-03-31</td>
      <td>19:00:00</td>
      <td>2.7</td>
      <td>1254.333333</td>
      <td>226.0</td>
      <td>11.524820</td>
      <td>1033.00</td>
      <td>142.0</td>
      <td>818.666667</td>
      <td>107.0</td>
      <td>1669.00</td>
      <td>1068.333333</td>
      <td>14.300</td>
      <td>52.933333</td>
      <td>0.858103</td>
    </tr>
    <tr>
      <td>506</td>
      <td>2004-03-31</td>
      <td>20:00:00</td>
      <td>2.3</td>
      <td>1198.000000</td>
      <td>221.0</td>
      <td>9.345221</td>
      <td>952.75</td>
      <td>131.0</td>
      <td>881.500000</td>
      <td>105.0</td>
      <td>1588.25</td>
      <td>1018.500000</td>
      <td>13.175</td>
      <td>55.974999</td>
      <td>0.844558</td>
    </tr>
    <tr>
      <td>507</td>
      <td>2004-03-31</td>
      <td>21:00:00</td>
      <td>1.5</td>
      <td>1060.250000</td>
      <td>109.0</td>
      <td>5.467274</td>
      <td>786.50</td>
      <td>81.0</td>
      <td>1044.000000</td>
      <td>91.0</td>
      <td>1418.00</td>
      <td>833.000000</td>
      <td>12.650</td>
      <td>57.000000</td>
      <td>0.831531</td>
    </tr>
    <tr>
      <td>508</td>
      <td>2004-03-31</td>
      <td>22:00:00</td>
      <td>1.4</td>
      <td>1050.000000</td>
      <td>105.0</td>
      <td>5.101134</td>
      <td>768.50</td>
      <td>60.0</td>
      <td>1078.250000</td>
      <td>79.0</td>
      <td>1408.75</td>
      <td>758.500000</td>
      <td>12.275</td>
      <td>57.750001</td>
      <td>0.822377</td>
    </tr>
    <tr>
      <td>509</td>
      <td>2004-03-31</td>
      <td>23:00:00</td>
      <td>1.2</td>
      <td>1029.250000</td>
      <td>84.0</td>
      <td>4.804320</td>
      <td>753.50</td>
      <td>57.0</td>
      <td>1097.500000</td>
      <td>79.0</td>
      <td>1395.25</td>
      <td>749.000000</td>
      <td>11.975</td>
      <td>58.450000</td>
      <td>0.816387</td>
    </tr>
  </tbody>
</table>
<p>510 rows × 15 columns</p>
</div>




```python
df.isnull().sum()
```




    Date              0
    Time              0
    CO(GT)            1
    PT08.S1(CO)       2
    NMHC(GT)          2
    C6H6(GT)         10
    PT08.S2(NMHC)     4
    NOx(GT)           6
    PT08.S3(NOx)      7
    NO2(GT)           6
    PT08.S4(NO2)      6
    PT08.S5(O3)       1
    T                 0
    RH                0
    AH                0
    dtype: int64



df를 트레인 테스트로 나누지 않고 원본 그대로에 대해서 fillna로 채우게 된다.

먼저 ffill로 이전값으로 채우고, 맨앞에 있는 값들은 안채워질테니 bfill로 채운다.


```python
df = df.fillna(method = 'ffill').fillna(method = 'bfill')
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
      <th>Date</th>
      <th>Time</th>
      <th>CO(GT)</th>
      <th>PT08.S1(CO)</th>
      <th>NMHC(GT)</th>
      <th>C6H6(GT)</th>
      <th>PT08.S2(NMHC)</th>
      <th>NOx(GT)</th>
      <th>PT08.S3(NOx)</th>
      <th>NO2(GT)</th>
      <th>PT08.S4(NO2)</th>
      <th>PT08.S5(O3)</th>
      <th>T</th>
      <th>RH</th>
      <th>AH</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>2004-03-10</td>
      <td>18:00:00</td>
      <td>2.6</td>
      <td>1360.000000</td>
      <td>150.0</td>
      <td>11.881723</td>
      <td>1045.50</td>
      <td>166.0</td>
      <td>1056.250000</td>
      <td>113.0</td>
      <td>1692.00</td>
      <td>1267.500000</td>
      <td>13.600</td>
      <td>48.875001</td>
      <td>0.757754</td>
    </tr>
    <tr>
      <td>1</td>
      <td>2004-03-10</td>
      <td>19:00:00</td>
      <td>2.0</td>
      <td>1292.250000</td>
      <td>112.0</td>
      <td>9.397165</td>
      <td>954.75</td>
      <td>103.0</td>
      <td>1173.750000</td>
      <td>92.0</td>
      <td>1558.75</td>
      <td>972.250000</td>
      <td>13.300</td>
      <td>47.700000</td>
      <td>0.725487</td>
    </tr>
    <tr>
      <td>2</td>
      <td>2004-03-10</td>
      <td>20:00:00</td>
      <td>2.2</td>
      <td>1402.000000</td>
      <td>88.0</td>
      <td>8.997817</td>
      <td>939.25</td>
      <td>131.0</td>
      <td>1140.000000</td>
      <td>114.0</td>
      <td>1554.50</td>
      <td>1074.000000</td>
      <td>11.900</td>
      <td>53.975000</td>
      <td>0.750239</td>
    </tr>
    <tr>
      <td>3</td>
      <td>2004-03-10</td>
      <td>21:00:00</td>
      <td>2.2</td>
      <td>1375.500000</td>
      <td>80.0</td>
      <td>9.228796</td>
      <td>948.25</td>
      <td>172.0</td>
      <td>1092.000000</td>
      <td>122.0</td>
      <td>1583.75</td>
      <td>1203.250000</td>
      <td>11.000</td>
      <td>60.000000</td>
      <td>0.786713</td>
    </tr>
    <tr>
      <td>4</td>
      <td>2004-03-10</td>
      <td>22:00:00</td>
      <td>1.6</td>
      <td>1272.250000</td>
      <td>51.0</td>
      <td>6.518224</td>
      <td>835.50</td>
      <td>131.0</td>
      <td>1205.000000</td>
      <td>116.0</td>
      <td>1490.00</td>
      <td>1110.000000</td>
      <td>11.150</td>
      <td>59.575001</td>
      <td>0.788794</td>
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
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <td>505</td>
      <td>2004-03-31</td>
      <td>19:00:00</td>
      <td>2.7</td>
      <td>1254.333333</td>
      <td>226.0</td>
      <td>11.524820</td>
      <td>1033.00</td>
      <td>142.0</td>
      <td>818.666667</td>
      <td>107.0</td>
      <td>1669.00</td>
      <td>1068.333333</td>
      <td>14.300</td>
      <td>52.933333</td>
      <td>0.858103</td>
    </tr>
    <tr>
      <td>506</td>
      <td>2004-03-31</td>
      <td>20:00:00</td>
      <td>2.3</td>
      <td>1198.000000</td>
      <td>221.0</td>
      <td>9.345221</td>
      <td>952.75</td>
      <td>131.0</td>
      <td>881.500000</td>
      <td>105.0</td>
      <td>1588.25</td>
      <td>1018.500000</td>
      <td>13.175</td>
      <td>55.974999</td>
      <td>0.844558</td>
    </tr>
    <tr>
      <td>507</td>
      <td>2004-03-31</td>
      <td>21:00:00</td>
      <td>1.5</td>
      <td>1060.250000</td>
      <td>109.0</td>
      <td>5.467274</td>
      <td>786.50</td>
      <td>81.0</td>
      <td>1044.000000</td>
      <td>91.0</td>
      <td>1418.00</td>
      <td>833.000000</td>
      <td>12.650</td>
      <td>57.000000</td>
      <td>0.831531</td>
    </tr>
    <tr>
      <td>508</td>
      <td>2004-03-31</td>
      <td>22:00:00</td>
      <td>1.4</td>
      <td>1050.000000</td>
      <td>105.0</td>
      <td>5.101134</td>
      <td>768.50</td>
      <td>60.0</td>
      <td>1078.250000</td>
      <td>79.0</td>
      <td>1408.75</td>
      <td>758.500000</td>
      <td>12.275</td>
      <td>57.750001</td>
      <td>0.822377</td>
    </tr>
    <tr>
      <td>509</td>
      <td>2004-03-31</td>
      <td>23:00:00</td>
      <td>1.2</td>
      <td>1029.250000</td>
      <td>84.0</td>
      <td>4.804320</td>
      <td>753.50</td>
      <td>57.0</td>
      <td>1097.500000</td>
      <td>79.0</td>
      <td>1395.25</td>
      <td>749.000000</td>
      <td>11.975</td>
      <td>58.450000</td>
      <td>0.816387</td>
    </tr>
  </tbody>
</table>
<p>510 rows × 15 columns</p>
</div>




```python
df.isnull().sum()
```




    Date             0
    Time             0
    CO(GT)           0
    PT08.S1(CO)      0
    NMHC(GT)         0
    C6H6(GT)         0
    PT08.S2(NMHC)    0
    NOx(GT)          0
    PT08.S3(NOx)     0
    NO2(GT)          0
    PT08.S4(NO2)     0
    PT08.S5(O3)      0
    T                0
    RH               0
    AH               0
    dtype: int64



시계열 변수도 한 변수에 결측값이 많다면 이전값을 채운다고 했을때 그 이전값이 상당히 멀리떨어진 값일 수도 있다. 이런 경우에는 근처값을 사용해서 결측을 채울 수 없다. 

특히 한 컬럼에 결측값이 연속해서 많이 쏠린게 있다면 `np.cumsum` 함수를 응용해서 연속으로 얼마나 결측이 발생했는지 사전에 잘 체크해야 한다.
