---
layout: post
title: "선형회귀모델 구현 팀프로젝트 결과"
tags: [머신러닝, 선형회귀분석, 팀프로젝트]
comments: true
---

House Price 예측을 위한 선형회귀모델 구현

```python
# 필요한 패키지 import 하는 코드
from sklearn.preprocessing import LabelEncoder
%matplotlib inline
import numpy as np
import matplotlib as mpl
import matplotlib.pyplot as plt
import pandas as pd
from patsy import *
```

#  회귀분석 프로젝트 수행결과

> House Price 예측을 위한 선형회귀모델 구현

## [  개 요 ]

#### ㅇ 선정 컨텐츠 : Kaggle House Prices: Advanced Regression Techniques
   * 컨텐츠 URL : https://www.kaggle.com/c/house-prices-advanced-regression-techniques
        
#### ㅇ 수행과제 목적 : 종속변수 'SalePrice'에 대한 선형회귀 예측모델 구현

#### ㅇ Dataset 구성 :
    1) 학습을 위한 train.csv 와 모델 TEST를 위한 test.csv

    2) column : ID, 종속변수 SalePrice, 독립변수 HeatingQC(house의 난방 퀄리티), YearBuilt(house가 지어진 년도) 등 총 80개로 구성
    
    3) row : 1460개 (test.csv : 1459개)
    
#### ㅇ 선형회귀 예측모델 구현요약

- 모델 성능측정 결과

    1) cross validation
    
      -> 평균 R-squared 0.92

    2) kaggle competition 
    
    
     - 정규선형회귀 미적용 모델 : 0.14787 Score , 4400 그룹 중 2637 rank 

<img src=https://github.com/minman2115/linear_regression_analysis_team_project/blob/master/1.png>

      - 정규선형회귀 적용모델 : 0.14632 Score / 4400 그룹 중 2560 rank 
      (alpha = 0.00009를 적용한 LASSO 모델)

<img src=https://github.com/minman2115/linear_regression_analysis_team_project/blob/master/2.png>

- 모델구성 기준

    1) condition number를 높이는 변수 제거
  
    2) 부분회귀플롯으로 상대적으로 높은 상관관계를 보이는 독립변수 채택

## [ 목 차 ]

### 1. '데이터 탐색 및 전처리' 결과

### 2. '선형회귀 예측모델 구현' 결과

----------------------------------------------------

## [  단계별 수행결과   ]

## 1. '데이터 탐색 및  전처리' 결과

#### * 칼럼별 description
- 집의 전체적인 모양새 (건축양식, 재료, 지어진 연도 ,지붕 스타일, 평가 점수, 울타리, 테라스 종류, 테라스 면적)
- 집 세부사항 (면적, 방 개수, 화장실 종류별 개수, 부엌 개수)
- Utility (난방여부, 난방방식, 전기, 벽난로)
- 주변위치를 고려한 특징 (단지, 도로까지의 거리, 경사, 인접도로 종류)
- 차고 (허용 대수, 면적, 연식, 평가 점수)
- 기타 사항 (매매 방식, 팔린 연도, 등)


#### 1) 범주형 col

- 순수한 범주형 col

MasVnrType


```python
## train 데이터 로드
df = pd.read_csv("train.csv")

dmatrix("0+ C(MasVnrType)",df)
```




    DesignMatrix with shape (1452, 4)
      Columns:
        ['C(MasVnrType)[BrkCmn]',
         'C(MasVnrType)[BrkFace]',
         'C(MasVnrType)[None]',
         'C(MasVnrType)[Stone]']
      Terms:
        'C(MasVnrType)' (columns 0:4)
      (to view full data, use np.asarray(this_obj))



- ordinal한 범주형 col

GarageQual


```python
dmatrix("0+ C(GarageQual)",df)
```




    DesignMatrix with shape (1379, 5)
      Columns:
        ['C(GarageQual)[Ex]',
         'C(GarageQual)[Fa]',
         'C(GarageQual)[Gd]',
         'C(GarageQual)[Po]',
         'C(GarageQual)[TA]']
      Terms:
        'C(GarageQual)' (columns 0:5)
      (to view full data, use np.asarray(this_obj))




