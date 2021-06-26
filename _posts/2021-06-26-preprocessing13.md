---
layout: post
title: "쌍체 표본 t검정 기초개념"
tags: [데이터전처리, 수학기초]
comments: true
---

.

Data_Preprocessing_TIL(20210626)

[학습자료]

패스트캠퍼스 온라인 강의 "파이썬을 활용한 데이터 전처리 Level UP 올인원 패키지 Online." 를 공부하고 정리한 내용입니다.

URL : https://fastcampus.co.kr/data_online_preprocess

[학습내용]

- 쌍체 표본 t검정의 목적 : 특정 실험 및 조치 등이 효과가 있는지에 대한 유의성을 검정

일반적으로 어떤 실험을 하기 전과 한 후를 비교했을때 그 차이가 유의한지를 보는 것이다.

예를 들어서 실험전에 n개의 샘플이 있고, 값을 x1 ~ xn까지 있다고 하자. 그리고 실험 후의 결과를 y1 ~ yn까지라고 해보자. 주의해야할 점은 샘플데이터는 실험전과 후의 차이가 있을뿐 데이터는 동일한 것이다. 실험 전후의 차이를 d1 ~ dn까지로 나타낸 것이다. 그래서 이 실험전후의 차이가 0인것을 영가설로 두고, 0이 아닌것을 대립가설로 두고 검정하는 것을 말한다.

![1](https://user-images.githubusercontent.com/41605276/123511529-b8f21280-d6bc-11eb-95ae-ce2e1df52566.PNG)

- 쌍체 표본 t검정을 수행하기 위한 선행조건

실험전값과 실험후의 측정값은 정규분포를 따르지 않아도 무방하나 실험전후 측정값의 차이인 d는 정규성을 띄고 있어야 한다.

- 쌍체 표본 t검정의 통계량

분자는 d의 평균이 들어가고, 분모에는 이 d에 대한 표준편차가 들어간다. d가 크면 클수록 통계량이 커지는 것을 알 수 있다. 표준편차가 작으면 작을수록 또 통계량이 커진다. 

![2](https://user-images.githubusercontent.com/41605276/123511676-af1cdf00-d6bd-11eb-9f94-32d91a100efa.PNG)

- 파이썬을 이용한 쌍체 표본 t검정

1) 정규성 검정(Kolmogorov-Smornov 검정)

코드


```python
scipy.stats.kstest(x,'norn')
```

결과해석

pvalue가 특정수치(0.05) 미만이면 정규성을 따른다고 판단

2) 쌍체표본 t검정

코드


```python
# a,b : 실험전후 결과(주의사항 : 반드시 길이가 같아야 함)
scipy.stats.ttest_rel(a,b)
```

결과해석

pvalue가 특정수치 미만이면 그룹 a와 그룹 b간 차이가 존재한다고 판단(즉, 특정실험의 효과가 있음)

statistics가 양수이면 양의 효과(d>0)가 있다고 판단하며, 음수면 음의효과(d<0)가 있다고 판단

- python을 이용한 실제 실습

다이어트 약이 효과가 있었는지를 검정하는 예시

step 1) 데이터 load


