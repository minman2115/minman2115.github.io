---
layout: post
title: "추세와 계절성 기초개념"
tags: [시계열분석]
comments: true
---

.

#### 그림, 실습코드 등 학습자료 출처 : https://datascienceschool.net

#### 1. Trend(결정론적 추세)

![1](https://user-images.githubusercontent.com/41605276/58452105-18356f80-8151-11e9-9f41-0836fb7acc15.jpg)

아래 예시들은 모두 추세를 가지는 확률과정을 시뮬레이션 한 것이다.


```python
%matplotlib inline
%config InlineBackend.figure_format = 'retina'

np.random.seed(0)
t = np.arange(20)
y = np.zeros((30, 20))
for i in range(30):
    y[i, :] = t + sp.stats.norm.rvs(size=20)
    plt.plot(t, y[i], lw=1)
plt.show()
```


<img width="374" alt="추세와 계절성 기초개념_3_0" src="https://user-images.githubusercontent.com/41605276/58452117-27b4b880-8151-11e9-822f-f6d9d0d827b8.png">



```python
np.random.seed(0)
t = np.arange(20)
y = np.zeros((30, 20))
for i in range(30):
    y[i, :] = -5 * np.cos(0.25 * np.pi * t) + sp.stats.norm.rvs(size=20)
    plt.plot(t, y[i], lw=1)
plt.show()
```


<img width="374" alt="추세와 계절성 기초개념_4_0" src="https://user-images.githubusercontent.com/41605276/58452127-300cf380-8151-11e9-86e8-cb4899db7cfe.png">


#### 2. trend estimation(추세추정)

![2](https://user-images.githubusercontent.com/41605276/58452139-39965b80-8151-11e9-8dc4-4dca0f37c645.jpg)

#### 3. 다항식 추세

![3](https://user-images.githubusercontent.com/41605276/58452144-3f8c3c80-8151-11e9-9213-b14beece01d3.jpg)

대기중 CO2 농도를 측정 예시


```python
data = sm.datasets.get_rdataset("CO2", package="datasets")
df = data.data

def yearfraction2datetime(yearfraction, startyear=0):
    import datetime
    import dateutil
    year = int(yearfraction) + startyear
    month = int(round(12 * (yearfraction - year)))
    delta = dateutil.relativedelta.relativedelta(months=month)
    date = datetime.datetime(year, 1, 1) + delta
    return date

df["datetime"] = df.time.map(yearfraction2datetime)
df["month"] = df.datetime.dt.month
df.tail()
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
      <th>time</th>
      <th>value</th>
      <th>datetime</th>
      <th>month</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>463</th>
      <td>1997.583333</td>
      <td>362.57</td>
      <td>1997-08-01</td>
      <td>8</td>
    </tr>
    <tr>
      <th>464</th>
      <td>1997.666667</td>
      <td>360.24</td>
      <td>1997-09-01</td>
      <td>9</td>
    </tr>
    <tr>
      <th>465</th>
      <td>1997.750000</td>
      <td>360.83</td>
      <td>1997-10-01</td>
      <td>10</td>
    </tr>
    <tr>
      <th>466</th>
      <td>1997.833333</td>
      <td>362.49</td>
      <td>1997-11-01</td>
      <td>11</td>
    </tr>
    <tr>
      <th>467</th>
      <td>1997.916667</td>
      <td>364.34</td>
      <td>1997-12-01</td>
      <td>12</td>
    </tr>
  </tbody>
</table>
</div>




```python
result = sm.OLS.from_formula("value ~ time", data=df).fit()
result.summary()
```




<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>          <td>value</td>      <th>  R-squared:         </th> <td>   0.969</td> 
</tr>
<tr>
  <th>Model:</th>                   <td>OLS</td>       <th>  Adj. R-squared:    </th> <td>   0.969</td> 
</tr>
<tr>
  <th>Method:</th>             <td>Least Squares</td>  <th>  F-statistic:       </th> <td>1.479e+04</td>
</tr>
<tr>
  <th>Date:</th>             <td>Tue, 28 May 2019</td> <th>  Prob (F-statistic):</th>  <td>  0.00</td>  
</tr>
<tr>
  <th>Time:</th>                 <td>12:27:37</td>     <th>  Log-Likelihood:    </th> <td> -1113.5</td> 
</tr>
<tr>
  <th>No. Observations:</th>      <td>   468</td>      <th>  AIC:               </th> <td>   2231.</td> 
</tr>
<tr>
  <th>Df Residuals:</th>          <td>   466</td>      <th>  BIC:               </th> <td>   2239.</td> 
</tr>
<tr>
  <th>Df Model:</th>              <td>     1</td>      <th>                     </th>     <td> </td>    
</tr>
<tr>
  <th>Covariance Type:</th>      <td>nonrobust</td>    <th>                     </th>     <td> </td>    
</tr>
</table>
<table class="simpletable">
<tr>
      <td></td>         <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>Intercept</th> <td>-2249.7742</td> <td>   21.268</td> <td> -105.784</td> <td> 0.000</td> <td>-2291.566</td> <td>-2207.982</td>
</tr>
<tr>
  <th>time</th>      <td>    1.3075</td> <td>    0.011</td> <td>  121.634</td> <td> 0.000</td> <td>    1.286</td> <td>    1.329</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td>15.857</td> <th>  Durbin-Watson:     </th> <td>   0.212</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.000</td> <th>  Jarque-Bera (JB):  </th> <td>   7.798</td>
</tr>
<tr>
  <th>Skew:</th>          <td> 0.048</td> <th>  Prob(JB):          </th> <td>  0.0203</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 2.375</td> <th>  Cond. No.          </th> <td>3.48e+05</td>
</tr>
</table><br/><br/>Warnings:<br/>[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.<br/>[2] The condition number is large, 3.48e+05. This might indicate that there are<br/>strong multicollinearity or other numerical problems.



result.summary에 따르면 추세함수는 아래와 같다.

f(t) = 1.3075t - 2249.7742


```python
t = df.time
y = df.value
trend = result.params[0] + result.params[1] * t
plt.plot(t, y, '-', t, trend, '-')
plt.show()
```


<img width="380" alt="추세와 계절성 기초개념_13_0" src="https://user-images.githubusercontent.com/41605276/58452155-49ae3b00-8151-11e9-954e-715767d00a52.png">


만약 추세가 2차 함수 형태라면 아래와 같이 추세를 추정할 수 있다.


```python
result2 = sm.OLS.from_formula("value ~ time + I(time ** 2)", data=df).fit()
result2.summary()
```




<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>          <td>value</td>      <th>  R-squared:         </th> <td>   0.979</td> 
</tr>
<tr>
  <th>Model:</th>                   <td>OLS</td>       <th>  Adj. R-squared:    </th> <td>   0.979</td> 
</tr>
<tr>
  <th>Method:</th>             <td>Least Squares</td>  <th>  F-statistic:       </th> <td>1.075e+04</td>
</tr>
<tr>
  <th>Date:</th>             <td>Tue, 28 May 2019</td> <th>  Prob (F-statistic):</th>  <td>  0.00</td>  
</tr>
<tr>
  <th>Time:</th>                 <td>12:29:41</td>     <th>  Log-Likelihood:    </th> <td> -1027.8</td> 
</tr>
<tr>
  <th>No. Observations:</th>      <td>   468</td>      <th>  AIC:               </th> <td>   2062.</td> 
</tr>
<tr>
  <th>Df Residuals:</th>          <td>   465</td>      <th>  BIC:               </th> <td>   2074.</td> 
</tr>
<tr>
  <th>Df Model:</th>              <td>     2</td>      <th>                     </th>     <td> </td>    
</tr>
<tr>
  <th>Covariance Type:</th>      <td>nonrobust</td>    <th>                     </th>     <td> </td>    
</tr>
</table>
<table class="simpletable">
<tr>
        <td></td>          <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>Intercept</th>    <td>  4.77e+04</td> <td> 3482.902</td> <td>   13.696</td> <td> 0.000</td> <td> 4.09e+04</td> <td> 5.45e+04</td>
</tr>
<tr>
  <th>time</th>         <td>  -49.1907</td> <td>    3.521</td> <td>  -13.971</td> <td> 0.000</td> <td>  -56.110</td> <td>  -42.272</td>
</tr>
<tr>
  <th>I(time ** 2)</th> <td>    0.0128</td> <td>    0.001</td> <td>   14.342</td> <td> 0.000</td> <td>    0.011</td> <td>    0.015</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td>66.659</td> <th>  Durbin-Watson:     </th> <td>   0.306</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.000</td> <th>  Jarque-Bera (JB):  </th> <td>  17.850</td>
</tr>
<tr>
  <th>Skew:</th>          <td>-0.116</td> <th>  Prob(JB):          </th> <td>0.000133</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 2.072</td> <th>  Cond. No.          </th> <td>1.35e+11</td>
</tr>
</table><br/><br/>Warnings:<br/>[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.<br/>[2] The condition number is large, 1.35e+11. This might indicate that there are<br/>strong multicollinearity or other numerical problems.




```python
trend2 = result2.params[0] + result2.params[1] * t + result2.params[2] * t**2
plt.plot(t, y, '-', t, trend2, '-')
plt.show()
```


<img width="380" alt="추세와 계절성 기초개념_16_0" src="https://user-images.githubusercontent.com/41605276/58452164-516ddf80-8151-11e9-8c62-971568372b03.png">


#### 3. 계절성 추세

![4](https://user-images.githubusercontent.com/41605276/58452169-57fc5700-8151-11e9-8522-82422ae31847.jpg)


```python
df2 = sm.datasets.get_rdataset("deaths", "MASS").data
df2["datetime"] = df2.time.map(yearfraction2datetime)
df2["month"] = df2.datetime.dt.month
df2.tail()
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
      <th>time</th>
      <th>value</th>
      <th>datetime</th>
      <th>month</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>67</th>
      <td>1979.583333</td>
      <td>1354</td>
      <td>1979-08-01</td>
      <td>8</td>
    </tr>
    <tr>
      <th>68</th>
      <td>1979.666667</td>
      <td>1333</td>
      <td>1979-09-01</td>
      <td>9</td>
    </tr>
    <tr>
      <th>69</th>
      <td>1979.750000</td>
      <td>1492</td>
      <td>1979-10-01</td>
      <td>10</td>
    </tr>
    <tr>
      <th>70</th>
      <td>1979.833333</td>
      <td>1781</td>
      <td>1979-11-01</td>
      <td>11</td>
    </tr>
    <tr>
      <th>71</th>
      <td>1979.916667</td>
      <td>1915</td>
      <td>1979-12-01</td>
      <td>12</td>
    </tr>
  </tbody>
</table>
</div>




```python
result = sm.OLS.from_formula('value ~ C(month) - 1', data=df2).fit()
result.summary()
```




<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>          <td>value</td>      <th>  R-squared:         </th> <td>   0.853</td>
</tr>
<tr>
  <th>Model:</th>                   <td>OLS</td>       <th>  Adj. R-squared:    </th> <td>   0.826</td>
</tr>
<tr>
  <th>Method:</th>             <td>Least Squares</td>  <th>  F-statistic:       </th> <td>   31.66</td>
</tr>
<tr>
  <th>Date:</th>             <td>Tue, 28 May 2019</td> <th>  Prob (F-statistic):</th> <td>6.55e-21</td>
</tr>
<tr>
  <th>Time:</th>                 <td>12:41:12</td>     <th>  Log-Likelihood:    </th> <td> -494.38</td>
</tr>
<tr>
  <th>No. Observations:</th>      <td>    72</td>      <th>  AIC:               </th> <td>   1013.</td>
</tr>
<tr>
  <th>Df Residuals:</th>          <td>    60</td>      <th>  BIC:               </th> <td>   1040.</td>
</tr>
<tr>
  <th>Df Model:</th>              <td>    11</td>      <th>                     </th>     <td> </td>   
</tr>
<tr>
  <th>Covariance Type:</th>      <td>nonrobust</td>    <th>                     </th>     <td> </td>   
</tr>
</table>
<table class="simpletable">
<tr>
        <td></td>          <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>C(month)[1]</th>  <td> 2959.3333</td> <td>  103.831</td> <td>   28.502</td> <td> 0.000</td> <td> 2751.641</td> <td> 3167.025</td>
</tr>
<tr>
  <th>C(month)[2]</th>  <td> 2894.6667</td> <td>  103.831</td> <td>   27.879</td> <td> 0.000</td> <td> 2686.975</td> <td> 3102.359</td>
</tr>
<tr>
  <th>C(month)[3]</th>  <td> 2743.0000</td> <td>  103.831</td> <td>   26.418</td> <td> 0.000</td> <td> 2535.308</td> <td> 2950.692</td>
</tr>
<tr>
  <th>C(month)[4]</th>  <td> 2269.6667</td> <td>  103.831</td> <td>   21.859</td> <td> 0.000</td> <td> 2061.975</td> <td> 2477.359</td>
</tr>
<tr>
  <th>C(month)[5]</th>  <td> 1805.1667</td> <td>  103.831</td> <td>   17.386</td> <td> 0.000</td> <td> 1597.475</td> <td> 2012.859</td>
</tr>
<tr>
  <th>C(month)[6]</th>  <td> 1608.6667</td> <td>  103.831</td> <td>   15.493</td> <td> 0.000</td> <td> 1400.975</td> <td> 1816.359</td>
</tr>
<tr>
  <th>C(month)[7]</th>  <td> 1550.8333</td> <td>  103.831</td> <td>   14.936</td> <td> 0.000</td> <td> 1343.141</td> <td> 1758.525</td>
</tr>
<tr>
  <th>C(month)[8]</th>  <td> 1408.3333</td> <td>  103.831</td> <td>   13.564</td> <td> 0.000</td> <td> 1200.641</td> <td> 1616.025</td>
</tr>
<tr>
  <th>C(month)[9]</th>  <td> 1397.3333</td> <td>  103.831</td> <td>   13.458</td> <td> 0.000</td> <td> 1189.641</td> <td> 1605.025</td>
</tr>
<tr>
  <th>C(month)[10]</th> <td> 1690.0000</td> <td>  103.831</td> <td>   16.277</td> <td> 0.000</td> <td> 1482.308</td> <td> 1897.692</td>
</tr>
<tr>
  <th>C(month)[11]</th> <td> 1874.0000</td> <td>  103.831</td> <td>   18.049</td> <td> 0.000</td> <td> 1666.308</td> <td> 2081.692</td>
</tr>
<tr>
  <th>C(month)[12]</th> <td> 2478.5000</td> <td>  103.831</td> <td>   23.871</td> <td> 0.000</td> <td> 2270.808</td> <td> 2686.192</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td>19.630</td> <th>  Durbin-Watson:     </th> <td>   1.374</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.000</td> <th>  Jarque-Bera (JB):  </th> <td>  49.630</td>
</tr>
<tr>
  <th>Skew:</th>          <td> 0.787</td> <th>  Prob(JB):          </th> <td>1.67e-11</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 6.750</td> <th>  Cond. No.          </th> <td>    1.00</td>
</tr>
</table><br/><br/>Warnings:<br/>[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.



모형식 문자열에서 -1은 y절편을 모형에 넣지 않는 것을 의미한다. 

다시말해서 모든 month변수의 가능한 값을 사용한다. 

month 변수가 0과 1값을 가지는 dummy 변수이기때문에 이 분석에서 구한 계수는 특정한 달의 기온 평균값이 된다.

추정한 계절성 성분과 나머지 성분을 분리하여 그리면 아래와 같다.


```python
plt.plot(df2.value) # 파란색 선
plt.plot(result.fittedvalues, lw=3, alpha=0.5) # 살색 선
plt.plot(result.resid) # 초록색 선
plt.show()
```


<img width="386" alt="추세와 계절성 기초개념_22_0" src="https://user-images.githubusercontent.com/41605276/58452178-62b6ec00-8151-11e9-9f7a-15cc279108f5.png">


위 방법으로 찾아낸 잔차 시계열을 보면 시간이 지나갈 수록 점점 감소하고 있는 것을 확인할 수 있다.

이러한 선형추세까지 한꺼번에 잡아내려면 다음과 같이 회귀분석을 하면 된다.


```python
result2 = sm.OLS.from_formula('value ~ time + C(month) - 1', data=df2).fit()
result2.summary()
```




<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>          <td>value</td>      <th>  R-squared:         </th> <td>   0.881</td>
</tr>
<tr>
  <th>Model:</th>                   <td>OLS</td>       <th>  Adj. R-squared:    </th> <td>   0.857</td>
</tr>
<tr>
  <th>Method:</th>             <td>Least Squares</td>  <th>  F-statistic:       </th> <td>   36.43</td>
</tr>
<tr>
  <th>Date:</th>             <td>Tue, 28 May 2019</td> <th>  Prob (F-statistic):</th> <td>8.56e-23</td>
</tr>
<tr>
  <th>Time:</th>                 <td>12:49:03</td>     <th>  Log-Likelihood:    </th> <td> -486.75</td>
</tr>
<tr>
  <th>No. Observations:</th>      <td>    72</td>      <th>  AIC:               </th> <td>   999.5</td>
</tr>
<tr>
  <th>Df Residuals:</th>          <td>    59</td>      <th>  BIC:               </th> <td>   1029.</td>
</tr>
<tr>
  <th>Df Model:</th>              <td>    12</td>      <th>                     </th>     <td> </td>   
</tr>
<tr>
  <th>Covariance Type:</th>      <td>nonrobust</td>    <th>                     </th>     <td> </td>   
</tr>
</table>
<table class="simpletable">
<tr>
        <td></td>          <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>C(month)[1]</th>  <td> 1.204e+05</td> <td> 3.15e+04</td> <td>    3.825</td> <td> 0.000</td> <td> 5.74e+04</td> <td> 1.83e+05</td>
</tr>
<tr>
  <th>C(month)[2]</th>  <td> 1.203e+05</td> <td> 3.15e+04</td> <td>    3.823</td> <td> 0.000</td> <td> 5.73e+04</td> <td> 1.83e+05</td>
</tr>
<tr>
  <th>C(month)[3]</th>  <td> 1.202e+05</td> <td> 3.15e+04</td> <td>    3.819</td> <td> 0.000</td> <td> 5.72e+04</td> <td> 1.83e+05</td>
</tr>
<tr>
  <th>C(month)[4]</th>  <td> 1.197e+05</td> <td> 3.15e+04</td> <td>    3.803</td> <td> 0.000</td> <td> 5.67e+04</td> <td> 1.83e+05</td>
</tr>
<tr>
  <th>C(month)[5]</th>  <td> 1.192e+05</td> <td> 3.15e+04</td> <td>    3.789</td> <td> 0.000</td> <td> 5.63e+04</td> <td> 1.82e+05</td>
</tr>
<tr>
  <th>C(month)[6]</th>  <td>  1.19e+05</td> <td> 3.15e+04</td> <td>    3.782</td> <td> 0.000</td> <td> 5.61e+04</td> <td> 1.82e+05</td>
</tr>
<tr>
  <th>C(month)[7]</th>  <td>  1.19e+05</td> <td> 3.15e+04</td> <td>    3.781</td> <td> 0.000</td> <td>  5.6e+04</td> <td> 1.82e+05</td>
</tr>
<tr>
  <th>C(month)[8]</th>  <td> 1.189e+05</td> <td> 3.15e+04</td> <td>    3.776</td> <td> 0.000</td> <td> 5.59e+04</td> <td> 1.82e+05</td>
</tr>
<tr>
  <th>C(month)[9]</th>  <td> 1.188e+05</td> <td> 3.15e+04</td> <td>    3.776</td> <td> 0.000</td> <td> 5.59e+04</td> <td> 1.82e+05</td>
</tr>
<tr>
  <th>C(month)[10]</th> <td> 1.191e+05</td> <td> 3.15e+04</td> <td>    3.785</td> <td> 0.000</td> <td> 5.62e+04</td> <td> 1.82e+05</td>
</tr>
<tr>
  <th>C(month)[11]</th> <td> 1.193e+05</td> <td> 3.15e+04</td> <td>    3.791</td> <td> 0.000</td> <td> 5.63e+04</td> <td> 1.82e+05</td>
</tr>
<tr>
  <th>C(month)[12]</th> <td> 1.199e+05</td> <td> 3.15e+04</td> <td>    3.810</td> <td> 0.000</td> <td>  5.7e+04</td> <td> 1.83e+05</td>
</tr>
<tr>
  <th>time</th>         <td>  -59.4024</td> <td>   15.920</td> <td>   -3.731</td> <td> 0.000</td> <td>  -91.258</td> <td>  -27.547</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td>26.709</td> <th>  Durbin-Watson:     </th> <td>   1.679</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.000</td> <th>  Jarque-Bera (JB):  </th> <td> 102.584</td>
</tr>
<tr>
  <th>Skew:</th>          <td> 0.943</td> <th>  Prob(JB):          </th> <td>5.30e-23</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 8.535</td> <th>  Cond. No.          </th> <td>7.93e+06</td>
</tr>
</table><br/><br/>Warnings:<br/>[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.<br/>[2] The condition number is large, 7.93e+06. This might indicate that there are<br/>strong multicollinearity or other numerical problems.




```python
plt.plot(df2.value)
plt.plot(result2.fittedvalues, lw=3, alpha=0.5)
plt.plot(result2.resid)
plt.show()
```


<img width="386" alt="추세와 계절성 기초개념_25_0" src="https://user-images.githubusercontent.com/41605276/58452182-6b0f2700-8151-11e9-9138-ca37136a6b4c.png">


이제는 CO2를 계절성까지 포함하여 추세 추정을 하는 실습을 해보자.


```python
result3 = sm.OLS.from_formula("value ~ 0 + C(month) + time + I(time ** 2)", data=df).fit()
result3.summary()
```




<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>          <td>value</td>      <th>  R-squared:         </th> <td>   0.998</td> 
</tr>
<tr>
  <th>Model:</th>                   <td>OLS</td>       <th>  Adj. R-squared:    </th> <td>   0.998</td> 
</tr>
<tr>
  <th>Method:</th>             <td>Least Squares</td>  <th>  F-statistic:       </th> <td>1.531e+04</td>
</tr>
<tr>
  <th>Date:</th>             <td>Tue, 28 May 2019</td> <th>  Prob (F-statistic):</th>  <td>  0.00</td>  
</tr>
<tr>
  <th>Time:</th>                 <td>12:50:35</td>     <th>  Log-Likelihood:    </th> <td> -505.82</td> 
</tr>
<tr>
  <th>No. Observations:</th>      <td>   468</td>      <th>  AIC:               </th> <td>   1040.</td> 
</tr>
<tr>
  <th>Df Residuals:</th>          <td>   454</td>      <th>  BIC:               </th> <td>   1098.</td> 
</tr>
<tr>
  <th>Df Model:</th>              <td>    13</td>      <th>                     </th>     <td> </td>    
</tr>
<tr>
  <th>Covariance Type:</th>      <td>nonrobust</td>    <th>                     </th>     <td> </td>    
</tr>
</table>
<table class="simpletable">
<tr>
        <td></td>          <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>C(month)[1]</th>  <td> 4.771e+04</td> <td> 1155.536</td> <td>   41.289</td> <td> 0.000</td> <td> 4.54e+04</td> <td>    5e+04</td>
</tr>
<tr>
  <th>C(month)[2]</th>  <td> 4.771e+04</td> <td> 1155.536</td> <td>   41.290</td> <td> 0.000</td> <td> 4.54e+04</td> <td>    5e+04</td>
</tr>
<tr>
  <th>C(month)[3]</th>  <td> 4.771e+04</td> <td> 1155.536</td> <td>   41.290</td> <td> 0.000</td> <td> 4.54e+04</td> <td>    5e+04</td>
</tr>
<tr>
  <th>C(month)[4]</th>  <td> 4.771e+04</td> <td> 1155.536</td> <td>   41.291</td> <td> 0.000</td> <td> 4.54e+04</td> <td>    5e+04</td>
</tr>
<tr>
  <th>C(month)[5]</th>  <td> 4.771e+04</td> <td> 1155.536</td> <td>   41.292</td> <td> 0.000</td> <td> 4.54e+04</td> <td>    5e+04</td>
</tr>
<tr>
  <th>C(month)[6]</th>  <td> 4.771e+04</td> <td> 1155.536</td> <td>   41.291</td> <td> 0.000</td> <td> 4.54e+04</td> <td>    5e+04</td>
</tr>
<tr>
  <th>C(month)[7]</th>  <td> 4.771e+04</td> <td> 1155.536</td> <td>   41.290</td> <td> 0.000</td> <td> 4.54e+04</td> <td>    5e+04</td>
</tr>
<tr>
  <th>C(month)[8]</th>  <td> 4.771e+04</td> <td> 1155.536</td> <td>   41.288</td> <td> 0.000</td> <td> 4.54e+04</td> <td>    5e+04</td>
</tr>
<tr>
  <th>C(month)[9]</th>  <td> 4.771e+04</td> <td> 1155.536</td> <td>   41.287</td> <td> 0.000</td> <td> 4.54e+04</td> <td>    5e+04</td>
</tr>
<tr>
  <th>C(month)[10]</th> <td> 4.771e+04</td> <td> 1155.536</td> <td>   41.286</td> <td> 0.000</td> <td> 4.54e+04</td> <td>    5e+04</td>
</tr>
<tr>
  <th>C(month)[11]</th> <td> 4.771e+04</td> <td> 1155.536</td> <td>   41.287</td> <td> 0.000</td> <td> 4.54e+04</td> <td>    5e+04</td>
</tr>
<tr>
  <th>C(month)[12]</th> <td> 4.771e+04</td> <td> 1155.536</td> <td>   41.288</td> <td> 0.000</td> <td> 4.54e+04</td> <td>    5e+04</td>
</tr>
<tr>
  <th>time</th>         <td>  -49.2021</td> <td>    1.168</td> <td>  -42.120</td> <td> 0.000</td> <td>  -51.498</td> <td>  -46.907</td>
</tr>
<tr>
  <th>I(time ** 2)</th> <td>    0.0128</td> <td>    0.000</td> <td>   43.242</td> <td> 0.000</td> <td>    0.012</td> <td>    0.013</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td> 8.182</td> <th>  Durbin-Watson:     </th> <td>   0.169</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.017</td> <th>  Jarque-Bera (JB):  </th> <td>   7.581</td>
</tr>
<tr>
  <th>Skew:</th>          <td> 0.260</td> <th>  Prob(JB):          </th> <td>  0.0226</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 2.654</td> <th>  Cond. No.          </th> <td>4.68e+11</td>
</tr>
</table><br/><br/>Warnings:<br/>[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.<br/>[2] The condition number is large, 4.68e+11. This might indicate that there are<br/>strong multicollinearity or other numerical problems.




```python
plt.subplot(211)
plt.plot(df.value)
plt.plot(result3.fittedvalues, lw=2, alpha=0.9)
plt.subplot(212)
plt.plot(df.value - result3.fittedvalues) # result3.resid 와 같다.
plt.tight_layout()
plt.show()
```


<img width="422" alt="추세와 계절성 기초개념_28_0" src="https://user-images.githubusercontent.com/41605276/58452186-73676200-8151-11e9-99e1-92e2be59bf1a.png">