#### 2) 실수형 col

- 실제 실수형 col

GarageArea


```python
df['GarageArea'].tail()
```




    1455    460
    1456    500
    1457    252
    1458    240
    1459    276
    Name: GarageArea, dtype: int64



- 순수한 범주형 col

MSSubclass


```python
dmatrix("0+ C(MSSubClass)",df)
```




    DesignMatrix with shape (1460, 15)
      Columns:
        ['C(MSSubClass)[20]',
         'C(MSSubClass)[30]',
         'C(MSSubClass)[40]',
         'C(MSSubClass)[45]',
         'C(MSSubClass)[50]',
         'C(MSSubClass)[60]',
         'C(MSSubClass)[70]',
         'C(MSSubClass)[75]',
         'C(MSSubClass)[80]',
         'C(MSSubClass)[85]',
         'C(MSSubClass)[90]',
         'C(MSSubClass)[120]',
         'C(MSSubClass)[160]',
         'C(MSSubClass)[180]',
         'C(MSSubClass)[190]']
      Terms:
        'C(MSSubClass)' (columns 0:15)
      (to view full data, use np.asarray(this_obj))



### [ Issue ]

#### 1) 결측값은 어떻게 처리할 것인가

- 결측값 변수 변황 : 'PoolQC','MiscFeature' 등 19개


```python
total = df.isnull().sum().sort_values(ascending=False)
percent = (df.isnull().sum()/df.isnull().count()).sort_values(ascending=False)
missing_data = pd.concat([total, percent], axis=1, keys=['Total', 'Percent'])
missing_data.head(19)
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
      <th>Total</th>
      <th>Percent</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>PoolQC</th>
      <td>1453</td>
      <td>0.995205</td>
    </tr>
    <tr>
      <th>MiscFeature</th>
      <td>1406</td>
      <td>0.963014</td>
    </tr>
    <tr>
      <th>Alley</th>
      <td>1369</td>
      <td>0.937671</td>
    </tr>
    <tr>
      <th>Fence</th>
      <td>1179</td>
      <td>0.807534</td>
    </tr>
    <tr>
      <th>FireplaceQu</th>
      <td>690</td>
      <td>0.472603</td>
    </tr>
    <tr>
      <th>LotFrontage</th>
      <td>259</td>
      <td>0.177397</td>
    </tr>
    <tr>
      <th>GarageCond</th>
      <td>81</td>
      <td>0.055479</td>
    </tr>
    <tr>
      <th>GarageType</th>
      <td>81</td>
      <td>0.055479</td>
    </tr>
    <tr>
      <th>GarageYrBlt</th>
      <td>81</td>
      <td>0.055479</td>
    </tr>
    <tr>
      <th>GarageFinish</th>
      <td>81</td>
      <td>0.055479</td>
    </tr>
    <tr>
      <th>GarageQual</th>
      <td>81</td>
      <td>0.055479</td>
    </tr>
    <tr>
      <th>BsmtExposure</th>
      <td>38</td>
      <td>0.026027</td>
    </tr>
    <tr>
      <th>BsmtFinType2</th>
      <td>38</td>
      <td>0.026027</td>
    </tr>
    <tr>
      <th>BsmtFinType1</th>
      <td>37</td>
      <td>0.025342</td>
    </tr>
    <tr>
      <th>BsmtCond</th>
      <td>37</td>
      <td>0.025342</td>
    </tr>
    <tr>
      <th>BsmtQual</th>
      <td>37</td>
      <td>0.025342</td>
    </tr>
    <tr>
      <th>MasVnrArea</th>
      <td>8</td>
      <td>0.005479</td>
    </tr>
    <tr>
      <th>MasVnrType</th>
      <td>8</td>
      <td>0.005479</td>
    </tr>
    <tr>
      <th>Electrical</th>
      <td>1</td>
      <td>0.000685</td>
    </tr>
  </tbody>
</table>
</div>



- 'MiscVal','MiscFeature','BsmtExposure','BsmtFinType2' 총 4개 변수 
   
   -> 최빈값으로 대체


```python
df['BsmtExposure'][948] = 'No'
df['BsmtFinType2'][332] = 'Unf'

