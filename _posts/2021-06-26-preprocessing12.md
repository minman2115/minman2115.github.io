---
layout: post
title: "독립 표본 t검정 기초개념"
tags: [데이터전처리, 수학기초]
comments: true
---

.

Data_Preprocessing_TIL(20210626)

[학습자료]

패스트캠퍼스 온라인 강의 "파이썬을 활용한 데이터 전처리 Level UP 올인원 패키지 Online." 를 공부하고 정리한 내용입니다.

URL : https://fastcampus.co.kr/data_online_preprocess

[학습내용]

- 독립 표본 t검정의 목적 : 서로 다른 두 그룹의 데이터 평균을 비교하는 가설검정 기법


- 영가설과 대립가설

영가설은 서로다른 두 그룹의 데이터 평균이 같다.

대립가설은 서로다른 두 그룹의 데이터 평균이 같지 않다.

![1](https://user-images.githubusercontent.com/41605276/123505669-5dfbf380-d69b-11eb-8295-4edbeff71fe5.png)

지점 A의 평균이 200이고 지점 B의 평균이 180이라고 한다면 디 두지점의 평균의 차이가 유의한 차이인지 아니면 단순한 우연에 의한 차이인지를 알고 싶은 것이다.

- 독립 표본 t검정을 하기 위한 선행조건

1) 독립성 : 두 그룹은 서로 독립적이어야 한다.

한 그룹의 평균이 다른 그룹의 평균에 영향을 주지 않는 확률적으로 독립이어야 한다.

2) 정규성 : 전체 데이터는 정규분포를 따라야 한다.

정규성을 따르지 않으면 비모수 검정인 Mann-Whitney 검정을 수행해야 한다.

3) 등분산성 : 두 그룹의 데이터에 대한 분산이 같아야 한다.

두 그룹의 분산이 다르면 평균이 차이가 있다, 같다라고 했을때 분산의 영향을 받기 때문이다. 만약에 한 그룹의 분산은 매우크고, 다른 한 그룹의 분산은 매우 작다면 그런데 두 그룹의 차이가 유의하지 않다(평균이 비슷하다)고 가정해보자. 이거는 단순히 한쪽의 분산이 커서 평균이 같을 수 있기 때문이다.

Levene의 등분산 검정 : p-value가 0.05 미만이면 분산이 다르다고 판단

분산이 같을때 사용하는 통계량이 있고, 분산이 다를때 사용하는 통계량이 있다. 즉 분산이 같은지 여부에 따라서 사용하는 통계량이 다르다 라는 것만 알고가자.

![2](https://user-images.githubusercontent.com/41605276/123506025-03fc2d80-d69d-11eb-96c2-29b950ab1c28.png)

통계량의 분자에는 두 그룹의 평균차이가 들어가고, 분모에는 어떤 분산이 들어가는 형태이다. 만약에 두 그룹의 평균차이가 완전히 같다라고 하면 0에 가까워질 것이고, 차이가 크면 클수록 t의 절대값이 커질 것이다.

- python을 이용한 독립표본 t검정

1) 정규성 검정(Kolmogorov-Smornov 검정)

코드


```python
scipy.stats.kstest(x,'norn')
```

결과해석

pvalue가 특정수치(0.05) 미만이면 정규성을 따른다고 판단

2) 등분산성 검정(Levene test)

코드


```python
# s1, s2, s3, s4, ... : 배열형태의 샘플데이터 
# 독립표본 t검정을 할경우에는 실질적으로 s1과 s2까지만 파라미터로 들어갈 것이다.
scipy.stats.levene(s1,s2,s3,s4,...)
```

결과해석

pvalue가 특정수치(0.05) 미만이면 샘플간 분산이 같지 않다고 판단

3) 독립표본 t검정

코드


```python
# a,b : 배열형태의 두 그룹의 데이터 
# equal_var : 등분산성 검정에서 등분산성을 만족하는지 여부 (True 또는 False)
scipy.stats.ttest_ind(a,b,equal_var)
```

결과해석

statistics가 양수면 a의 평균이 더 크다고 판단

pvalue가 특정수치 미만이면 a와 b의 평균이 같지 않다고 판단

4) Mann-Whitneyu 검정 (정규성 검정을 만족하지 않을때 사용하는 검정)

코드


```python
# a,b : 배열형태의 두 그룹의 데이터 
scipy.stats.mannwhitneyu(a,b)
```

