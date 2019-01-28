---
layout: post
title: "선형회귀모형 구현연습"
tags: [머신러닝, 선형회귀분석]
comments: true
---

뉴욕 레스토랑 데이터를 기반으로 선형회귀모형 관련 연습문제 풀이

#### '패스트캠퍼스'에서 공부한 내용을 필기노트 형식으로 정리한 것으로 일부 주관적이며 오류가 있을 수 있습니다.

- 패스트캠퍼스 : https://www.fastcampus.co.kr
- 자료인용 출처 : https://datascienceschool.net

- 문제 : price는 가격을 나타내고, food, decor, service 열은 각각 음식, 데코, 서비스를 평가한 점수이며 east는 위치를 나타내는 더미변수이다. 이 데이터를 대상으로 다음 문제를 풀어라. 유의수준은 5%로 한다.


```python
# 필요한 패키지 임포트
%matplotlib inline
import numpy as np
import matplotlib as mpl
import matplotlib.pyplot as plt
```


```python
# 데이터로드
df0 = pd.read_csv("http://gattonweb.uky.edu/sheather/book/docs/datasets/nyc.csv", encoding='latin1')
del df0["Case"]
df0.tail()
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
      <th>Restaurant</th>
      <th>Price</th>
      <th>Food</th>
      <th>Decor</th>
      <th>Service</th>
      <th>East</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>163</th>
      <td>Baci</td>
      <td>31</td>
      <td>17</td>
      <td>15</td>
      <td>16</td>
      <td>0</td>
    </tr>
    <tr>
      <th>164</th>
      <td>Puccini</td>
      <td>26</td>
      <td>20</td>
      <td>16</td>
      <td>17</td>
      <td>0</td>
    </tr>
    <tr>
      <th>165</th>
      <td>Bella Luna</td>
      <td>31</td>
      <td>18</td>
      <td>16</td>
      <td>17</td>
      <td>0</td>
    </tr>
    <tr>
      <th>166</th>
      <td>Métisse</td>
      <td>38</td>
      <td>22</td>
      <td>17</td>
      <td>21</td>
      <td>0</td>
    </tr>
    <tr>
      <th>167</th>
      <td>Gennaro</td>
      <td>34</td>
      <td>24</td>
      <td>10</td>
      <td>16</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 데이터 분포 확인
sns.pairplot(df[['Price','Food','Decor','Service','East']])
plt.show()
```