for i in range(len(df['MiscVal']+1)):
    if df['MiscVal'].values[i] == 0 and df['MiscFeature'].values[i] == 'Shed':
        df['MiscVal'].values[i] = df['MiscVal'][df['MiscVal'] != 0].mode()[0]
```

- 'LotFrontage' 변수

   -> 중앙값으로 대체


```python
df['LotFrontage'].fillna(df.groupby('Neighborhood')['LotFrontage']\
                              .transform('median'), inplace=True)
```

- 그 외 결측값

   -> 범주형데이터는 'None', 실수형 데이터는 '0'으로 대체


```python
# columns where NaN values have meaning e.g. no pool etc.
cols_fillna = ['PoolQC','MiscFeature','Alley','Fence','MasVnrType','FireplaceQu',
               'GarageQual','GarageCond','GarageFinish','GarageType', 'Electrical',
               'KitchenQual', 'SaleType', 'Functional', 'Exterior2nd', 'Exterior1st',
               'BsmtExposure','BsmtCond','BsmtQual','BsmtFinType1','BsmtFinType2',
               'MSZoning', 'Utilities']

# replace 'NaN' with 'None' in these columns
for col in cols_fillna:
    df[col].fillna('None',inplace=True)

df.fillna(0, inplace=True)
```

#### 2) 아웃라이어 제거



```python
plt.scatter(df["GrLivArea"], df["SalePrice"])
plt.scatter(df["GrLivArea"][523], df["SalePrice"][523], s=200, c='r', alpha = 0.5)
plt.scatter(df["GrLivArea"][1298], df["SalePrice"][1298], s=200, c='r', alpha = 0.5)
```




    <matplotlib.collections.PathCollection at 0x1e7212cf588>




<img src=https://github.com/minman2115/linear_regression_analysis_team_project/blob/master/boston_house_price_27_1.png>



```python
df = df.drop(df.index[1298])
df = df.drop(df.index[523])
```

### V 소결론 : 결측값 및 아웃라이어 제거로 선형회귀 예측모델의 구현을 위한 사전작업 수행

-----------------------------------------

## 2. '선형회귀 예측모델 구현' 결과

### [ Issue ]

#### 1) 80개에 달하는 독립변수 중 어떤 변수를 모델에 활용할 것인가
- 종속변수와 상관관계가 있는 독립변수를 탐색
  
  -> 80개에 달하는 칼럼에 대해 순차적으로 탐색 & 토의
  
  
- boxplot, correlation heatmap을 이용 saleprice와 상관관계가 있는 독립변수를 채택
- 부분회귀플롯을 이용 상관관계가 있는 독립변수를 채택

#### 2) 최상의 Perfomance 성능구현을 위해 어떻게 할 것인가
- '독립변수 구성 재검토 <-> 모델구성' 반복시행으로 모델의 performance 향상시도
- kaggle contest summit으로 실질적인 모델의 performance 확인

#### 3) 모델의 신뢰성을 의심할만한 요소가 있지는 않은가
- 잔차정규성 검증 등 모델의 신뢰성 확인
- 파이썬코드 구현상 오류발생여부 최종확인

### [ 선형회귀 예측모델 구현결과 ] 
-> 종속변수 SalePrice에 대한 StatsModels R스타일 선형회귀분석모델 도출

<img src=https://github.com/minman2115/linear_regression_analysis_team_project/blob/master/3.png>

#### 1) 모델구성 :  'OverallQual' 등 15개의 독립변수 조합

- np.log(SalePrice) (종속변수) 
 
  -> 데이터가 정규분포로 가까워지도록 종속변수 제곱근 변수변환 조치


- scale(MasVnrArea) 등 실수형 데이터 8건 

  -> 변수별 데이터 수치범위가 상이하기 때문에 scale처리
  

- C(Neighborhood) 등 범주형 데이터 4건 

  -> 'C()' 연산자를 적용하여 범주형 데이터임을 명시적으로 지정
  
  
- C(OverallQual, Poly), C(GarageQual, Poly), C(BsmtExposure, Poly) 총 세건

  -> ordinal한 범주형 변수를 활용하기 위해 'category' 데이터 타입으로 변경, 'statsmodel Orthogonal Polynomial Coding' 처리


```python
## OverallQual 변수 데이터 타입 'category' 으로 변경 
df['OverallQual'] = df["OverallQual"].astype('category')

