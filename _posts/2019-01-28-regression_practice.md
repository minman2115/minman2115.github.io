---
layout: post
title: "선형회귀모형 구현연습"
tags: [머신러닝, 선형회귀분석]
comments: true
---

선형회귀모형 관련 연습문제 풀이

#### '패스트캠퍼스'에서 공부한 내용을 필기노트 형식으로 정리한 것으로 일부 주관적이며 오류가 있을 수 있습니다.

- 패스트캠퍼스 : https://www.fastcampus.co.kr
- 자료인용 출처 : https://datascienceschool.net

### 1. 다음은 경력과 연봉간의 관계를 조사한 데이터이다. 차후 나오는 문제들에 대한 풀이를 하시오


```python
# 필요한 패키지 임포트
%matplotlib inline
import numpy as np
import matplotlib as mpl
import matplotlib.pyplot as plt
from patsy import *
```


```python
# 데이터 로드
df1 = pd.read_csv("http://gattonweb.uky.edu/sheather/book/docs/datasets/profsalary.txt", sep = "\t")
del df1["Case"]
df1.tail()
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
      <th>Salary</th>
      <th>Experience</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>138</th>
      <td>43</td>
      <td>3</td>
    </tr>
    <tr>
      <th>139</th>
      <td>63</td>
      <td>12</td>
    </tr>
    <tr>
      <th>140</th>
      <td>67</td>
      <td>16</td>
    </tr>
    <tr>
      <th>141</th>
      <td>71</td>
      <td>20</td>
    </tr>
    <tr>
      <th>142</th>
      <td>69</td>
      <td>31</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 데이터 분포 확인
plt.scatter(df1.Experience, df1.Salary)
plt.xlabel('Experience')
plt.ylabel('Salary')
plt.show()
```