```python
# data 불러오기
import os
import pandas as pd
# pwd 시 경로 : /c/Users/user/Desktop/aa/2. 탐색적 데이터 분석/데이터
os.chdir(r"C:/Users/user/Desktop/aa/2. 탐색적 데이터 분석/데이터/")

df = pd.read_csv("다이어트약_효과검증.csv")
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
      <th>다이어트전(kg)</th>
      <th>다이어트후(kg)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>87.41</td>
      <td>88.30</td>
    </tr>
    <tr>
      <td>1</td>
      <td>81.05</td>
      <td>76.21</td>
    </tr>
    <tr>
      <td>2</td>
      <td>60.72</td>
      <td>53.34</td>
    </tr>
    <tr>
      <td>3</td>
      <td>81.02</td>
      <td>78.21</td>
    </tr>
    <tr>
      <td>4</td>
      <td>75.75</td>
      <td>76.74</td>
    </tr>
    <tr>
      <td>5</td>
      <td>85.48</td>
      <td>74.74</td>
    </tr>
    <tr>
      <td>6</td>
      <td>62.11</td>
      <td>59.00</td>
    </tr>
    <tr>
      <td>7</td>
      <td>56.56</td>
      <td>49.85</td>
    </tr>
    <tr>
      <td>8</td>
      <td>85.11</td>
      <td>79.79</td>
    </tr>
    <tr>
      <td>9</td>
      <td>80.67</td>
      <td>79.38</td>
    </tr>
    <tr>
      <td>10</td>
      <td>66.65</td>
      <td>65.53</td>
    </tr>
    <tr>
      <td>11</td>
      <td>59.29</td>
      <td>53.80</td>
    </tr>
    <tr>
      <td>12</td>
      <td>77.08</td>
      <td>68.51</td>
    </tr>
    <tr>
      <td>13</td>
      <td>80.08</td>
      <td>78.30</td>
    </tr>
    <tr>
      <td>14</td>
      <td>82.89</td>
      <td>82.26</td>
    </tr>
    <tr>
      <td>15</td>
      <td>84.45</td>
      <td>83.48</td>
    </tr>
    <tr>
      <td>16</td>
      <td>88.72</td>
      <td>86.44</td>
    </tr>
    <tr>
      <td>17</td>
      <td>67.97</td>
      <td>62.91</td>
    </tr>
    <tr>
      <td>18</td>
      <td>73.23</td>
      <td>74.53</td>
    </tr>
    <tr>
      <td>19</td>
      <td>75.32</td>
      <td>71.58</td>
    </tr>
    <tr>
      <td>20</td>
      <td>58.96</td>
      <td>51.54</td>
    </tr>
    <tr>
      <td>21</td>
      <td>55.89</td>
      <td>52.07</td>
    </tr>
    <tr>
      <td>22</td>
      <td>89.54</td>
      <td>84.32</td>
    </tr>
    <tr>
      <td>23</td>
      <td>73.34</td>
      <td>64.47</td>
    </tr>
    <tr>
      <td>24</td>
      <td>69.92</td>
      <td>67.26</td>
    </tr>
    <tr>
      <td>25</td>
      <td>76.70</td>
      <td>69.76</td>
    </tr>
    <tr>
      <td>26</td>
      <td>72.76</td>
      <td>68.79</td>
    </tr>
    <tr>
      <td>27</td>
      <td>84.80</td>
      <td>83.77</td>
    </tr>
    <tr>
      <td>28</td>
      <td>81.31</td>
      <td>78.05</td>
    </tr>
    <tr>
      <td>29</td>
      <td>77.63</td>
      <td>74.82</td>
    </tr>
    <tr>
      <td>30</td>
      <td>57.19</td>
      <td>52.65</td>
    </tr>
    <tr>
      <td>31</td>
      <td>89.08</td>
      <td>88.31</td>
    </tr>
    <tr>
      <td>32</td>
      <td>68.69</td>
      <td>65.70</td>
    </tr>
    <tr>
      <td>33</td>
      <td>66.58</td>
      <td>64.10</td>
    </tr>
    <tr>
      <td>34</td>
      <td>61.49</td>
      <td>60.53</td>
    </tr>
    <tr>
      <td>35</td>
      <td>67.12</td>
      <td>62.10</td>
    </tr>
    <tr>
      <td>36</td>
      <td>69.87</td>
      <td>60.27</td>
    </tr>
    <tr>
      <td>37</td>
      <td>82.98</td>
      <td>82.20</td>
    </tr>
    <tr>
      <td>38</td>
      <td>72.75</td>
      <td>61.21</td>
    </tr>
    <tr>
      <td>39</td>
      <td>79.50</td>
      <td>77.63</td>
    </tr>
    <tr>
      <td>40</td>
      <td>57.93</td>
      <td>54.11</td>
    </tr>
    <tr>
      <td>41</td>
      <td>65.19</td>
      <td>56.46</td>
    </tr>
    <tr>
      <td>42</td>
      <td>70.02</td>
      <td>67.07</td>
    </tr>
    <tr>
      <td>43</td>
      <td>83.36</td>
      <td>76.00</td>
    </tr>
    <tr>
      <td>44</td>
      <td>78.59</td>
      <td>74.65</td>
    </tr>
    <tr>
      <td>45</td>
      <td>88.18</td>
      <td>84.15</td>
    </tr>
    <tr>
      <td>46</td>
      <td>59.97</td>
      <td>51.40</td>
    </tr>
    <tr>
      <td>47</td>
      <td>82.43</td>
      <td>75.39</td>
    </tr>
    <tr>
      <td>48</td>
      <td>58.08</td>
      <td>49.02</td>
    </tr>
    <tr>
      <td>49</td>
      <td>58.60</td>
      <td>52.48</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.columns
```




    Index(['다이어트전(kg)', '다이어트후(kg)'], dtype='object')




```python
before = df['다이어트전(kg)']
after = df['다이어트후(kg)']
```

step 2) 정규성 검정


```python
# 차이 d(after-before)에 대한 정규성 검정
from scipy.stats import *
kstest(after - before, 'norm')
```




    KstestResult(statistic=0.7424620196514834, pvalue=7.04108433205159e-29)



step 3) 쌍체표본 t검정


```python
ttest_rel(before, after) # 확실한 양의 효과가 있음을 확인 
```




    Ttest_relResult(statistic=9.70596986565785, pvalue=5.370404098342324e-13)