## GarageQual 데이터 타입 'category' 으로 변경, 'statsmodel Orthogonal Polynomial Coding' 처리
df["GarageQual"] = df["GarageQual"].astype('category')
df["GarageQual"].cat.reorder_categories(['None', 'Po', 'Fa', 'TA', 'Gd', 'Ex'], inplace = True)
le = LabelEncoder()
le.fit(['None', 'Po', 'Fa', 'TA', 'Gd', 'Ex'])
GarageQual_dict = {'None': 0, 'Po': 1, 'Fa' : 2, 'TA' : 3, 'Gd' : 4, 'Ex' :5}
df["GarageQual"] = np.vectorize(GarageQual_dict.get)(df["GarageQual"])

## BsmtExposure 데이터 타입 'category' 으로 변경, 'statsmodel Orthogonal Polynomial Coding' 처리
df["BsmtExposure"] = df["BsmtExposure"].astype('category')
df['BsmtExposure'].cat.reorder_categories(['None', 'No', 'Mn', 'Av', 'Gd'], inplace=True)
le.fit(['None', 'No', 'Mn', 'Av', 'Gd'])
BsmtExposure_dict = {'None' : 0,'No' : 1, 'Mn' : 2, 'Av': 3, 'Gd' : 4}
df['BsmtExposure'] = np.vectorize(BsmtExposure_dict.get)(df['BsmtExposure'])
```

#### 2) 모델의 성능 확인

- model summary


```python
model = sm.OLS.from_formula("np.log(SalePrice) ~ 0 + C(OverallQual, Poly) \
                            + scale(MasVnrArea) + scale(LotArea) + C(Neighborhood) + C(BsmtExposure, Poly) + scale(YearRemodAdd) \
                            + C(GarageQual, Poly) + C(MasVnrType) + C(CentralAir) + scale(GrLivArea) + scale(GarageArea) \
                            + scale(FullBath) + scale(Fireplaces) + C(PavedDrive) + scale(GarageArea)", df)
