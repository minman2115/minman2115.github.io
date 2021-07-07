---
layout: post
title: "상관분석 기초개념"
tags: [데이터전처리, 수학기초]
comments: true
---

.


Data_Preprocessing_TIL(20210707)

[학습자료]

패스트캠퍼스 온라인 강의 "파이썬을 활용한 데이터 전처리 Level UP 올인원 패키지 Online." 를 공부하고 정리한 내용입니다.

URL : https://fastcampus.co.kr/data_online_preprocess

[학습내용]

- 상관분석과 카이제곱검정

상관분석은 두 연속형 변수가 어떤 관계가 있는지 확인할때 사용하는 기법이다.

반면에 카이제곱검정은 두 범주형 변수가 어떤 관계가 있는지 확인할때 사용하는 기법이다.

여기서 말하는 "관계"는 x가 증가하면 y도 증가한다 또는 x가 감소하면 y도 감소한다 라는 형태의 상관관계 혹은 독립성을 보는 것이다.

- 상관분석 개요

목적 : 두 연속형 변수간에 어떠한 선형관계를 가지는지를 파악하기 위해서

여기서 선형관계라는 것은 x가 증가하면 y도 증가한다 혹은 x가 감소하면 y도 감소하는 관계이다.

영가설 : 두 변수간에는 유의미한 상관성이 존재하지 않는다.

대립가설 : 두 변수간에는 유의미한 상관성이 존재한다.

시각화 방법 : 산점도

상관관계의 경우 일반적인 검증방법처럼 영가설과 대립가설을 수립해서 검증하기보다는 통계량을 바탕으로 관계가 있다없다를 보는게 더 일반적이다. 

- 피어슨 상관계수

![1](https://user-images.githubusercontent.com/41605276/124750878-f5492c80-df60-11eb-99d6-da2f7ee22c76.png)

위에 수식에서 공분산은 x와 y가 같이 움직이는 정도라고 이해하면 된다. variance가 작고 같이 움직이는 정도가 클수록 통계량이 커지는 것을 알 수 있다. 이 통계량의 범위는 -1 ~ 1 사이의 범위이다.


상관계수가 1에 가까울수록 양의 상관관계가 강하다고 하며, -1에 가까울수록 음의 상관관계가 강한것이다. 상관계수가 0에 가까울수록 상관관계가 약하다고 판단하면 된다.

![2](https://user-images.githubusercontent.com/41605276/124751406-8ddfac80-df61-11eb-84cc-2feba0c21a69.PNG)

머신러닝 관점에서 상관계수가 1이라는 것은 x가 주어지면 y를 정확하게 예측할 수 있다는 의미이다.


- 스피어만 상관계수

피어슨 상관계수를 구하는데 x의 순위 그리고 y의 순위에 대한 피어슨 상관계수를 구한다고 생각하면 된다.

피어슨 상관계수는 데이터의 분포가 직선형태의 선형관계인 경우를 가늠하는 것이고, 스피어만 상관계수는 직선이던 곡선이던 x가 증가하면 y도 증가한다 또는 x가 감소하면 y도 감소하는거 자체를 확인하고자 하는 것이다.

따라서 x값과 y값이 있으면 이것들을 그대로 쓰는 것이 아니고, 아래 그림과 같이 오름차순으로 매기는 순위를 매긴다. 그런 다음에 r(x)와 r(y)의 피어슨 상관계수를 구하면 된다. 

![3](https://user-images.githubusercontent.com/41605276/124751713-ec0c8f80-df61-11eb-861d-9c14fff4e222.PNG)


- 파이썬을 이용한 상관분석

1) 피어슨 상관계수

코드


```python
scipy.stats.pearsonr(x,y)
```

결과해석

result = (statistics, pvalue)

statistics : 피어슨 상관계수

pvalue 0.05미만이면 유의한 상관성이 있다고 봄

2) 스피어만 상관계수

코드


```python
scipy.stats.spearmanr(x,y)
```

결과해석

result = (statistics, pvalue)

statistics : 스피어만 상관계수

pvalue 0.05미만이면 유의한 상관성이 있다고 봄

3) 상관행렬

코드