![newyork_regression_3_0](https://user-images.githubusercontent.com/41605276/51816557-630dba80-230a-11e9-98f1-bc8565a94861.png)


#### 1. 다른 변수들을 사용하여 가격을 예측하는 선형회귀모형을 만들어라.


```python
# 독립변수외 상수항 추가
df = sm.add_constant(df0[['Food','Decor','Service','East']])

model = sm.OLS(df0[['Price']], df)
result = model.fit()
print(result.summary())
```

                                OLS Regression Results                            
    ==============================================================================
    Dep. Variable:                  Price   R-squared:                       0.628
    Model:                            OLS   Adj. R-squared:                  0.619
    Method:                 Least Squares   F-statistic:                     68.76
    Date:                Sat, 19 Jan 2019   Prob (F-statistic):           5.35e-34
    Time:                        12:46:31   Log-Likelihood:                -529.36
    No. Observations:                 168   AIC:                             1069.
    Df Residuals:                     163   BIC:                             1084.
    Df Model:                           4                                         
    Covariance Type:            nonrobust                                         
    ==============================================================================
                     coef    std err          t      P>|t|      [0.025      0.975]
    ------------------------------------------------------------------------------
    const        -24.0238      4.708     -5.102      0.000     -33.321     -14.727
    Food           1.5381      0.369      4.169      0.000       0.810       2.267
    Decor          1.9101      0.217      8.802      0.000       1.482       2.339
    Service       -0.0027      0.396     -0.007      0.995      -0.785       0.780
    East           2.0681      0.947      2.184      0.030       0.199       3.938
    ==============================================================================
    Omnibus:                        5.180   Durbin-Watson:                   1.760
    Prob(Omnibus):                  0.075   Jarque-Bera (JB):                5.039
    Skew:                           0.304   Prob(JB):                       0.0805
    Kurtosis:                       3.591   Cond. No.                         357.
    ==============================================================================
    
    Warnings:
    [1] Standard Errors assume that the covariance matrix of the errors is correctly specified.
    

#### 2. Food, Decor,Service 중에서 가격에 가장 크게 영향을 미치는 변수는 무엇인가? 이 변수는 통계적으로 유의한가

- 정답 : Decor 변수가 가장 크게 영향을 미친다. 유의확률 또한 1% 미만으로 통계적으로도 유의하다고 할 수 있다.


```python
fig = plt.figure(figsize=(10, 10))
sm.graphics.plot_partregress_grid(result, fig=fig)
fig.suptitle("")
plt.show()
```


![newyork_regression_8_0](https://user-images.githubusercontent.com/41605276/51816566-715bd680-230a-11e9-9d7d-4325a8d30fdb.png)


#### 3. 식당의 위치는 가격에 영향을 미치는가? 만약 영향을 미친다면 East의 식당은 West 식당대비 어느정도 비싸다고 말 할 수 있는가?

- 정답 : East의 유의확률은 약 3%이므로 유의하다. 즉 위치는 가격에 영향을 미친다. 유의수준 5%에서 약 0.2 ~ 3.9 달러 비싸다는 것을 알 수 있다.

#### 4. 통계적으로 유의하지 않은 변수는 무엇인가?

- 정답 : service 변수가 유의하지 않은 변수이다.

#### 5. 통계적으로 유의하지 않은 변수를 제외하고 다시 모형을 만들어 결과를 비교하라


```python
model2 = sm.OLS(df0[['Price']], (df[['Food','Decor','East']]))
result2 = model2.fit()
print(result2.summary())
```

                                OLS Regression Results                            
    ==============================================================================
    Dep. Variable:                  Price   R-squared:                       0.981
    Model:                            OLS   Adj. R-squared:                  0.980
    Method:                 Least Squares   F-statistic:                     2775.
    Date:                Sat, 19 Jan 2019   Prob (F-statistic):          6.49e-141
    Time:                        12:49:38   Log-Likelihood:                -541.91
    No. Observations:                 168   AIC:                             1090.
    Df Residuals:                     165   BIC:                             1099.
    Df Model:                           3                                         
    Covariance Type:            nonrobust                                         
    ==============================================================================
                     coef    std err          t      P>|t|      [0.025      0.975]
    ------------------------------------------------------------------------------
    Food           0.4913      0.180      2.735      0.007       0.137       0.846
    Decor          1.7721      0.202      8.768      0.000       1.373       2.171
    East           2.2858      1.000      2.286      0.024       0.311       4.260
    ==============================================================================
    Omnibus:                        2.965   Durbin-Watson:                   1.745
    Prob(Omnibus):                  0.227   Jarque-Bera (JB):                2.692
    Skew:                           0.308   Prob(JB):                        0.260
    Kurtosis:                       3.070   Cond. No.                         57.7
    ==============================================================================
    
    Warnings:
    [1] Standard Errors assume that the covariance matrix of the errors is correctly specified.
    

#### 6. Food = 20, Decor = 20, Service = 20, East = 1인 식당의 가격을 예측하라


```python
x_new = pd.DataFrame({'const':[1],'Food':[20],'Decor':[20],'Service':[20],'East':[1]})
result.predict(x_new)
```




    0    46.953842
    dtype: float64



#### 7. 모형2를 이용하여 아웃라이어인 식당을 구하라.


```python
idx = result2.resid_pearson > 2
df0[idx]
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
      <th>Restaurant</th>
      <th>Price</th>
      <th>Food</th>
      <th>Decor</th>
      <th>Service</th>
      <th>East</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>29</th>
      <td>Harry Cipriani</td>
      <td>65</td>
      <td>21</td>
      <td>20</td>
      <td>20</td>
      <td>1</td>
    </tr>
    <tr>
      <th>44</th>
      <td>Bravo Gianni</td>
      <td>56</td>
      <td>22</td>
      <td>17</td>
      <td>21</td>
      <td>1</td>
    </tr>
    <tr>
      <th>47</th>
      <td>Il Valletto Due Mila</td>
      <td>56</td>
      <td>21</td>
      <td>17</td>
      <td>20</td>
      <td>1</td>
    </tr>
    <tr>
      <th>55</th>
      <td>Nello</td>
      <td>54</td>
      <td>18</td>
      <td>16</td>
      <td>15</td>
      <td>1</td>
    </tr>
    <tr>
      <th>102</th>
      <td>Rao's</td>
      <td>57</td>
      <td>23</td>
      <td>16</td>
      <td>20</td>
      <td>1</td>
    </tr>
    <tr>
      <th>129</th>
      <td>Rainbow Grill</td>
      <td>65</td>
      <td>19</td>
      <td>23</td>
      <td>18</td>
      <td>0</td>
    </tr>
    <tr>
      <th>131</th>
      <td>San Domenico</td>
      <td>65</td>
      <td>23</td>
      <td>22</td>
      <td>22</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>




```python
PricePredicted = result2.predict(df[['Food', 'Decor', 'East']])
```


```python
plt.scatter(PricePredicted, df0.Price)
plt.scatter(PricePredicted[idx], df0[idx].Price, s=200, c='r', alpha=0.5)
plt.show()
```


![newyork_regression_20_0](https://user-images.githubusercontent.com/41605276/51816583-83d61000-230a-11e9-83fd-a7335f95dc31.png)


#### 8. 모형 2를 이용하여 레버리지가 큰 데이터를 구하라. 추가적으로 (레버리지가 평균 레버리지의 3배 이상) 이 데이터를 스캐터 플롯상에 시각화할것


```python
influence = result2.get_influence()
h = influence.hat_matrix_diag
idx = h > 3 * h.mean()
df0[idx]
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
      <th>Restaurant</th>
      <th>Price</th>
      <th>Food</th>
      <th>Decor</th>
      <th>Service</th>
      <th>East</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>116</th>
      <td>Veronica</td>
      <td>22</td>
      <td>21</td>
      <td>6</td>
      <td>14</td>
      <td>0</td>
    </tr>
    <tr>
      <th>129</th>
      <td>Rainbow Grill</td>
      <td>65</td>
      <td>19</td>
      <td>23</td>
      <td>18</td>
      <td>0</td>
    </tr>
    <tr>
      <th>144</th>
      <td>Palio</td>
      <td>63</td>
      <td>22</td>
      <td>25</td>
      <td>22</td>
      <td>0</td>
    </tr>
    <tr>
      <th>167</th>
      <td>Gennaro</td>
      <td>34</td>
      <td>24</td>
      <td>10</td>
      <td>16</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>




```python
plt.scatter(PricePredicted, df0.Price)
plt.scatter(PricePredicted[idx], df0[idx].Price, s=200, c='r', alpha=0.5)
plt.show()
```


![newyork_regression_23_0](https://user-images.githubusercontent.com/41605276/51816595-918b9580-230a-11e9-8e1b-d78b74701e1c.png)


#### 9. 모형 2를 이용하여 'Cook's distance'가 큰 데이터를 구하라. 추가적으로 (Fox' Outlier Recommendation 사용) 이 데이터를 스캐터 플롯에 시각화 할것


```python
cooks_d2, pvals = influence.cooks_distance
fox_cr = 4 / (len(df0) - 4 - 1)
idx = cooks_d2 > fox_cr
df0[idx]
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
      <th>Restaurant</th>
      <th>Price</th>
      <th>Food</th>
      <th>Decor</th>
      <th>Service</th>
      <th>East</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>29</th>
      <td>Harry Cipriani</td>
      <td>65</td>
      <td>21</td>
      <td>20</td>
      <td>20</td>
      <td>1</td>
    </tr>
    <tr>
      <th>102</th>
      <td>Rao's</td>
      <td>57</td>
      <td>23</td>
      <td>16</td>
      <td>20</td>
      <td>1</td>
    </tr>
    <tr>
      <th>108</th>
      <td>Casa Mia</td>
      <td>31</td>
      <td>20</td>
      <td>19</td>
      <td>19</td>
      <td>1</td>
    </tr>
    <tr>
      <th>114</th>
      <td>Lamarca</td>
      <td>19</td>
      <td>18</td>
      <td>9</td>
      <td>15</td>
      <td>1</td>
    </tr>
    <tr>
      <th>129</th>
      <td>Rainbow Grill</td>
      <td>65</td>
      <td>19</td>
      <td>23</td>
      <td>18</td>
      <td>0</td>
    </tr>
    <tr>
      <th>131</th>
      <td>San Domenico</td>
      <td>65</td>
      <td>23</td>
      <td>22</td>
      <td>22</td>
      <td>0</td>
    </tr>
    <tr>
      <th>138</th>
      <td>Rino Trattoria</td>
      <td>23</td>
      <td>20</td>
      <td>14</td>
      <td>16</td>
      <td>0</td>
    </tr>
    <tr>
      <th>140</th>
      <td>Trattoria Del Sogno</td>
      <td>29</td>
      <td>21</td>
      <td>18</td>
      <td>19</td>
      <td>0</td>
    </tr>
    <tr>
      <th>144</th>
      <td>Palio</td>
      <td>63</td>
      <td>22</td>
      <td>25</td>
      <td>22</td>
      <td>0</td>
    </tr>
    <tr>
      <th>151</th>
      <td>Il Tinello</td>
      <td>57</td>
      <td>23</td>
      <td>19</td>
      <td>23</td>
      <td>0</td>
    </tr>
    <tr>
      <th>167</th>
      <td>Gennaro</td>
      <td>34</td>
      <td>24</td>
      <td>10</td>
      <td>16</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>




```python
plt.scatter(PricePredicted, df0.Price)
plt.scatter(PricePredicted[idx], df0[idx].Price, s=200, c='r', alpha=0.5)
plt.show()
```


![newyork_regression_26_0](https://user-images.githubusercontent.com/41605276/51816611-a10ade80-230a-11e9-8ac9-6d17715d0654.png)