result = model.fit()
result.summary()
```




<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>    <td>np.log(SalePrice)</td> <th>  R-squared:         </th> <td>   0.892</td>
</tr>
<tr>
  <th>Model:</th>                   <td>OLS</td>        <th>  Adj. R-squared:    </th> <td>   0.888</td>
</tr>
<tr>
  <th>Method:</th>             <td>Least Squares</td>   <th>  F-statistic:       </th> <td>   211.6</td>
</tr>
<tr>
  <th>Date:</th>             <td>Sat, 17 Nov 2018</td>  <th>  Prob (F-statistic):</th>  <td>  0.00</td> 
</tr>
<tr>
  <th>Time:</th>                 <td>00:19:27</td>      <th>  Log-Likelihood:    </th> <td>  894.53</td>
</tr>
<tr>
  <th>No. Observations:</th>      <td>  1458</td>       <th>  AIC:               </th> <td>  -1677.</td>
</tr>
<tr>
  <th>Df Residuals:</th>          <td>  1402</td>       <th>  BIC:               </th> <td>  -1381.</td>
</tr>
<tr>
  <th>Df Model:</th>              <td>    55</td>       <th>                     </th>     <td> </td>   
</tr>
<tr>
  <th>Covariance Type:</th>      <td>nonrobust</td>     <th>                     </th>     <td> </td>   
</tr>
</table>
<table class="simpletable">
<tr>
                 <td></td>                    <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>C(OverallQual, Poly).Constant</th>   <td>   11.6806</td> <td>    0.058</td> <td>  203.035</td> <td> 0.000</td> <td>   11.568</td> <td>   11.793</td>
</tr>
<tr>
  <th>C(OverallQual, Poly).Linear</th>     <td>    0.9123</td> <td>    0.068</td> <td>   13.354</td> <td> 0.000</td> <td>    0.778</td> <td>    1.046</td>
</tr>
<tr>
  <th>C(OverallQual, Poly).Quadratic</th>  <td>   -0.0696</td> <td>    0.057</td> <td>   -1.213</td> <td> 0.225</td> <td>   -0.182</td> <td>    0.043</td>
</tr>
<tr>
  <th>C(OverallQual, Poly).Cubic</th>      <td>    0.0484</td> <td>    0.050</td> <td>    0.976</td> <td> 0.329</td> <td>   -0.049</td> <td>    0.146</td>
</tr>
<tr>
  <th>C(OverallQual, Poly)^4</th>          <td>    0.0293</td> <td>    0.049</td> <td>    0.595</td> <td> 0.552</td> <td>   -0.067</td> <td>    0.126</td>
</tr>
<tr>
  <th>C(OverallQual, Poly)^5</th>          <td>   -0.1488</td> <td>    0.047</td> <td>   -3.169</td> <td> 0.002</td> <td>   -0.241</td> <td>   -0.057</td>
</tr>
<tr>
  <th>C(OverallQual, Poly)^6</th>          <td>    0.0401</td> <td>    0.040</td> <td>    1.014</td> <td> 0.311</td> <td>   -0.038</td> <td>    0.118</td>
</tr>
<tr>
  <th>C(OverallQual, Poly)^7</th>          <td>   -0.0516</td> <td>    0.029</td> <td>   -1.795</td> <td> 0.073</td> <td>   -0.108</td> <td>    0.005</td>
</tr>
<tr>
  <th>C(OverallQual, Poly)^8</th>          <td>    0.0015</td> <td>    0.018</td> <td>    0.083</td> <td> 0.934</td> <td>   -0.034</td> <td>    0.037</td>
</tr>
<tr>
  <th>C(OverallQual, Poly)^9</th>          <td>   -0.0068</td> <td>    0.010</td> <td>   -0.662</td> <td> 0.508</td> <td>   -0.027</td> <td>    0.013</td>
</tr>
<tr>
  <th>C(Neighborhood)[T.Blueste]</th>      <td>   -0.1260</td> <td>    0.101</td> <td>   -1.244</td> <td> 0.214</td> <td>   -0.325</td> <td>    0.073</td>
</tr>
<tr>
  <th>C(Neighborhood)[T.BrDale]</th>       <td>   -0.2406</td> <td>    0.049</td> <td>   -4.861</td> <td> 0.000</td> <td>   -0.338</td> <td>   -0.144</td>
</tr>
<tr>
  <th>C(Neighborhood)[T.BrkSide]</th>      <td>   -0.0408</td> <td>    0.040</td> <td>   -1.010</td> <td> 0.313</td> <td>   -0.120</td> <td>    0.038</td>
</tr>
<tr>
  <th>C(Neighborhood)[T.ClearCr]</th>      <td>    0.0475</td> <td>    0.044</td> <td>    1.070</td> <td> 0.285</td> <td>   -0.040</td> <td>    0.135</td>
</tr>
<tr>
  <th>C(Neighborhood)[T.CollgCr]</th>      <td>    0.0353</td> <td>    0.035</td> <td>    0.997</td> <td> 0.319</td> <td>   -0.034</td> <td>    0.105</td>
</tr>
<tr>
  <th>C(Neighborhood)[T.Crawfor]</th>      <td>    0.1072</td> <td>    0.040</td> <td>    2.673</td> <td> 0.008</td> <td>    0.029</td> <td>    0.186</td>
</tr>
<tr>
  <th>C(Neighborhood)[T.Edwards]</th>      <td>   -0.0625</td> <td>    0.038</td> <td>   -1.629</td> <td> 0.104</td> <td>   -0.138</td> <td>    0.013</td>
</tr>
<tr>
  <th>C(Neighborhood)[T.Gilbert]</th>      <td>    0.0115</td> <td>    0.037</td> <td>    0.308</td> <td> 0.758</td> <td>   -0.062</td> <td>    0.085</td>
</tr>
<tr>
  <th>C(Neighborhood)[T.IDOTRR]</th>       <td>   -0.1976</td> <td>    0.043</td> <td>   -4.547</td> <td> 0.000</td> <td>   -0.283</td> <td>   -0.112</td>
</tr>
<tr>
  <th>C(Neighborhood)[T.MeadowV]</th>      <td>   -0.2414</td> <td>    0.049</td> <td>   -4.938</td> <td> 0.000</td> <td>   -0.337</td> <td>   -0.146</td>
</tr>
<tr>
  <th>C(Neighborhood)[T.Mitchel]</th>      <td>   -0.0114</td> <td>    0.040</td> <td>   -0.286</td> <td> 0.775</td> <td>   -0.090</td> <td>    0.067</td>
</tr>
<tr>
  <th>C(Neighborhood)[T.NAmes]</th>        <td>   -0.0106</td> <td>    0.037</td> <td>   -0.288</td> <td> 0.773</td> <td>   -0.083</td> <td>    0.061</td>
</tr>
<tr>
  <th>C(Neighborhood)[T.NPkVill]</th>      <td>   -0.0386</td> <td>    0.058</td> <td>   -0.669</td> <td> 0.503</td> <td>   -0.152</td> <td>    0.074</td>
</tr>
<tr>
  <th>C(Neighborhood)[T.NWAmes]</th>       <td>    0.0048</td> <td>    0.038</td> <td>    0.127</td> <td> 0.899</td> <td>   -0.070</td> <td>    0.079</td>
</tr>
<tr>
  <th>C(Neighborhood)[T.NoRidge]</th>      <td>    0.1050</td> <td>    0.042</td> <td>    2.505</td> <td> 0.012</td> <td>    0.023</td> <td>    0.187</td>
</tr>
<tr>
  <th>C(Neighborhood)[T.NridgHt]</th>      <td>    0.0983</td> <td>    0.039</td> <td>    2.522</td> <td> 0.012</td> <td>    0.022</td> <td>    0.175</td>
</tr>
<tr>
  <th>C(Neighborhood)[T.OldTown]</th>      <td>   -0.1465</td> <td>    0.039</td> <td>   -3.803</td> <td> 0.000</td> <td>   -0.222</td> <td>   -0.071</td>
</tr>
<tr>
  <th>C(Neighborhood)[T.SWISU]</th>        <td>   -0.0628</td> <td>    0.046</td> <td>   -1.377</td> <td> 0.169</td> <td>   -0.152</td> <td>    0.027</td>
</tr>
<tr>
  <th>C(Neighborhood)[T.Sawyer]</th>       <td>   -0.0202</td> <td>    0.039</td> <td>   -0.519</td> <td> 0.604</td> <td>   -0.097</td> <td>    0.056</td>
</tr>
<tr>
  <th>C(Neighborhood)[T.SawyerW]</th>      <td>    0.0129</td> <td>    0.038</td> <td>    0.335</td> <td> 0.738</td> <td>   -0.063</td> <td>    0.088</td>
</tr>
<tr>
  <th>C(Neighborhood)[T.Somerst]</th>      <td>    0.0578</td> <td>    0.038</td> <td>    1.541</td> <td> 0.123</td> <td>   -0.016</td> <td>    0.131</td>
</tr>
<tr>
  <th>C(Neighborhood)[T.StoneBr]</th>      <td>    0.1551</td> <td>    0.044</td> <td>    3.496</td> <td> 0.000</td> <td>    0.068</td> <td>    0.242</td>
</tr>
<tr>
  <th>C(Neighborhood)[T.Timber]</th>       <td>    0.0276</td> <td>    0.041</td> <td>    0.674</td> <td> 0.500</td> <td>   -0.053</td> <td>    0.108</td>
</tr>
<tr>
  <th>C(Neighborhood)[T.Veenker]</th>      <td>    0.1331</td> <td>    0.054</td> <td>    2.481</td> <td> 0.013</td> <td>    0.028</td> <td>    0.238</td>
</tr>
<tr>
  <th>C(BsmtExposure, Poly).Linear</th>    <td>    0.1605</td> <td>    0.018</td> <td>    8.856</td> <td> 0.000</td> <td>    0.125</td> <td>    0.196</td>
</tr>
<tr>
  <th>C(BsmtExposure, Poly).Quadratic</th> <td>   -0.0332</td> <td>    0.016</td> <td>   -2.029</td> <td> 0.043</td> <td>   -0.065</td> <td>   -0.001</td>
</tr>
<tr>
  <th>C(BsmtExposure, Poly).Cubic</th>     <td>    0.0499</td> <td>    0.011</td> <td>    4.657</td> <td> 0.000</td> <td>    0.029</td> <td>    0.071</td>
</tr>
<tr>
  <th>C(BsmtExposure, Poly)^4</th>         <td>   -0.0002</td> <td>    0.011</td> <td>   -0.015</td> <td> 0.988</td> <td>   -0.022</td> <td>    0.021</td>
</tr>
<tr>
  <th>C(GarageQual, Poly).Linear</th>      <td>    0.1475</td> <td>    0.059</td> <td>    2.510</td> <td> 0.012</td> <td>    0.032</td> <td>    0.263</td>
</tr>
<tr>
  <th>C(GarageQual, Poly).Quadratic</th>   <td>    0.0511</td> <td>    0.046</td> <td>    1.119</td> <td> 0.263</td> <td>   -0.038</td> <td>    0.141</td>
</tr>
<tr>
  <th>C(GarageQual, Poly).Cubic</th>       <td>   -0.0384</td> <td>    0.055</td> <td>   -0.699</td> <td> 0.485</td> <td>   -0.146</td> <td>    0.069</td>
</tr>
<tr>
  <th>C(GarageQual, Poly)^4</th>           <td>    0.0393</td> <td>    0.052</td> <td>    0.760</td> <td> 0.447</td> <td>   -0.062</td> <td>    0.141</td>
</tr>
<tr>
  <th>C(GarageQual, Poly)^5</th>           <td>   -0.0080</td> <td>    0.031</td> <td>   -0.261</td> <td> 0.794</td> <td>   -0.068</td> <td>    0.052</td>
</tr>
<tr>
  <th>C(MasVnrType)[T.BrkFace]</th>        <td>    0.1136</td> <td>    0.036</td> <td>    3.140</td> <td> 0.002</td> <td>    0.043</td> <td>    0.185</td>
</tr>
<tr>
  <th>C(MasVnrType)[T.None]</th>           <td>    0.1110</td> <td>    0.036</td> <td>    3.044</td> <td> 0.002</td> <td>    0.039</td> <td>    0.182</td>
</tr>
<tr>
  <th>C(MasVnrType)[T.Stone]</th>          <td>    0.1478</td> <td>    0.038</td> <td>    3.851</td> <td> 0.000</td> <td>    0.072</td> <td>    0.223</td>
</tr>
<tr>
  <th>C(CentralAir)[T.Y]</th>              <td>    0.1076</td> <td>    0.017</td> <td>    6.253</td> <td> 0.000</td> <td>    0.074</td> <td>    0.141</td>
</tr>
<tr>
  <th>C(PavedDrive)[T.P]</th>              <td>    0.0197</td> <td>    0.029</td> <td>    0.670</td> <td> 0.503</td> <td>   -0.038</td> <td>    0.078</td>
</tr>
<tr>
  <th>C(PavedDrive)[T.Y]</th>              <td>    0.0540</td> <td>    0.017</td> <td>    3.097</td> <td> 0.002</td> <td>    0.020</td> <td>    0.088</td>
</tr>
<tr>
  <th>scale(MasVnrArea)</th>               <td>    0.0063</td> <td>    0.006</td> <td>    1.138</td> <td> 0.255</td> <td>   -0.005</td> <td>    0.017</td>
</tr>
<tr>
  <th>scale(LotArea)</th>                  <td>    0.0240</td> <td>    0.004</td> <td>    5.862</td> <td> 0.000</td> <td>    0.016</td> <td>    0.032</td>
</tr>
<tr>
  <th>scale(YearRemodAdd)</th>             <td>    0.0521</td> <td>    0.005</td> <td>   10.628</td> <td> 0.000</td> <td>    0.042</td> <td>    0.062</td>
</tr>
<tr>
  <th>scale(GrLivArea)</th>                <td>    0.1269</td> <td>    0.006</td> <td>   20.882</td> <td> 0.000</td> <td>    0.115</td> <td>    0.139</td>
</tr>
<tr>
  <th>scale(GarageArea)</th>               <td>    0.0418</td> <td>    0.006</td> <td>    7.315</td> <td> 0.000</td> <td>    0.031</td> <td>    0.053</td>
</tr>
<tr>
  <th>scale(FullBath)</th>                 <td>    0.0056</td> <td>    0.006</td> <td>    1.014</td> <td> 0.311</td> <td>   -0.005</td> <td>    0.017</td>
</tr>
<tr>
  <th>scale(Fireplaces)</th>               <td>    0.0271</td> <td>    0.004</td> <td>    6.044</td> <td> 0.000</td> <td>    0.018</td> <td>    0.036</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td>266.005</td> <th>  Durbin-Watson:     </th> <td>   1.955</td> 
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.000</td>  <th>  Jarque-Bera (JB):  </th> <td> 903.131</td> 
</tr>
<tr>
  <th>Skew:</th>          <td>-0.881</td>  <th>  Prob(JB):          </th> <td>7.72e-197</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 6.430</td>  <th>  Cond. No.          </th> <td>    105.</td> 
</tr>
</table><br/><br/>Warnings:<br/>[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.



- Cross-validation

  -> 과최적화(overfitting)는 일어나지 않은 것으로 판단


```python
## cross-validation 파이썬 코드 구현