![regression_practice_3_0](https://user-images.githubusercontent.com/41605276/51816677-0b238380-230b-11e9-80cc-7d6e6923b99c.png)


### (1) statsmodels를 사용하여 경력에서 연봉을 예측하는 2차 다항회귀모형을 만들어라


```python
sm.OLS.from_formula("Salary ~ Experience + I(Experience**2)", data=df1).fit().summary()
```




<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>         <td>Salary</td>      <th>  R-squared:         </th> <td>   0.925</td>
</tr>
<tr>
  <th>Model:</th>                   <td>OLS</td>       <th>  Adj. R-squared:    </th> <td>   0.924</td>
</tr>
<tr>
  <th>Method:</th>             <td>Least Squares</td>  <th>  F-statistic:       </th> <td>   859.3</td>
</tr>
<tr>
  <th>Date:</th>             <td>Sat, 19 Jan 2019</td> <th>  Prob (F-statistic):</th> <td>2.43e-79</td>
</tr>
<tr>
  <th>Time:</th>                 <td>23:17:19</td>     <th>  Log-Likelihood:    </th> <td> -349.51</td>
</tr>
<tr>
  <th>No. Observations:</th>      <td>   143</td>      <th>  AIC:               </th> <td>   705.0</td>
</tr>
<tr>
  <th>Df Residuals:</th>          <td>   140</td>      <th>  BIC:               </th> <td>   713.9</td>
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
           <td></td>             <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>Intercept</th>          <td>   34.7205</td> <td>    0.829</td> <td>   41.896</td> <td> 0.000</td> <td>   33.082</td> <td>   36.359</td>
</tr>
<tr>
  <th>Experience</th>         <td>    2.8723</td> <td>    0.096</td> <td>   30.014</td> <td> 0.000</td> <td>    2.683</td> <td>    3.061</td>
</tr>
<tr>
  <th>I(Experience ** 2)</th> <td>   -0.0533</td> <td>    0.002</td> <td>  -21.526</td> <td> 0.000</td> <td>   -0.058</td> <td>   -0.048</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td>31.087</td> <th>  Durbin-Watson:     </th> <td>   1.969</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.000</td> <th>  Jarque-Bera (JB):  </th> <td>   6.892</td>
</tr>
<tr>
  <th>Skew:</th>          <td> 0.030</td> <th>  Prob(JB):          </th> <td>  0.0319</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 1.926</td> <th>  Cond. No.          </th> <td>2.04e+03</td>
</tr>
</table><br/><br/>Warnings:<br/>[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.<br/>[2] The condition number is large, 2.04e+03. This might indicate that there are<br/>strong multicollinearity or other numerical problems.



### (2) 다음 플롯과 같이 스캐터 플롯 위에 모형의 예측값을 나타내는 코드를 완성하라.


```python
result = sm.OLS.from_formula("Salary ~ Experience + I(Experience**2)", data=df1).fit()
```


```python
xnew1 = pd.DataFrame(np.linspace(0,35,100),columns=["Experience"])
ypred1 = result.predict(xnew1)
plt.plot(xnew1, ypred1,'r-')
plt.scatter(df1.Experience, df1.Salary)
plt.xlabel("Experience")
plt.ylabel("Salary")
plt.show()
```


![regression_practice_8_0](https://user-images.githubusercontent.com/41605276/51816683-15458200-230b-11e9-9fdf-927cace8cba6.png)


### (3) 레버리지가 평균의 3배 이상인 데이터를 찾아서 그림과 같이 스캐터 플롯 위에 표시하여라


```python
influence = result.get_influence()
h = influence.hat_matrix_diag
idx = h > ( 3 * h.mean() )
df1[idx]
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
      <th>Salary</th>
      <th>Experience</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>21</th>
      <td>67</td>
      <td>36</td>
    </tr>
    <tr>
      <th>40</th>
      <td>65</td>
      <td>36</td>
    </tr>
    <tr>
      <th>46</th>
      <td>40</td>
      <td>1</td>
    </tr>
    <tr>
      <th>60</th>
      <td>37</td>
      <td>1</td>
    </tr>
    <tr>
      <th>65</th>
      <td>69</td>
      <td>36</td>
    </tr>
  </tbody>
</table>
</div>




```python
xnew1 = pd.DataFrame(np.linspace(0,35,100),columns=["Experience"])
ypred1 = result.predict(xnew1)
plt.plot(xnew1, ypred1,'r-')
plt.scatter(df1.Experience, df1.Salary)
plt.scatter(df1.Experience[idx], df1[idx].Salary, s=200, c='r', alpha=0.5)
plt.xlabel("Experience")
plt.ylabel("Salary")
plt.show()
```


![regression_practice_11_0](https://user-images.githubusercontent.com/41605276/51816690-1eceea00-230b-11e9-884d-d0daaa585bc4.png)


### 2. 다음은 Defective와 Temperature, Density, Rate 간의 관계를 조사한 데이터이다. 차후 나오는 문제들에 대한 풀이를 하시오

* 종속변수 : Defective


```python
df2 = pd.read_csv("http://gattonweb.uky.edu/sheather/book/docs/datasets/defects.txt", sep = "\t")
del df2["Case"]
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
      <th>Temperature</th>
      <th>Density</th>
      <th>Rate</th>
      <th>Defective</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>25</th>
      <td>2.44</td>
      <td>23.47</td>
      <td>236.0</td>
      <td>36.7</td>
    </tr>
    <tr>
      <th>26</th>
      <td>1.87</td>
      <td>26.51</td>
      <td>237.3</td>
      <td>24.5</td>
    </tr>
    <tr>
      <th>27</th>
      <td>1.45</td>
      <td>30.70</td>
      <td>221.0</td>
      <td>2.8</td>
    </tr>
    <tr>
      <th>28</th>
      <td>2.82</td>
      <td>22.30</td>
      <td>253.2</td>
      <td>60.8</td>
    </tr>
    <tr>
      <th>29</th>
      <td>1.74</td>
      <td>28.47</td>
      <td>207.9</td>
      <td>10.5</td>
    </tr>
  </tbody>
</table>
</div>



### (1) 이 데이터를 이용하여 선형회귀모형을 구하라


```python
result2 = sm.OLS.from_formula("Defective ~ scale(Temperature) + scale(Density) + scale(Rate)", data=df2).fit()
result2.summary()
```




<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>        <td>Defective</td>    <th>  R-squared:         </th> <td>   0.880</td>
</tr>
<tr>
  <th>Model:</th>                   <td>OLS</td>       <th>  Adj. R-squared:    </th> <td>   0.866</td>
</tr>
<tr>
  <th>Method:</th>             <td>Least Squares</td>  <th>  F-statistic:       </th> <td>   63.36</td>
</tr>
<tr>
  <th>Date:</th>             <td>Sat, 19 Jan 2019</td> <th>  Prob (F-statistic):</th> <td>4.37e-12</td>
</tr>
<tr>
  <th>Time:</th>                 <td>23:22:59</td>     <th>  Log-Likelihood:    </th> <td> -99.268</td>
</tr>
<tr>
  <th>No. Observations:</th>      <td>    30</td>      <th>  AIC:               </th> <td>   206.5</td>
</tr>
<tr>
  <th>Df Residuals:</th>          <td>    26</td>      <th>  BIC:               </th> <td>   212.1</td>
</tr>
<tr>
  <th>Df Model:</th>              <td>     3</td>      <th>                     </th>     <td> </td>   
</tr>
<tr>
  <th>Covariance Type:</th>      <td>nonrobust</td>    <th>                     </th>     <td> </td>   
</tr>
</table>
<table class="simpletable">
<tr>
           <td></td>             <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>Intercept</th>          <td>   27.1433</td> <td>    1.298</td> <td>   20.909</td> <td> 0.000</td> <td>   24.475</td> <td>   29.812</td>
</tr>
<tr>
  <th>scale(Temperature)</th> <td>    9.2224</td> <td>    4.758</td> <td>    1.938</td> <td> 0.063</td> <td>   -0.557</td> <td>   19.002</td>
</tr>
<tr>
  <th>scale(Density)</th>     <td>   -6.0354</td> <td>    4.945</td> <td>   -1.221</td> <td> 0.233</td> <td>  -16.199</td> <td>    4.129</td>
</tr>
<tr>
  <th>scale(Rate)</th>        <td>    2.9899</td> <td>    3.346</td> <td>    0.894</td> <td> 0.380</td> <td>   -3.887</td> <td>    9.867</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td> 2.091</td> <th>  Durbin-Watson:     </th> <td>   2.213</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.351</td> <th>  Jarque-Bera (JB):  </th> <td>   1.491</td>
</tr>
<tr>
  <th>Skew:</th>          <td> 0.545</td> <th>  Prob(JB):          </th> <td>   0.474</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 2.948</td> <th>  Cond. No.          </th> <td>    8.38</td>
</tr>
</table><br/><br/>Warnings:<br/>[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.



### (2) 실제 Defective와 이 모형에서 예측한  Defective 간의 비선형 회귀모형을 구하여 실제 데이터와 회귀 결과를 플롯으로 나타내라. 어떤 비선형 함수를 사용해야 하는가


```python
ypred2 = result2.predict(df2)
df3 = df2.copy()
df3["DefectiveHat"] = ypred2

## np.sqrt => 제곱근(루트) 구하는 함수
model3 = sm.OLS.from_formula("DefectiveHat ~ np.sqrt(Defective)",data = df3)
result3 = model3.fit()
result3.summary()
```




<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>      <td>DefectiveHat</td>   <th>  R-squared:         </th> <td>   0.942</td>
</tr>
<tr>
  <th>Model:</th>                   <td>OLS</td>       <th>  Adj. R-squared:    </th> <td>   0.940</td>
</tr>
<tr>
  <th>Method:</th>             <td>Least Squares</td>  <th>  F-statistic:       </th> <td>   456.3</td>
</tr>
<tr>
  <th>Date:</th>             <td>Sat, 19 Jan 2019</td> <th>  Prob (F-statistic):</th> <td>7.17e-19</td>
</tr>
<tr>
  <th>Time:</th>                 <td>23:23:35</td>     <th>  Log-Likelihood:    </th> <td> -86.351</td>
</tr>
<tr>
  <th>No. Observations:</th>      <td>    30</td>      <th>  AIC:               </th> <td>   176.7</td>
</tr>
<tr>
  <th>Df Residuals:</th>          <td>    28</td>      <th>  BIC:               </th> <td>   179.5</td>
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
           <td></td>             <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>Intercept</th>          <td>   -9.8560</td> <td>    1.914</td> <td>   -5.151</td> <td> 0.000</td> <td>  -13.776</td> <td>   -5.936</td>
</tr>
<tr>
  <th>np.sqrt(Defective)</th> <td>    7.8455</td> <td>    0.367</td> <td>   21.361</td> <td> 0.000</td> <td>    7.093</td> <td>    8.598</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td> 0.724</td> <th>  Durbin-Watson:     </th> <td>   1.684</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.696</td> <th>  Jarque-Bera (JB):  </th> <td>   0.600</td>
</tr>
<tr>
  <th>Skew:</th>          <td>-0.324</td> <th>  Prob(JB):          </th> <td>   0.741</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 2.756</td> <th>  Cond. No.          </th> <td>    12.6</td>
</tr>
</table><br/><br/>Warnings:<br/>[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.




```python
plt.scatter(df3.Defective, df3.DefectiveHat)
xnew3 = pd.DataFrame(np.linspace(0,70,100),columns = ["Defective"])
ypred3 = result3.predict(xnew3)
plt.plot(xnew3, ypred3,'r-')
plt.xlabel("Defective")
plt.ylabel("Defective(Predicted)")
plt.title("Defective vs Predicted Defective")
plt.show()
```


![regression_practice_18_0](https://user-images.githubusercontent.com/41605276/51816702-2db59c80-230b-11e9-83ba-1972f9088a6e.png)


### (3) 위에서 구한 비선형 변환함수를 사용하여 Defective와 예측된 Defective간의 관계를 나타낼 수 있도록 비선형 회귀모형을 만들고 그림을 그려라


```python
model4 = sm.OLS.from_formula("np.sqrt(Defective) ~ scale(Temperature) + scale(Density) + scale(Rate)",data = df3)
result4 = model4.fit()
result4.summary()
```




<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>    <td>np.sqrt(Defective)</td> <th>  R-squared:         </th> <td>   0.943</td>
</tr>
<tr>
  <th>Model:</th>                    <td>OLS</td>        <th>  Adj. R-squared:    </th> <td>   0.936</td>
</tr>
<tr>
  <th>Method:</th>              <td>Least Squares</td>   <th>  F-statistic:       </th> <td>   143.5</td>
</tr>
<tr>
  <th>Date:</th>              <td>Sat, 19 Jan 2019</td>  <th>  Prob (F-statistic):</th> <td>2.71e-16</td>
</tr>
<tr>
  <th>Time:</th>                  <td>23:25:52</td>      <th>  Log-Likelihood:    </th> <td> -23.438</td>
</tr>
<tr>
  <th>No. Observations:</th>       <td>    30</td>       <th>  AIC:               </th> <td>   54.88</td>
</tr>
<tr>
  <th>Df Residuals:</th>           <td>    26</td>       <th>  BIC:               </th> <td>   60.48</td>
</tr>
<tr>
  <th>Df Model:</th>               <td>     3</td>       <th>                     </th>     <td> </td>   
</tr>
<tr>
  <th>Covariance Type:</th>       <td>nonrobust</td>     <th>                     </th>     <td> </td>   
</tr>
</table>
<table class="simpletable">
<tr>
           <td></td>             <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>Intercept</th>          <td>    4.7160</td> <td>    0.104</td> <td>   45.498</td> <td> 0.000</td> <td>    4.503</td> <td>    4.929</td>
</tr>
<tr>
  <th>scale(Temperature)</th> <td>    0.8978</td> <td>    0.380</td> <td>    2.363</td> <td> 0.026</td> <td>    0.117</td> <td>    1.679</td>
</tr>
<tr>
  <th>scale(Density)</th>     <td>   -0.9633</td> <td>    0.395</td> <td>   -2.440</td> <td> 0.022</td> <td>   -1.775</td> <td>   -0.152</td>
</tr>
<tr>
  <th>scale(Rate)</th>        <td>    0.3304</td> <td>    0.267</td> <td>    1.237</td> <td> 0.227</td> <td>   -0.219</td> <td>    0.879</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td> 1.136</td> <th>  Durbin-Watson:     </th> <td>   1.742</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.567</td> <th>  Jarque-Bera (JB):  </th> <td>   0.729</td>
</tr>
<tr>
  <th>Skew:</th>          <td> 0.380</td> <th>  Prob(JB):          </th> <td>   0.695</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 2.935</td> <th>  Cond. No.          </th> <td>    8.38</td>
</tr>
</table><br/><br/>Warnings:<br/>[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.




```python
ypred4 = result4.predict(df3)
df4 = df3.copy()
df4["DefectiveHat"] = ypred4 ** 2
plt.scatter(df4.Defective, df4.DefectiveHat)
plt.show()
```


![regression_practice_21_0](https://user-images.githubusercontent.com/41605276/51816709-37d79b00-230b-11e9-8a09-76e4a23b5596.png)