```python
DataFrame.corr(method)
# method : pearson, spearman
```

결과해석

컬럼간 상관계수를 나타내는 행렬


- 상관분석 실습

금값과 은값과 달러는 어떤 상관성이 존재하는지 확인하는 실습


```python
# data 불러오기
import os
import pandas as pd
# pwd 시 경로 : /c/Users/user/Desktop/aa/2. 탐색적 데이터 분석/데이터
os.chdir(r"C:/Users/user/Desktop/aa/2. 탐색적 데이터 분석/데이터/")

df = pd.read_excel("일별_금은달러.xlsx")
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
      <th>일자</th>
      <th>금값</th>
      <th>은값</th>
      <th>달러 환율</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>2020.08.07</td>
      <td>78538.90</td>
      <td>27.53</td>
      <td>1188.5</td>
    </tr>
    <tr>
      <td>1</td>
      <td>2020.08.06</td>
      <td>78009.22</td>
      <td>28.39</td>
      <td>1185.5</td>
    </tr>
    <tr>
      <td>2</td>
      <td>2020.08.05</td>
      <td>77748.00</td>
      <td>26.88</td>
      <td>1188.0</td>
    </tr>
    <tr>
      <td>3</td>
      <td>2020.08.04</td>
      <td>75873.57</td>
      <td>26.01</td>
      <td>1194.5</td>
    </tr>
    <tr>
      <td>4</td>
      <td>2020.08.03</td>
      <td>75761.76</td>
      <td>24.39</td>
      <td>1195.7</td>
    </tr>
    <tr>
      <td>5</td>
      <td>2020.07.31</td>
      <td>75628.90</td>
      <td>24.19</td>
      <td>1191.0</td>
    </tr>
    <tr>
      <td>6</td>
      <td>2020.07.30</td>
      <td>75110.59</td>
      <td>23.34</td>
      <td>1196.0</td>
    </tr>
    <tr>
      <td>7</td>
      <td>2020.07.29</td>
      <td>75110.45</td>
      <td>24.30</td>
      <td>1194.0</td>
    </tr>
    <tr>
      <td>8</td>
      <td>2020.07.28</td>
      <td>74342.85</td>
      <td>24.27</td>
      <td>1199.5</td>
    </tr>
    <tr>
      <td>9</td>
      <td>2020.07.27</td>
      <td>74332.14</td>
      <td>24.48</td>
      <td>1197.0</td>
    </tr>
    <tr>
      <td>10</td>
      <td>2020.07.24</td>
      <td>73293.98</td>
      <td>22.81</td>
      <td>1204.0</td>
    </tr>
    <tr>
      <td>11</td>
      <td>2020.07.23</td>
      <td>72451.43</td>
      <td>22.95</td>
      <td>1198.5</td>
    </tr>
    <tr>
      <td>12</td>
      <td>2020.07.22</td>
      <td>71170.07</td>
      <td>23.08</td>
      <td>1197.0</td>
    </tr>
    <tr>
      <td>13</td>
      <td>2020.07.21</td>
      <td>70129.97</td>
      <td>21.50</td>
      <td>1196.0</td>
    </tr>
    <tr>
      <td>14</td>
      <td>2020.07.20</td>
      <td>70104.72</td>
      <td>20.12</td>
      <td>1203.0</td>
    </tr>
    <tr>
      <td>15</td>
      <td>2020.07.17</td>
      <td>69793.91</td>
      <td>19.68</td>
      <td>1205.0</td>
    </tr>
    <tr>
      <td>16</td>
      <td>2020.07.16</td>
      <td>69966.63</td>
      <td>19.50</td>
      <td>1205.0</td>
    </tr>
    <tr>
      <td>17</td>
      <td>2020.07.15</td>
      <td>70004.80</td>
      <td>19.68</td>
      <td>1201.0</td>
    </tr>
    <tr>
      <td>18</td>
      <td>2020.07.14</td>
      <td>69827.02</td>
      <td>19.45</td>
      <td>1206.5</td>
    </tr>
    <tr>
      <td>19</td>
      <td>2020.07.13</td>
      <td>69933.64</td>
      <td>19.71</td>
      <td>1203.0</td>
    </tr>
    <tr>
      <td>20</td>
      <td>2020.07.10</td>
      <td>69735.18</td>
      <td>18.98</td>
      <td>1201.0</td>
    </tr>
    <tr>
      <td>21</td>
      <td>2020.07.09</td>
      <td>69748.21</td>
      <td>18.88</td>
      <td>1195.0</td>
    </tr>
    <tr>
      <td>22</td>
      <td>2020.07.08</td>
      <td>69184.17</td>
      <td>19.08</td>
      <td>1195.0</td>
    </tr>
    <tr>
      <td>23</td>
      <td>2020.07.07</td>
      <td>68443.14</td>
      <td>18.63</td>
      <td>1197.0</td>
    </tr>
    <tr>
      <td>24</td>
      <td>2020.07.06</td>
      <td>68269.27</td>
      <td>18.50</td>
      <td>1194.0</td>
    </tr>
    <tr>
      <td>25</td>
      <td>2020.07.02</td>
      <td>68278.07</td>
      <td>18.24</td>
      <td>1199.0</td>
    </tr>
    <tr>
      <td>26</td>
      <td>2020.07.01</td>
      <td>68941.28</td>
      <td>18.13</td>
      <td>1203.5</td>
    </tr>
    <tr>
      <td>27</td>
      <td>2020.06.30</td>
      <td>68559.78</td>
      <td>18.54</td>
      <td>1203.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.sort_values(by = '일자', inplace = True)
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
      <th>일자</th>
      <th>금값</th>
      <th>은값</th>
      <th>달러 환율</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>27</td>
      <td>2020.06.30</td>
      <td>68559.78</td>
      <td>18.54</td>
      <td>1203.0</td>
    </tr>
    <tr>
      <td>26</td>
      <td>2020.07.01</td>
      <td>68941.28</td>
      <td>18.13</td>
      <td>1203.5</td>
    </tr>
    <tr>
      <td>25</td>
      <td>2020.07.02</td>
      <td>68278.07</td>
      <td>18.24</td>
      <td>1199.0</td>
    </tr>
    <tr>
      <td>24</td>
      <td>2020.07.06</td>
      <td>68269.27</td>
      <td>18.50</td>
      <td>1194.0</td>
    </tr>
    <tr>
      <td>23</td>
      <td>2020.07.07</td>
      <td>68443.14</td>
      <td>18.63</td>
      <td>1197.0</td>
    </tr>
    <tr>
      <td>22</td>
      <td>2020.07.08</td>
      <td>69184.17</td>
      <td>19.08</td>
      <td>1195.0</td>
    </tr>
    <tr>
      <td>21</td>
      <td>2020.07.09</td>
      <td>69748.21</td>
      <td>18.88</td>
      <td>1195.0</td>
    </tr>
    <tr>
      <td>20</td>
      <td>2020.07.10</td>
      <td>69735.18</td>
      <td>18.98</td>
      <td>1201.0</td>
    </tr>
    <tr>
      <td>19</td>
      <td>2020.07.13</td>
      <td>69933.64</td>
      <td>19.71</td>
      <td>1203.0</td>
    </tr>
    <tr>
      <td>18</td>
      <td>2020.07.14</td>
      <td>69827.02</td>
      <td>19.45</td>
      <td>1206.5</td>
    </tr>
    <tr>
      <td>17</td>
      <td>2020.07.15</td>
      <td>70004.80</td>
      <td>19.68</td>
      <td>1201.0</td>
    </tr>
    <tr>
      <td>16</td>
      <td>2020.07.16</td>
      <td>69966.63</td>
      <td>19.50</td>
      <td>1205.0</td>
    </tr>
    <tr>
      <td>15</td>
      <td>2020.07.17</td>
      <td>69793.91</td>
      <td>19.68</td>
      <td>1205.0</td>
    </tr>
    <tr>
      <td>14</td>
      <td>2020.07.20</td>
      <td>70104.72</td>
      <td>20.12</td>
      <td>1203.0</td>
    </tr>
    <tr>
      <td>13</td>
      <td>2020.07.21</td>
      <td>70129.97</td>
      <td>21.50</td>
      <td>1196.0</td>
    </tr>
    <tr>
      <td>12</td>
      <td>2020.07.22</td>
      <td>71170.07</td>
      <td>23.08</td>
      <td>1197.0</td>
    </tr>
    <tr>
      <td>11</td>
      <td>2020.07.23</td>
      <td>72451.43</td>
      <td>22.95</td>
      <td>1198.5</td>
    </tr>
    <tr>
      <td>10</td>
      <td>2020.07.24</td>
      <td>73293.98</td>
      <td>22.81</td>
      <td>1204.0</td>
    </tr>
    <tr>
      <td>9</td>
      <td>2020.07.27</td>
      <td>74332.14</td>
      <td>24.48</td>
      <td>1197.0</td>
    </tr>
    <tr>
      <td>8</td>
      <td>2020.07.28</td>
      <td>74342.85</td>
      <td>24.27</td>
      <td>1199.5</td>
    </tr>
    <tr>
      <td>7</td>
      <td>2020.07.29</td>
      <td>75110.45</td>
      <td>24.30</td>
      <td>1194.0</td>
    </tr>
    <tr>
      <td>6</td>
      <td>2020.07.30</td>
      <td>75110.59</td>
      <td>23.34</td>
      <td>1196.0</td>
    </tr>
    <tr>
      <td>5</td>
      <td>2020.07.31</td>
      <td>75628.90</td>
      <td>24.19</td>
      <td>1191.0</td>
    </tr>
    <tr>
      <td>4</td>
      <td>2020.08.03</td>
      <td>75761.76</td>
      <td>24.39</td>
      <td>1195.7</td>
    </tr>
    <tr>
      <td>3</td>
      <td>2020.08.04</td>
      <td>75873.57</td>
      <td>26.01</td>
      <td>1194.5</td>
    </tr>
    <tr>
      <td>2</td>
      <td>2020.08.05</td>
      <td>77748.00</td>
      <td>26.88</td>
      <td>1188.0</td>
    </tr>
    <tr>
      <td>1</td>
      <td>2020.08.06</td>
      <td>78009.22</td>
      <td>28.39</td>
      <td>1185.5</td>
    </tr>
    <tr>
      <td>0</td>
      <td>2020.08.07</td>
      <td>78538.90</td>
      <td>27.53</td>
      <td>1188.5</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 그래프 기본 설정
from matplotlib import pyplot as plt
import numpy as np
%matplotlib inline
plt.rcParams["font.family"] = 'Malgun Gothic'
plt.rcParams["font.size"] = 20
plt.rcParams["figure.figsize"] = (10, 8)

# 파란점선으로 시간이 흐르면 흐를수록 증가하는 형태를 보인다.
plt.scatter(df['일자'], df['금값'])
# 초록점선은 데이터 스케일이 작아서 형태가 잘 안보인다.
plt.scatter(df['일자'], df['은값'])
# 주황점선은 데이터 스케일이 작아서 형태가 잘 안보인다.
plt.scatter(df['일자'], df['달러 환율'])
plt.xticks(df['일자'].iloc[::8])
```




    ([<matplotlib.axis.XTick at 0x2104c239388>,
      <matplotlib.axis.XTick at 0x2104bbb5348>,
      <matplotlib.axis.XTick at 0x2104be91588>,
      <matplotlib.axis.XTick at 0x2104c292248>],
     <a list of 4 Text xticklabel objects>)