df_test = df.copy()
from sklearn.model_selection import KFold

scores = np.zeros(5)
cv = KFold(5, shuffle=True, random_state=0)
for i, (idx_train, idx_test) in enumerate(cv.split(df)):
    df_train = df.iloc[idx_train]
    df_test = df.iloc[idx_test]
    
    cv_model = sm.OLS.from_formula("np.log(SalePrice) ~ 0 + C(OverallQual, Poly) \
                            + scale(MasVnrArea) + scale(LotArea) + C(Neighborhood) + C(BsmtExposure, Poly) + scale(YearRemodAdd) \
                            + C(GarageQual, Poly) + C(MasVnrType) + C(CentralAir) + scale(GrLivArea) + scale(GarageArea) \
                            + scale(FullBath) + scale(Fireplaces) + C(PavedDrive) + scale(GarageArea)"
                                , data=df_test.iloc[:, 1:])
    
    cv_result = cv_model.fit()
    
    pred = np.exp(cv_result.predict(df_test))
    rss = ((df_test.SalePrice - pred) ** 2).sum()
    tss = ((df_test.SalePrice - df_test.SalePrice.mean()) ** 2).sum()
    rsquared = 1 - rss / tss
    
    scores[i] = rsquared
    print("train R2 = {:.8f}, test R2 = {:.8f}".format(cv_result.rsquared, rsquared))