`result=(statistics,pvalue)`의 튜플형태

pvalue가 특정수치 미만이면 a와 b의 평균이 같지 않다고 판단

- python을 이용한 실제 실습

반별로 점수차이가 있는지 독립표본 t검정으로 검증을 해보자

step 1) 데이터 load


```python
# data 불러오기
import os
import pandas as pd
# pwd 시 경로 : /c/Users/user/Desktop/aa/2. 탐색적 데이터 분석/데이터
os.chdir(r"C:/Users/user/Desktop/aa/2. 탐색적 데이터 분석/데이터/")

df1 = pd.read_csv("반별_점수_type1.csv", engine = "python")
df1
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
      <th>반</th>
      <th>점수</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>A</td>
      <td>73</td>
    </tr>
    <tr>
      <td>1</td>
      <td>A</td>
      <td>69</td>
    </tr>
    <tr>
      <td>2</td>
      <td>A</td>
      <td>71</td>
    </tr>
    <tr>
      <td>3</td>
      <td>A</td>
      <td>71</td>
    </tr>
    <tr>
      <td>4</td>
      <td>A</td>
      <td>73</td>
    </tr>
    <tr>
      <td>5</td>
      <td>A</td>
      <td>67</td>
    </tr>
    <tr>
      <td>6</td>
      <td>A</td>
      <td>73</td>
    </tr>
    <tr>
      <td>7</td>
      <td>A</td>
      <td>69</td>
    </tr>
    <tr>
      <td>8</td>
      <td>A</td>
      <td>62</td>
    </tr>
    <tr>
      <td>9</td>
      <td>A</td>
      <td>74</td>
    </tr>
    <tr>
      <td>10</td>
      <td>A</td>
      <td>68</td>
    </tr>
    <tr>
      <td>11</td>
      <td>A</td>
      <td>66</td>
    </tr>
    <tr>
      <td>12</td>
      <td>A</td>
      <td>70</td>
    </tr>
    <tr>
      <td>13</td>
      <td>A</td>
      <td>82</td>
    </tr>
    <tr>
      <td>14</td>
      <td>A</td>
      <td>70</td>
    </tr>
    <tr>
      <td>15</td>
      <td>A</td>
      <td>65</td>
    </tr>
    <tr>
      <td>16</td>
      <td>A</td>
      <td>76</td>
    </tr>
    <tr>
      <td>17</td>
      <td>A</td>
      <td>73</td>
    </tr>
    <tr>
      <td>18</td>
      <td>A</td>
      <td>58</td>
    </tr>
    <tr>
      <td>19</td>
      <td>A</td>
      <td>81</td>
    </tr>
    <tr>
      <td>20</td>
      <td>B</td>
      <td>63</td>
    </tr>
    <tr>
      <td>21</td>
      <td>B</td>
      <td>56</td>
    </tr>
    <tr>
      <td>22</td>
      <td>B</td>
      <td>73</td>
    </tr>
    <tr>
      <td>23</td>
      <td>B</td>
      <td>61</td>
    </tr>
    <tr>
      <td>24</td>
      <td>B</td>
      <td>55</td>
    </tr>
    <tr>
      <td>25</td>
      <td>B</td>
      <td>77</td>
    </tr>
    <tr>
      <td>26</td>
      <td>B</td>
      <td>75</td>
    </tr>
    <tr>
      <td>27</td>
      <td>B</td>
      <td>65</td>
    </tr>
    <tr>
      <td>28</td>
      <td>B</td>
      <td>61</td>
    </tr>
    <tr>
      <td>29</td>
      <td>B</td>
      <td>55</td>
    </tr>
  </tbody>
</table>
</div>




```python
group_A = df1['점수'].loc[df1['반'] == 'A'].values
group_B = df1['점수'].loc[df1['반'] == 'B'].values

print(type(group_A))
print(group_A)
print(group_B)
```

    <class 'numpy.ndarray'>
    [73 69 71 71 73 67 73 69 62 74 68 66 70 82 70 65 76 73 58 81]
    [63 56 73 61 55 77 75 65 61 55]
    

step 2) load 한 데이터에 대한 정규성 검정


```python
# kstest를 이용한 정규성 검정: 모두 정규 분포를 띔을 확인
from scipy.stats import *