![4](https://user-images.githubusercontent.com/41605276/124755941-ee251d00-df66-11eb-98b2-d2fe5536d9f5.png)


금값이 증가하니까 은값도 증가하는 형태를 보인다.


```python
plt.scatter(df['금값'], df['은값'])
```




    <matplotlib.collections.PathCollection at 0x2104c5fae08>




![5](https://user-images.githubusercontent.com/41605276/124756014-fbdaa280-df66-11eb-9aca-831c1a46631d.png)



```python
pd.plotting.scatter_matrix(df.drop('일자', axis = 1))
```




    array([[<matplotlib.axes._subplots.AxesSubplot object at 0x000002104BECEA08>,
            <matplotlib.axes._subplots.AxesSubplot object at 0x000002104C304D08>,
            <matplotlib.axes._subplots.AxesSubplot object at 0x000002104C33E3C8>],
           [<matplotlib.axes._subplots.AxesSubplot object at 0x000002104C373E48>,
            <matplotlib.axes._subplots.AxesSubplot object at 0x000002104C3ABF48>,
            <matplotlib.axes._subplots.AxesSubplot object at 0x000002104C3E9088>],
           [<matplotlib.axes._subplots.AxesSubplot object at 0x000002104C41D108>,
            <matplotlib.axes._subplots.AxesSubplot object at 0x000002104C637208>,
            <matplotlib.axes._subplots.AxesSubplot object at 0x000002104C63ED88>]],
          dtype=object)




![6](https://user-images.githubusercontent.com/41605276/124756096-13199000-df67-11eb-984a-d6d7557f6b4a.png)


피어슨 상관계수로 상관관계를 확인해보자

금값과 은값은 선형관계를 보인다.

반면에 금값과 은값이 증가하면 달러는 떨어지는 추세로 확인할 수 있다.

pvalue도 0.05미만으로 모두 유의한 상관관계를 가진다.


```python
from scipy.stats import *
print(pearsonr(df['금값'], df['은값']))
print(pearsonr(df['금값'], df['달러 환율']))
print(pearsonr(df['은값'], df['달러 환율']))
```

    (0.9718640116033496, 7.508878356557318e-18)
    (-0.6793266264341937, 7.033325258452259e-05)
    (-0.6954569556203559, 3.989042061957972e-05)
    


```python
import itertools
target_columns = ['금값', '은값', '달러 환율']
for col1, col2 in itertools.combinations(target_columns, 2):
    result = pearsonr(df[col1], df[col2])
    print("{} ~ {}: coef:{}, p-value: {}".format(col1, col2, result[0], result[1]))
```

    금값 ~ 은값: coef:0.9718640116033496, p-value: 7.508878356557318e-18
    금값 ~ 달러 환율: coef:-0.6793266264341937, p-value: 7.033325258452259e-05
    은값 ~ 달러 환율: coef:-0.6954569556203559, p-value: 3.989042061957972e-05
    

스피어만 상관계수도 계산해보자.

선형관계에 비해서는 순위값 관계가 크지 않다는 것을 확인할 수 있다.


```python
import itertools
target_columns = ['금값', '은값', '달러 환율']
for col1, col2 in itertools.combinations(target_columns, 2):
    result = spearmanr(df[col1], df[col2])
    print("{} ~ {}: coef:{}, p-value: {}".format(col1, col2, result[0], result[1]))
```

    금값 ~ 은값: coef:0.9711235892228813, p-value: 1.0481280432981455e-17
    금값 ~ 달러 환율: coef:-0.5039075855936102, p-value: 0.006257468267109453
    은값 ~ 달러 환율: coef:-0.528106006718531, p-value: 0.003869997604824081
    


```python
df.drop('일자', axis = 1).corr(method = 'pearson')
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
      <th>금값</th>
      <th>은값</th>
      <th>달러 환율</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>금값</td>
      <td>1.000000</td>
      <td>0.971864</td>
      <td>-0.679327</td>
    </tr>
    <tr>
      <td>은값</td>
      <td>0.971864</td>
      <td>1.000000</td>
      <td>-0.695457</td>
    </tr>
    <tr>
      <td>달러 환율</td>
      <td>-0.679327</td>
      <td>-0.695457</td>
      <td>1.000000</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.drop('일자', axis = 1).corr(method = 'spearman')
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
      <th>금값</th>
      <th>은값</th>
      <th>달러 환율</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>금값</td>
      <td>1.000000</td>
      <td>0.971124</td>
      <td>-0.503908</td>
    </tr>
    <tr>
      <td>은값</td>
      <td>0.971124</td>
      <td>1.000000</td>
      <td>-0.528106</td>
    </tr>
    <tr>
      <td>달러 환율</td>
      <td>-0.503908</td>
      <td>-0.528106</td>
      <td>1.000000</td>
    </tr>
  </tbody>
</table>
</div>