```

    train R2 = 0.92595252, test R2 = 0.92479377
    train R2 = 0.90179872, test R2 = 0.91127732
    train R2 = 0.91237197, test R2 = 0.93109328
    train R2 = 0.89836779, test R2 = 0.92725023
    train R2 = 0.91624406, test R2 = 0.92415947
    

#### 3) 모델의 신뢰도 확인

- 실제값과 예측값 비교


```python
plt.figure(figsize=(8, 5))
plt.scatter(df['SalePrice'], np.exp(result.predict(df)))
plt.xlabel('real value')
plt.ylabel('predicted value')
plt.show()
```


<img src=https://github.com/minman2115/linear_regression_analysis_team_project/blob/master/boston_house_price_42_0.png>


- 실제값과 예측값의 상관계수


```python
df['SalePrice'].corr(np.exp(result.predict(df)))
```




    0.9539567137232111



- 잔차정규성 검증


```python
# Q-Q plot
plt.figure(figsize=(8, 5))
sp.stats.probplot(result.resid, plot=plt)
plt.show()
```


<img src="https://github.com/minman2115/linear_regression_analysis_team_project/blob/master/boston_house_price_46_0.png" />


### V 결론 : 신뢰성과 Performance를 균형있게 고려한 선형회귀 예측모델 구현 달성


```python

```