print(kstest(group_A, 'norm'))
print(kstest(group_B, 'norm'))
```

    KstestResult(statistic=1.0, pvalue=0.0)
    KstestResult(statistic=1.0, pvalue=0.0)
    

step 3) load 한 데이터에 대한 등분산성 검정


```python
# 등분산 검정
levene(group_A, group_B) 
# pvalue가 0.05 미만이 아니므로, 등분산을 띈다고 볼 수 있음
```




    LeveneResult(statistic=2.033067087400979, pvalue=0.164964086222101)




```python
import numpy as np
np.var(group_A, None, ddof=1) # 분산이 32.26으로 나온다
```




    32.26052631578948



step 4) 독립표본 t검정 실시


```python
# A와 B간에는 차이가 존재함을 확인 
print(ttest_ind(group_A, group_B, equal_var = True)) 
```

    Ttest_indResult(statistic=2.5128526794964134, pvalue=0.01801095352893767)
    

p-value가 0.018로 0.05보다 작기 때문에 그룹a와 그룹b의 유의미한 차이가 존재한다고 볼 수 있다. 그리고 statistics가 양수인걸로 보아 그룹a가 그룹b보다 성적이 좋겠다라고 판단할 수 있다.

#### Tip. 다른 포맷의 데이터 처리

a반은 전부 양수인데 b반은 .0이 붙어있는 것을 볼 수 있다. 왜 두반의 포맷이 다를지 생각할 수 있는데 이는 b반에 결측값이 있기 때문이다. 왜냐하면 NaN값의 경우에는 type이 float이기 때문이다. 아래에 데이터를 확인해봐도 알 수 있다.


```python
df2 = pd.read_csv("반별_점수_type2.csv", engine = "python")
df2
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
      <th>A반</th>
      <th>B반</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>73</td>
      <td>63.0</td>
    </tr>
    <tr>
      <td>1</td>
      <td>69</td>
      <td>56.0</td>
    </tr>
    <tr>
      <td>2</td>
      <td>71</td>
      <td>73.0</td>
    </tr>
    <tr>
      <td>3</td>
      <td>71</td>
      <td>61.0</td>
    </tr>
    <tr>
      <td>4</td>
      <td>73</td>
      <td>55.0</td>
    </tr>
    <tr>
      <td>5</td>
      <td>67</td>
      <td>77.0</td>
    </tr>
    <tr>
      <td>6</td>
      <td>73</td>
      <td>75.0</td>
    </tr>
    <tr>
      <td>7</td>
      <td>69</td>
      <td>65.0</td>
    </tr>
    <tr>
      <td>8</td>
      <td>62</td>
      <td>61.0</td>
    </tr>
    <tr>
      <td>9</td>
      <td>74</td>
      <td>55.0</td>
    </tr>
    <tr>
      <td>10</td>
      <td>68</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>11</td>
      <td>66</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>12</td>
      <td>70</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>13</td>
      <td>82</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>14</td>
      <td>70</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>15</td>
      <td>65</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>16</td>
      <td>76</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>17</td>
      <td>73</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>18</td>
      <td>58</td>
      <td>NaN</td>
    </tr>
    <tr>
      <td>19</td>
      <td>81</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 길이가 달라서 결측이 발생할 수 있으므로,
# 일반적으로는 결측을 제거한 뒤 각 컬럼을 group_A와 group_B에 저장해야 한다.
group_A = df2['A반'].dropna().values
group_B = df2['B반'].dropna().values
```


```python
# kstest를 이용한 정규성 검정: 모두 정규 분포를 띔을 확인
from scipy.stats import *

print(kstest(group_A, 'norm'))
print(kstest(group_B, 'norm'))
```

    KstestResult(statistic=1.0, pvalue=0.0)
    KstestResult(statistic=1.0, pvalue=0.0)
    


```python
# 등분산 검정
levene(group_A, group_B) 
# pvalue가 0.05미만이 아니므로, 등분산을 띈다고 볼 수 있음
```




    LeveneResult(statistic=2.033067087400979, pvalue=0.164964086222101)




```python
import numpy as np
np.var(group_A, None, ddof=1)
```




    32.26052631578948




```python
# A와 B간에는 차이가 존재함을 확인 
print(ttest_ind(group_A, group_B, equal_var = True)) 
```

    Ttest_indResult(statistic=2.5128526794964134, pvalue=0.01801095352893767)
    
